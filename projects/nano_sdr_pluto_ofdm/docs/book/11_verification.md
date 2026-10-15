# Глава 11. Верификация

## 11.1. Философия: «золотые» модели и bit-exact

Вся проверка построена на одном принципе (`STATUS.md`, «Запуск тестів»; `python/*_ref.py`): для **каждого** RTL-блока в `python/` есть **бит-точная** целочисленная модель. Тестбенч подаёт
в блок вектор, полученный из модели, и сравнивает выход RTL с ожидаемым вектором **бит-в-бит**. Модель и RTL проходят одинаковые сдвиги, округления и насыщения (глава 6), поэтому «почти правильный» результат не
считается: любое расхождение — ошибка.

Три уровня модели:

1. **float-референс** (`rx_ref.py`, `phy2_ref.py`, `ldpc_ref.py`) — проверка алгоритма (как должно работать в идеале);
2. **fixed-point модель** (`rx_fixed_ref.py`, `phy2_fixed_ref.py`, `sync_ref.py`, `rx_blocks_ref.py`, `ldpc_fixed_ref.py`, `rxenh_fixed_ref.py`, `fft_ref.py`, `qam_ref.py`, `tx_ref.py`, `ofdm_ref.py`, `hdr_ref.py`, `interleaver_ref.py`, `scrambler_ref.py`, `crc_ref.py`) — golden для RTL;
3. **RTL-in-the-loop** (`phy_sim/`) — сравнение RTL с моделью на реальных (зашумлённых, с каналом) сигналах.

## 11.2. Как запускать

### 11.2.1. Один тестбенч

```bash
sim/run_tb.sh tb_phy_qam_demapper                    # без параметров
GENERIC=ORDER=16 sim/run_tb.sh tb_phy_qam_demapper   # с generic'ами (через -generic_top)
```

**[Код]** `sim/run_tb.sh`: компилирует (`xvlog -sv`) пакеты (`phy_pkg`, `phy_ldpc_pkg`, `phy_soft_pkg`, **первыми**), затем все `*.sv`/`*.v` из `rtl/common`, `rtl/tx`, `rtl/rx` и тестбенч `tb/*/$TB.sv`; копирует векторы
`sim/vec` в `sim/work/vec`; `xelab --timescale 1ns/1ps` и `xsim -runall`. Успех — строка `TEST PASSED` в выводе (скрипт возвращает код 0 только в этом случае).

* Путь к Vivado задан **жёстко**: `V=/c/AMDDesignTools/2025.2/Vivado/bin` (Git Bash/WSL-стиль на Windows). На другой машине его нужно поправить (§12.1).
* Generic'и передаются через файл `xelab.args` (потому что `.bat`-обёртки Vivado разбивают аргументы по `=`; комментарий в скрипте).
* Если `sim/vec` отсутствует (в репозитории он не лежит), сначала нужно сгенерировать векторы: `python python/generate_vectors.py`.

### 11.2.2. Полная регрессия

```bash
sim/run_regression.sh      # генерирует векторы и проганяет все TB
```

Скрипт (`sim/run_regression.sh`): `python python/generate_vectors.py`, затем для каждого сценария вызывает `run name [generics]`, печатает `PASS`/`FAIL`, в конце возвращает 0 при отсутствии ошибок. Всего в скрипте 54 строки вызова/проверки
(в том числе сценарии с разными generic'ами). **[Док]** `STATUS.md` (v0.8.0): «регресія 85 з 85». Последняя строка — Python-проверка RTL против модели через `phy_sim/experiments/rtl_runs_rxenh.py --check` (`rxenh_rtl_check`, UA=1 CA=1).

> Число 85 — заявление авторов; моя ревизия скрипта показывает 54 *строки* запуска, поскольку часть строк — циклы (`for c in ...; do run ...`), и в сумме экземпляров больше. Точный подсчёт запусков не выполнялся.

## 11.3. Соглашения тестбенчей

Из `STATUS.md` («Конвенції»): стимул — `{last, first, valid, payload}` на такт со **случайными паузами** (на пустых тактах — «мусор»); латентность проверяется точно (`out_valid == in_valid`, задержанный на `LATENCY`); LLR > 0 ⇒ бит 0.
Рассмотрим типичный тест на примере `tb/rx/tb_phy_crc.sv` и системного `tb_phy_rx_top.sv`: стимул читается из `.mem`-файлов через `$readmemh`, ожидаемое — из других `.mem`; тестбенч считает ошибки и печатает `TEST PASSED`/`TEST FAILED`.

## 11.4. Каталог тестбенчей

### 11.4.1. TX (`tb/tx/`)

| Тестбенч | Проверяет | Golden |
|---|---|---|
| `tb_phy_scrambler.sv` | `phy_scrambler`: номинальные, граничные и случайные кадры | `scrambler_ref.py` |
| `tb_phy_qam_mapper.sv` | `phy_qam_mapper` (ORDER 4/16/64) | `qam_ref.py` |
| `tb_phy_interleaver.sv` | `phy_interleaver` (интерливинг и деинтерливинг, back-pressure, разные WORD_W/ROT_UNIT/ROWS/COLS) | `interleaver_ref.py` |
| `tb_phy_ldpc_enc.sv` | `phy_ldpc_enc` (MODE 0/1, 4 кодовых слова = 2 символа) | `ldpc_ref.py::encode` |
| `tb_phy_ofdm_mapper.sv` | `phy_ofdm_mapper` с флагами слотов пилотов | `ofdm_ref.py::map_bins` |
| `tb_phy_pilot_insert.sv` | `phy_pilot_insert`, паузы с мусором | `ofdm_ref.py::insert_pilots` |
| `tb_phy_preamble_gen.sv` | `phy_preamble_gen` (sync и LTS) | `ofdm_ref.py::preamble` |
| `tb_phy_ifft_2048.sv` | `phy_ifft_2048` (N_LOG = 4/4/11; маски) | `tx_ref.py::ifft_raw` |
| `tb_phy_tx_scaler.sv` | `phy_tx_scaler` (усиления 16384, 8192, 65535, 0, 24000) | `tx_ref.py::tx_scale` |
| `tb_phy_cp_insert.sv` | `phy_cp_insert` (три конфигурации) | `tx_ref.py::cp_insert` |

### 11.4.2. RX (`tb/rx/`)

| Тестбенч | Проверяет | Golden |
|---|---|---|
| `tb_phy_input_scale.sv` | сдвиги 0, +3, −3, +7, −8 | `rx_blocks_ref.py::input_scale` |
| `tb_phy_dc_remove.sv` | K = 10 и K = 4 | `rx_blocks_ref.py::dc_remove` |
| `tb_phy_sync_sc.sv` | 3 вектора: событие, без события, шум; паузы, сброс | `sync_ref.py` |
| `tb_phy_cordic.sv` | `phy_cordic` | `sync_ref.py::cordic_vec` |
| `tb_phy_cfo_coarse.sv` | инкремент NCO по $P$ | `sync_ref.py::cfo_inc` |
| `tb_phy_nco_mixer.sv` | 5 приращений фазы; `ph_clr` | `rx_blocks_ref.py::nco_mix` |
| `tb_phy_rx_window.sv` | вырезание окон по индексам (значение отсчёта кодирует индекс); `N=16` и `N=2048` | собственная модель в TB |
| `tb_phy_rx_fft.sv` | БПФ + реордер (N_LOG 4/4/11) | `rx_fixed_ref.py::rx_fft` |
| `tb_phy_fft_core.sv` | `phy_fft_core` (N_LOG 4, 6, 11; маски) | `fft_ref.py` |
| `tb_phy_bin_select.sv` | выбор бинов, флаги | `rx_fixed_ref.py::select_active` |
| `tb_phy_channel_estimator.sv` | веса и `tau_q8` (SM = 0/1) — **также покрывает `phy_g_smooth` и `phy_tau_est`** | `rx_fixed_ref.py::chest`, `phy2_fixed_ref.py::timing_est` |
| `tb_phy_equalizer.sv` | `phy_equalizer` (RAM весов смоделирована в TB) | `rx_fixed_ref.py::equalize` |
| `tb_phy_phase_tracker.sv` | SLOPE 0/1; бины и углы | `rx_fixed_ref.py::cpe_track`, `cpe_sfo_track` |
| `tb_phy_noise_est.sv` | сумма энергии guard-бинов и лог-код | `phy2_fixed_ref.py::noise_code` |
| `tb_phy_mmse_post.sv` | два прогона на одной модели памяти весов | `phy2_fixed_ref.py::post_engine` |
| `tb_phy_llr_demap.sv` | QP 0/1, UA, 3 символа × 1100 бинов | `phy2_fixed_ref.py::demap_soft` / `rxenh_fixed_ref.py::demap_soft_ua` |
| `tb_phy_hdr.sv` | `phy_hdr_gen` (3 заголовка, 1100 бинов) и `phy_hdr_dec` (чистый, шумный, чистый шум) | `hdr_ref.py` |
| `tb_phy_ldpc_dec.sv` | MODE 0/1, POST 0/1 | `ldpc_fixed_ref.py` |
| `tb_phy_qam_demapper.sv` | ORDER 4/16/64 | `qam_ref.py` |
| `tb_phy_rx_decode.sv` | uncoded бэкенд | `rx_fixed_ref.py::decode_symbols` |
| `tb_phy_rx_decode_ldpc.sv` | кодированный бэкенд, MODE 0/1 | цепочка моделей `phy2_ref/ldpc_fixed_ref` |
| `tb_phy_rx_pkt_out.sv` | пакеты 0, 1, 7, 8, 9, 1100 байт; V3 = 0/1 | модель в TB |
| `tb_phy_crc.sv` | `phy_crc`: хорошие кадры → `ok=1`, с 1 ошибочным битом → `ok=0` | `crc_ref.py` |

### 11.4.3. Системные (`tb/system/`)

| Тестбенч | Что проверяет |
|---|---|
| `tb_phy_tx_top.sv` | весь TX против `tx_ref.py::tx_frame`: бит-в-бит IQ, непрерывность, `pkt_done`, `overflow`; два пакета подряд (1100/700 байт или 900/500, 270/200 в кодированном режиме); DAC — строб через такт |
| `tb_phy_rx_top.sv` | весь RX: поток отсчётов из Python-TX+канал: шум-преамбула, пакет A (35 дБ, CFO +9 кГц), пакет B (30 дБ, CFO −7 кГц, 2 луча); проверяет заголовок записи, `flags == 0`, `n_best` и NCO-инкремент bit-exact против `sync_ref.py`, полезные байты, `tlast`, 2 пакета, нет watchdog. Режимы MODE = 0/1/2 (2 — смена режима между пакетами) |
| `tb_phy_tx_axis_top.sv` | обёртка TX: DMA-кадрирование, пауза, AXI-Lite, весь TX против `tx_ref.py` |
| `tb_phy_rx_axis_top.sv` | обёртка RX на том же двухпакетном потоке + регистры |
| `tb_phy_regs_axil.sv` | `phy_regs_axil` на двух несвязанных тактах (100 МГц и ≈61,44 МГц) |
| `tb_rtl_tx_file.sv`, `tb_rtl_rx_file.sv` | **файловые** TB для Python-фреймворка (`phy_sim`): читают `rtl_rx_in.mem`, пишут `rtl_rx_out.txt` (`META`, `B`, `E`, `C`, `Q`, `W`, `P`, `X`, `Y`, `S`); generics `CODED`, `MMSE`, `MAX_ITER`, `FT_EN`, `TAU_TGT`, `HDR_EN`, `MODE_FB`, `NS`, `NSYMS`, `RMIN`, `GAIN_SH`, `CLKS_PER_SAMPLE`, `UA`, `CA`, `DEBUG`… |

## 11.5. `phy_sim`: сценарии и RTL-in-the-loop

Фреймворк `phy_sim/` (README): один сценарий на YAML (`phy_sim/scenarios/*.yaml`) исполняется на любой комбинации «TX backend → канал → RX backend»:

| Backend | Что это |
|---|---|
| `python` TX | бит-точная модель TX (`python/tx_ref.py`) |
| `python` RX `mode: fixed` | бит-точная модель RX |
| `python` RX `mode: float` | float-алгоритм (`rx_ref`) |
| `rtl` TX/RX | `phy_tx_top`/`phy_rx_top` в xsim через `tb_rtl_{tx,rx}_file.sv` |
| `pluto` | заготовка на pyadi-iio — **не проверялась на железе** |

Канал (`phy_sim/phy_sim/channel/`): AWGN, CFO, задержка/дробная задержка/SFO, многолучёвость, Rayleigh/Rician+Doppler, фазовый шум, IQ-дисбаланс, DC-смещение, квантование/клиппинг АЦП, нелинейность (Rapp PA), интерференция.
Анализ (`phy_sim/analysis/`): BER/SER/PER, EVM/MER, PAPR, занятая полоса, SNR на поднесущую, CPE, ICI, метрики Шмидля–Кокса, ошибки CFO/тайминга, MSE оценки канала, латентность/пропускная способность. Отчёты и развёртки: `report/`, `core/sweep.py`.

Тесты `phy_sim`: `python -m phy_sim regression` (11 сценариев с критериями pass/fail), `python -m phy_sim unit` (RTL-регрессия блоков/системы), `python -m pytest -q` (`phy_sim/tests/`: `unit/test_channel.py`, `unit/test_sweep_replay.py`, `phy/test_python_phy.py`, `regression/test_scenarios.py`;
RTL-тесты помечены `rtl`).

Исследования (`phy_sim/experiments/`): `range_study`, `dualmode_study`, `cp_study`, `header_study`, `rxenh_study`, `rxenh_diag`, `rxenh_det`, `phy_compare`, `rtl_runs*`, `collect_resources`, `build_*_report` + шаблоны отчётов (HTML). Они генерируют те числа SNR/PER, которые приведены в `STATUS.md`.

## 11.6. Синтез и ресурсы

`synth/synth_est.tcl` (запуск `vivado -mode batch -source synth_est.tcl -tclargs <top> <part> <outdir> [GENERIC:value ...]`): out-of-context синтез `phy_rx_top`/`phy_tx_top` (все `*.sv`/`*.v` из `rtl/common`, `tx`, `rx`, кроме обёрток `*_axis_top.v` и `phy_regs_axil.v`);
период 8,000 нс; отчёты `*_util.rpt`, `*_util_hier.rpt`, `*_timing.rpt` («post-synthesis, un-routed, indicative only»). Generic'и через двоеточие (`CA:1 UA_HW:0`), потому что `.bat` разбивает по `=`.
`synth/ff_breakdown.tcl` — разбор числа триггеров (файл не разбирался подробно; назначение — по имени).

## 11.7. Что покрыто и чего не хватает

**Хорошо покрыто (по коду):** все блоки TX и RX имеют бит-точные TB; системные TB проверяют цепочки целиком; режимы (QPSK/16-QAM, SM, UA, POST, SLOPE) прогоняются по generic'ам.

**Косвенное покрытие** (нет отдельного TB, проверяются в составе других):

| Модуль | Где проверяется |
|---|---|
| `phy_g_smooth`, `phy_tau_est` | `tb_phy_channel_estimator` (SM 0/1, проверка `tau_q8`) |
| `phy_rssi_code` | `tb_phy_rx_top`: код RSSI из beat 2 записи сравнивается с ожидаемым (`tb_phy_rx_top.sv:90`) |
| `phy_ca_refine`, `phy_w_core` | `phy_sim/experiments/rtl_runs_rxenh.py --check` через `tb_rtl_rx_file` (параметры CA/UA) |
| `phy_dyn_sr`, `phy_lgcode`, `phy_sincos`, `phy_delay_line`, `phy_complex_mult`, `phy_fft_stage` | через `tb_phy_ldpc_dec`, `tb_phy_mmse_post`/`tb_phy_noise_est`, `tb_phy_nco_mixer`, `tb_phy_sync_sc`, `tb_phy_fft_core` |
| `phy_descrambler` | `tb_phy_rx_decode`, `tb_phy_rx_decode_ldpc` |
| `phy_hdr_dec` | `tb_phy_hdr`, `tb_phy_rx_decode_ldpc` |

**Пробелы** (по моему обзору; не утверждения об ошибках):

1. `phy_fifo` в своей шапке ссылается на `tb_phy_fifo`, но такого файла в `tb/` нет — проверяется только косвенно через `tb_phy_tx_top`.
2. `phy_crc` имеет TB, но нигде не используется в тракте (§5.2.1).
3. **Нет проверки на железе** — «на залізі нічого не перевірено» (`STATUS.md`).
4. Нет сценариев с **длинным CFO > ±15 кГц** (целая часть CFO не реализована); в тестах CFO ±7…9 кГц.
5. Случайные тесты (`generate_vectors.py`) детерминированы по seed; нет формального покрытия (coverage), нет формальной верификации.
6. Нет тестов на **длительную** остановку DMA: в `tb_phy_rx_top` `m_axis_ready` случайный (в 70 % тактов равен 1, строка 53), то есть короткий back-pressure проверяется, а длительные остановки (когда следующий пакет уже ждёт commit и должен быть отброшен счётчиком `dropped`) — нет (по обзору тестбенча).
7. Тестбенчи не включены в единую среду (UVM и т. п.); проверка — «TEST PASSED» по строке вывода.
8. `python/check_params.py`, упомянутый в `phy_pkg.sv:2` как проверка синхронности `phy_pkg.sv` и `phy_params.py`, в репозитории **отсутствует**.

## 11.8. Контрольные вопросы

1. Почему тестбенч сравнивает «бит-в-бит», а не «с допуском»?
2. Какие два вида данных использует `tb_phy_rx_top` для стимула, и откуда они берутся?
3. Как проверяется `phy_g_smooth`, если для него нет собственного тестбенча?
