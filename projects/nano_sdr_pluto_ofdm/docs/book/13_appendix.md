# Глава 13. Приложения

## 13.1. Глоссарий (рус/англ)

| Русский | English | Пояснение |
|---|---|---|
| Автоматическая регулировка усиления (АРУ) | AGC, automatic gain control | в проекте — только статический сдвиг `phy_input_scale` (§5.4) |
| АЦП / ЦАП | ADC / DAC | в AD9361 |
| Базовый диапазон (комплексный) | (complex) baseband | §1.4 |
| Бабочка | butterfly | базовая операция БПФ (§3.2) |
| Бин | bin | один выход БПФ |
| Бит-реверсный порядок | bit-reversed order | порядок выхода DIF-БПФ (§3.2.1) |
| Блочная плавающая запятая | block floating point | вес = мантисса + показатель (§6.5) |
| Барабанный сдвигатель | barrel shifter | в LDPC-кодере/декодере (ротатор на 60 бит) |
| Вес эквалайзера | equalizer weight / tap | $w_k=1/H_k$ |
| Детектор пакета | packet detector | `phy_sync_sc` |
| Дескремблер | descrambler | `phy_descrambler` |
| Декодер LDPC | LDPC decoder | `phy_ldpc_dec` |
| Защитный интервал | guard interval | в OFDM — циклический префикс |
| Защитные бины | guard bins | неиспользуемые бины 601…1447, из них 700…1348 — оценка шума |
| Заголовок PHY | PHY header | символ с `MODE_ID`, `nsyms`, CRC-4 (§5.7) |
| Интерливер | interleaver | `phy_interleaver` |
| Кадр | frame | `[sync][LTS][header][data…]` (§2.7) |
| Квазициклический LDPC | QC-LDPC | §5.6.2 |
| Код Грея | Gray code | §1.5.4 |
| Комплексная огибающая | complex envelope | §1.4 |
| Коэффициент повторения (повторное кодирование) | repetition code | заголовок (§5.7) |
| Мягкое решение | soft decision | §1.6 |
| Мгновенный пик-фактор | PAPR | §2.6 |
| Многолучёвость | multipath | §2.1 |
| Насыщение | saturation | §6.4 |
| Несущая | carrier | |
| Ортогональность | orthogonality | §2.2 |
| Остаточная ошибка фазы (общая) | CPE, common phase error | §4.8.1 |
| Отсчёт | sample | |
| Пилот | pilot | известная поднесущая (§2.7.1) |
| Поднесущая | subcarrier | |
| Преамбула | preamble | sync + LTS |
| Приращение фазы NCO | NCO phase increment | `inc`, $2^{32}=2\pi$ |
| Пропускная способность | throughput | |
| Пакетная ошибка (PER) | packet error rate | §1.3 |
| Разброс задержек | delay spread | |
| Сглаживание | smoothing | `phy_g_smooth` (§4.9) |
| Символ OFDM | OFDM symbol | 2192 отсчёта |
| Символ QAM | QAM symbol / constellation point | |
| Синхронизация | synchronization | гл. 4 |
| Скремблер | scrambler | §5.1 |
| Скорость кода | code rate | $R=K/N$ |
| Смещение несущей (частотное) | CFO, carrier frequency offset | §4.3 |
| Смещение частоты дискретизации | SFO, sampling frequency offset | §4.8.2 |
| Соотношение сигнал/шум | SNR | §1.3 |
| Тактовый домен | clock domain | §10.5 |
| Циклический префикс | cyclic prefix (CP) | §2.4 |
| Эквалайзер | equalizer | `phy_equalizer` |
| Эквализация ZF/MMSE | zero-forcing / MMSE equalization | §2.5, §4.6 |
| Разрыв потока (опустошение) | underflow | `underflow` в TX |
| Обучающий символ (длинный) | LTS, long training symbol | §2.7 |
| Корреляция Шмидля–Кокса | Schmidl–Cox correlation | §4.2 |
| Лог-код | log code | $32\log_2x$ (§4.6.2) |
| Мягкий демаппер | soft demapper | `phy_llr_demap` |
| Ранняя остановка | early termination | LDPC (§5.6.4) |
| Слой | layer | блочная строка в layered-декодере |
| Циркулянт | circulant | сдвинутая единичная матрица $Z\times Z$ |
| Ядро (БПФ) | core | `phy_fft_core` |
| Поток «только valid» | valid-only stream | §7.6 |
| Золотая модель | golden model | бит-точная Python-модель (гл. 11) |

## 13.2. Параметры

### 13.2.1. Константы `rtl/common/phy_pkg.sv`

| Имя | Значение | Строка | Комментарий |
|---|---|---|---|
| `SAMPLE_RATE_HZ` | 30 720 000 | 6 | |
| `FFT_SIZE` | 2048 | 7 | |
| `CP_LEN` | 144 | 8 | RTL собран под 144 |
| `SYMBOL_LEN` | 2192 | 9 | |
| `SUBCARRIER_HZ` | 15 000 | 10 | |
| `SYMBOL_RATE_HZ` | 14 014 | 11 | целая часть |
| `NUM_ACTIVE_SC` | 1200 | 14 | |
| `PILOT_SPACING` | 12 | 15 | |
| `NUM_PILOTS` | 100 | 16 | |
| `NUM_DATA_SC` | 1100 | 17 | |
| `NUM_POS_SC` | 600 | 19 | |
| `NEG_FIRST_BIN` | 1448 | 20 | |
| `PILOT_OFFSET` | 6 | 21 | |
| `QAM_ORDER` | 16 | 24 | |
| `BITS_PER_SYM` | 4 | 25 | |
| `BITS_PER_AXIS` | 2 | 26 | |
| `QAM_UNIT` | 4096 | 27 | |
| `IQ_W` | 16 | 28 | |
| `LLR_W` | 8 | 29 | uncoded бэкенд |
| `LLR_SHIFT`, `LLR_GAIN` | 6, 1 | 30-31 | uncoded демаппер |
| `IL_COLS`, `IL_ROWS` | 20, 55 | 34-35 | |
| `PILOT_AMP` | 12288 | 36 | $3\cdot$`QAM_UNIT` |
| `SYNC_AMP` | 16384 | 37 | $4\cdot$`QAM_UNIT` |
| `LDPC_RATE_NUM/DEN` | 5/6 | 40-41 | **в RTL не используются** (по `grep`) |
| `CODED_BITS_PER_OFDM` | 4400 | 42 | |
| `BYTES_PER_OFDM` | 550 | 43 | uncoded |
| `TX_MAX_SYMS` | 8 | 44 | |
| `PILOT_SEED` | `0x7FFF` | 47 | |
| `QPSK_UNIT` | 9216 | 49 | |
| `HDR_AMP` | 9216 | 50 | |
| `HDR_SEED`, `SYNC_SEED`, `LTS_SEED` | `0x1B2D`, `0x1ACE`, `0x2B5D` | 51-53 | |
| `SCR_SEED` | `0x7FFF` | 56 | |
| `CRC32_POLY_REV`, `CRC32_INIT`, `CRC32_RESIDUE` | `0xEDB88320`, `0xFFFFFFFF`, `0xDEBB20E3` | 57-59 | только `phy_crc` |
| `fft_core_latency(n_log)` | функция | 62-67 | |

### 13.2.2. Параметры модулей (generics)

| Модуль | Параметры (по умолчанию) |
|---|---|
| `phy_tx_top` | `MAX_SYMS=8`, `SHIFT_MASK=0x0FF`, `CODED=0` |
| `phy_tx_frame_ctrl` | `NB=3` |
| `phy_cp_insert` | `N_LOG=11`, `CP=CP_LEN`, `BITREV=1`, `NB=3` |
| `phy_ifft_2048` | `N_LOG`, `SHIFT_MASK=0x0FF` |
| `phy_fft_2048` | `N_LOG`, `SHIFT_MASK=0x00F` |
| `phy_fft_core` | `N_LOG=11`, `W=18`, `TWW=16`, `SHIFT_MASK=0`, `IN_W=16` |
| `phy_fft_stage` | `LOGD`, `W=18`, `TWW=16`, `SHIFT` |
| `phy_complex_mult` | `AW=18`, `BW=16`, `FRAC=15` |
| `phy_cordic` | `XW=18`, `NITER=24` |
| `phy_cfo_coarse` | `XW=18`, `NITER=24`, `LOG2L=10` |
| `phy_interleaver` | `WORD_W=4`, `ROT_UNIT=1`, `ROWS=55`, `COLS=20`, `DEINT=0` |
| `phy_qam_mapper` | `ORDER=16`, `UNIT=4096` |
| `phy_qam_demapper` | `ORDER`, `UNIT`, `GAIN=1`, `SHIFT=6` |
| `phy_scrambler` | `SEED=0x7FFF` |
| `phy_tx_scaler` | `GAIN_W=16`, `GAIN_FRAC=14` |
| `phy_ldpc_enc` | `FILL_WORDS=20` |
| `phy_ldpc_dec` | `LW=8`, `MW=7`, `REL_THR=24` |
| `phy_dyn_sr` | `W=8`, `DEPTH=18` |
| `phy_fifo` | `W=8`, `DEPTH=8` |
| `phy_delay_line` | `W=32`, `DEPTH=1024` |
| `phy_lgcode` | `W=42` |
| `phy_input_scale` | `W=16`, `SH_W=4` |
| `phy_dc_remove` | `W=16`, `K=10` |
| `phy_nco_mixer` | `W=16` |
| `phy_sync_sc` | `L=1024`, `QSH=8`, `BOX=128`, `TRACK_LEN=704` |
| `phy_rx_window` | `N=2048`, `SYM=2192` |
| `phy_rx_fft` | `N_LOG`, `SHIFT_MASK=0x00F`, `NB=2` |
| `phy_phase_tracker` | `SLOPE=0`, `SFO_KB=4849` |
| `phy_ca_refine` | `MAX_SYMS=8`, `W0H=216` |
| `phy_rx_pkt_out` | `RAM_BYTES=8*550`, `HDR_V3=0` |
| `phy_rx_top` | `MAX_SYMS=8`, `W0_OFFSET=104`, `WIN_DELAY=704`, `WATCHDOG_BITS=24`, `FFT_MASK=0x00F`, `DC_K=16`, `UA_HW=1`, `CA=0`, `CODED=0` |
| `phy_rx_axis_top` | см. §10.2.1 |
| `phy_tx_axis_top` | `GAIN=16384`, `GAP=16384`, `ENABLE=1`, `MODE=1`, `CODED=1` |
| `phy_regs_axil` | `ID`, `NCFG=4`, `NST=16`, `CFG_INIT` |

### 13.2.3. Сгенерированные константы `phy_soft_pkg.sv`

`LGF = 32`, `LGMB = 5`, `CNU = −304`, `D_MIN = −256`, `D_MAX = 992`, `D_STEP = 2`, `NMU = 625`, `SH_C = 18`, `GUARD_LO = 700`, `GUARD_HI = 1348`, `LLR_MAXV = 31`, плюс таблицы `LGT_ROM[32]`, `EXP2_ROM[32]`, `MU_ROM[625]`, `GLM_ROM[625]`, `GLZ_ROM[625]`.

### 13.2.4. Константы `phy_ldpc_pkg.sv`

`LZ = 60`, `LMB = 18`, `LNB = 36`, `LKB = 30`, `LDMAX = 18`, `LN = 2160`, `LK = 1800`; функции `lmb(cs)`, `lkb(cs)`, `lpa(cs)`, `lmid(cs)`, `ldeg`, `lcol`, `ldelta`, `lshift`, `lcoloff`, `enc_n`, `enc_col`, `enc_sh`.
`lpa`: 13 (cs=0), 8 (cs=1); `lmid`: 9 (cs=0), 3 (cs=1).

## 13.3. Регистры (сводка)

Общая часть (`phy_regs_axil`), базовый адрес в BD **`0x7C440000`** (`STATUS.md`):

| Адрес | Тип | Назначение |
|---|---|---|
| `0x00` | RO | ID: RX `0x4F465258`, TX `0x4F465458` |
| `0x04` | RW | CTRL: `[0]` soft reset, `[1]` clear stats |
| `0x08` | RW | SNAP |
| `0x10 + 4i` | RW | конфигурация (см. ниже) |
| `0x80 + 4i` | RO | статус |

**TX** (`NCFG=3`): `0x10` GAIN, `0x14` GAP_SAMPLES, `0x18` `{MODE[1], ENABLE[0]}`.

**RX** (`NCFG=8`): `0x10` NSYMS, `0x14` RMIN, `0x18` GAIN_SH, `0x1C` `{CA[4], UA[3], MODE[2], HDR_EN[1], MMSE[0]}`, `0x20` MAX_ITER, `0x24` BAD_THR, `0x28` `{SMOOTH[1], FT_EN[0]}`, `0x2C` TAU_TGT.

Статус-слова — §10.1.4 (TX) и §10.2.3 (RX).

## 13.4. Таблица модулей и путей

Пути — от каталога `projects/nano_sdr_pluto_ofdm/`. «Гл.» — где модуль описан.

### 13.4.1. `rtl/tx/`

| Файл | Модуль | Гл. |
|---|---|---|
| `rtl/tx/phy_cp_insert.sv` | `phy_cp_insert` | 3.5, 8.9.3 |
| `rtl/tx/phy_hdr_gen.sv` | `phy_hdr_gen` | 5.7, 8.8.4 |
| `rtl/tx/phy_ifft_2048.sv` | `phy_ifft_2048` | 3.4, 8.9.1 |
| `rtl/tx/phy_interleaver.sv` | `phy_interleaver` | 5.3, 8.6, 9.10.3 |
| `rtl/tx/phy_ofdm_mapper.sv` | `phy_ofdm_mapper` | 2.7, 8.8.1 |
| `rtl/tx/phy_pilot_insert.sv` | `phy_pilot_insert` | 2.7.1, 8.8.2 |
| `rtl/tx/phy_preamble_gen.sv` | `phy_preamble_gen` | 4.2.1, 8.8.3 |
| `rtl/tx/phy_qam_mapper.sv` | `phy_qam_mapper` | 1.5, 8.7 |
| `rtl/tx/phy_scrambler.sv` | `phy_scrambler` | 5.1, 8.4 |
| `rtl/tx/phy_tx_axis_top.v` | `phy_tx_axis_top` | 10.1 |
| `rtl/tx/phy_tx_frame_ctrl.sv` | `phy_tx_frame_ctrl` | 8.3 |
| `rtl/tx/phy_tx_scaler.sv` | `phy_tx_scaler` | 8.9.2 |
| `rtl/tx/phy_tx_top.sv` | `phy_tx_top` | 8.2 |

### 13.4.2. `rtl/rx/`

| Файл | Модуль | Гл. |
|---|---|---|
| `rtl/rx/phy_bin_select.sv` | `phy_bin_select` | 9.7.2 |
| `rtl/rx/phy_ca_refine.sv` | `phy_ca_refine` | 4.10, 9.13 |
| `rtl/rx/phy_cfo_coarse.sv` | `phy_cfo_coarse` | 4.3, 9.5.1 |
| `rtl/rx/phy_channel_estimator.sv` | `phy_channel_estimator` | 4.5, 9.8.2 |
| `rtl/rx/phy_crc.sv` | `phy_crc` | 5.2.1, 9.12 |
| `rtl/rx/phy_dc_remove.sv` | `phy_dc_remove` | 4.3.6, 9.3.2 |
| `rtl/rx/phy_descrambler.sv` | `phy_descrambler` | 5.1.3, 9.10.5 |
| `rtl/rx/phy_equalizer.sv` | `phy_equalizer` | 4.5, 9.9.1 |
| `rtl/rx/phy_fft_2048.sv` | `phy_fft_2048` | 3, 9.7.1 |
| `rtl/rx/phy_g_smooth.sv` | `phy_g_smooth` | 4.9, 9.8.3 |
| `rtl/rx/phy_hdr_dec.sv` | `phy_hdr_dec` | 5.7.3, 9.10.2 |
| `rtl/rx/phy_input_scale.sv` | `phy_input_scale` | 5.4, 9.3.1 |
| `rtl/rx/phy_llr_demap.sv` | `phy_llr_demap` | 4.7, 9.10.1 |
| `rtl/rx/phy_mmse_post.sv` | `phy_mmse_post` | 4.6, 9.8.6 |
| `rtl/rx/phy_nco_mixer.sv` | `phy_nco_mixer` | 4.3.4, 9.5.4 |
| `rtl/rx/phy_noise_est.sv` | `phy_noise_est` | 4.6.1, 9.8.5 |
| `rtl/rx/phy_phase_tracker.sv` | `phy_phase_tracker` | 4.8, 9.9.2 |
| `rtl/rx/phy_qam_demapper.sv` | `phy_qam_demapper` | 1.6, 9.10.4 |
| `rtl/rx/phy_rssi_code.sv` | `phy_rssi_code` | 5.5.2, 9.12 |
| `rtl/rx/phy_rx_axis_top.v` | `phy_rx_axis_top` | 10.2 |
| `rtl/rx/phy_rx_decode.sv` | `phy_rx_decode` | 9.10.4 |
| `rtl/rx/phy_rx_decode_ldpc.sv` | `phy_rx_decode_ldpc` | 9.10.3 |
| `rtl/rx/phy_rx_fft.sv` | `phy_rx_fft` | 9.7.1 |
| `rtl/rx/phy_rx_pkt_out.sv` | `phy_rx_pkt_out` | 9.15, 10.4 |
| `rtl/rx/phy_rx_top.sv` | `phy_rx_top` | 9.2, 9.14 |
| `rtl/rx/phy_rx_window.sv` | `phy_rx_window` | 4.4.1, 9.6 |
| `rtl/rx/phy_sync_sc.sv` | `phy_sync_sc` | 4.2, 9.4 |
| `rtl/rx/phy_tau_est.sv` | `phy_tau_est` | 4.4.3, 9.8.4 |
| `rtl/rx/phy_w_core.sv` | `phy_w_core` | 9.13 |

### 13.4.3. `rtl/common/`

| Файл | Модуль | Гл. |
|---|---|---|
| `rtl/common/phy_complex_mult.sv` | `phy_complex_mult` | 8.9.1 |
| `rtl/common/phy_cordic.sv` | `phy_cordic` | 4.3.3, 9.5.2 |
| `rtl/common/phy_delay_line.sv` | `phy_delay_line` | 9.12 |
| `rtl/common/phy_dyn_sr.sv` | `phy_dyn_sr` | 9.11.1 |
| `rtl/common/phy_fft_core.sv` | `phy_fft_core` | 3.3, 8.9.1 |
| `rtl/common/phy_fft_stage.sv` | `phy_fft_stage` | 3.3.1, 8.9.1 |
| `rtl/common/phy_fifo.sv` | `phy_fifo` | 8.10 |
| `rtl/common/phy_ldpc_dec.sv` | `phy_ldpc_dec` | 5.6, 9.11 |
| `rtl/common/phy_ldpc_enc.sv` | `phy_ldpc_enc` | 5.6.3, 8.5 |
| `rtl/common/phy_ldpc_pkg.sv` | пакет `phy_ldpc_pkg` | 5.6.2, 13.2.4 |
| `rtl/common/phy_lgcode.sv` | `phy_lgcode` | 4.6.2, 5.5.1 |
| `rtl/common/phy_pkg.sv` | пакет `phy_pkg` | 2.3, 13.2.1 |
| `rtl/common/phy_regs_axil.v` | `phy_regs_axil` | 10.3 |
| `rtl/common/phy_sincos.sv` | `phy_sincos` | 4.3.4, 9.5.4 |
| `rtl/common/phy_soft_pkg.sv` | пакет `phy_soft_pkg` | 4.6, 13.2.3 |

## 13.5. Таблица покрытия «файл → глава»

Составлена прохождением по всем файлам `rtl/rx/`, `rtl/tx/` и `rtl/common/` (скрипт проверки и результат — §13.7). Каждый из 57 файлов упомянут и в таблице 13.4, и в тексте глав 8-10 (колонка «Гл.» выше). Сводка по главам:

| Глава | Файлы |
|---|---|
| 8 (TX) | `phy_tx_top`, `phy_tx_frame_ctrl`, `phy_scrambler`, `phy_interleaver`, `phy_qam_mapper`, `phy_hdr_gen`, `phy_ofdm_mapper`, `phy_pilot_insert`, `phy_preamble_gen`, `phy_ifft_2048`, `phy_tx_scaler`, `phy_cp_insert`, `phy_fifo`, `phy_ldpc_enc`, `phy_fft_core`, `phy_fft_stage`, `phy_complex_mult` |
| 9 (RX) | `phy_rx_top` и все 28 остальных файлов `rtl/rx/` (кроме `phy_rx_axis_top.v`), `phy_ldpc_dec`, `phy_dyn_sr`, `phy_cordic`, `phy_sincos`, `phy_delay_line`, `phy_lgcode` |
| 10 | `phy_tx_axis_top.v`, `phy_rx_axis_top.v`, `phy_regs_axil.v`, `phy_rx_pkt_out` (формат) |
| 13 | `phy_pkg`, `phy_soft_pkg`, `phy_ldpc_pkg` (константы) |

## 13.6. Литература и ссылки

**Общая теория связи и OFDM**

1. J. G. Proakis, M. Salehi. *Digital Communications*. McGraw-Hill.
2. D. Tse, P. Viswanath. *Fundamentals of Wireless Communication*. Cambridge University Press, 2005.
3. R. van Nee, R. Prasad. *OFDM for Wireless Multimedia Communications*. Artech House, 2000.
4. R. G. Lyons. *Understanding Digital Signal Processing*. Prentice Hall.
5. A. V. Oppenheim, R. W. Schafer. *Discrete-Time Signal Processing*. Prentice Hall.

**Синхронизация и канал**

6. T. M. Schmidl, D. C. Cox. «Robust Frequency and Timing Synchronization for OFDM». *IEEE Trans. Commun.*, vol. 45, no. 12, 1997, pp. 1613–1621.
7. J.-J. van de Beek, M. Sandell, P. O. Börjesson. «ML Estimation of Time and Frequency Offset in OFDM Systems». *IEEE Trans. Signal Process.*, vol. 45, no. 7, 1997.

**БПФ и CORDIC**

8. J. W. Cooley, J. W. Tukey. «An Algorithm for the Machine Calculation of Complex Fourier Series». *Math. Comput.*, vol. 19, 1965.
9. S. He, M. Torkelson. «A New Approach to Pipeline FFT Processor». *Proc. IPPS*, 1996 (SDF/radix-2² архитектуры).
10. J. E. Volder. «The CORDIC Trigonometric Computing Technique». *IRE Trans. Electronic Computers*, EC-8, 1959.

**Кодирование (LDPC)**

11. R. G. Gallager. «Low-Density Parity-Check Codes». *IRE Trans. Inf. Theory*, vol. 8, no. 1, 1962.
12. T. Richardson, R. Urbanke. *Modern Coding Theory*. Cambridge University Press, 2008.
13. D. E. Hocevar. «A Reduced Complexity Decoder Architecture via Layered Decoding of LDPC Codes». *Proc. IEEE SiPS*, 2004.
14. J. Chen, M. P. C. Fossorier. «Near Optimum Universal Belief Propagation Based Decoding of Low-Density Parity Check Codes». *IEEE Trans. Commun.*, 2002 (normalized min-sum).
15. IEEE Std 802.11n-2009 — структура QC-LDPC с двойной диагональю (на которую ссылается `python/ldpc_ref.py`).

**Аппаратура и инструменты**

16. Analog Devices. *AD9361 Reference Manual* (UG-570).
17. AMD/Xilinx. *Vivado Design Suite User Guide: Synthesis* (UG901) — «dynamic shift register» (на него ссылается `phy_dyn_sr.sv`).
18. F. Gray. «Pulse code communication». U.S. Patent 2 632 058, 1953 (код Грея).

> Номера страниц и редакции приведены по памяти автора книги там, где они указаны; сверяйте с первоисточниками.

**Внутренние документы проекта**

* `STATUS.md` — история версий, результаты, ограничения.
* `ARCHITECTURE_DUALMODE.md` — двухрежимный PHY.
* `phy_sim/README.md` — фреймворк.
* `sw/plutolink/README.md` — программа для плат.
* Упомянутые в `STATUS.md` ТЗ (`../nano_sdr_pluto/TestTask*.md`) в этот каталог не входят.

## 13.7. Результаты самопроверки

Проверка выполнена Python-скриптом (одноразовый, не входит в репозиторий) над текстами глав `docs/book/*.md` и деревом проекта:

| Проверка | Результат |
|---|---|
| Все 57 файлов `rtl/rx` (29), `rtl/tx` (13), `rtl/common` (15) упомянуты в тексте (по имени модуля/файла) | да, 57 из 57; у каждого ≥ 2 упоминаний |
| Все пути вида `` `rtl/...` ``, `` `tb/...` ``, `` `python/...` ``, `` `sim/...` ``, `` `synth/...` ``, `` `phy_sim/...` ``, `` `sw/...` ``, `` `fw/...` `` существуют | да, кроме трёх **намеренных** случаев: `python/check_params.py` (упомянут в `phy_pkg.sv:2`, файла нет — см. вопрос 5), `sim/vec` и `sim/work/vec` (создаются `generate_vectors.py`/`run_tb.sh`, в репозитории не лежат) |
| Все голые имена `phy_*.sv/.v`, `tb_*.sv` и `*.py` существуют | да (шаблон `*_ref.py` и `check_params.py` — не имена файлов / известное отсутствие) |
| Все ссылки `§N.M` указывают на существующие подразделы | да (267 заголовков) |
| Константы таблицы 13.2.1 совпадают со значениями `rtl/common/phy_pkg.sv` | да (проверено скриптом для всех целочисленных литералов) |
| Номера строк | выборочно (≈25 ссылок на `phy_rx_top`, `phy_tx_top`, `phy_sync_sc`, `phy_channel_estimator`, `phy_mmse_post`, `phy_phase_tracker`, `phy_rx_window`, `phy_nco_mixer`, `phy_cfo_coarse`, `phy_tau_est`, `phy_llr_demap`, `phy_hdr_gen`, `phy_ldpc_dec`, `phy_rx_pkt_out`, обёрток) сверены вручную с `cat -n`; расхождений не найдено; полный автоматический контроль строк не выполнялся |
| Формулы и числа | выборочно проверены арифметикой: полоса 18 МГц, 14014 симв/с, 15,14 и 50,45 Мбит/с, мощность QPSK/16-QAM (1,0125), $\log_2(0{,}9/649)\cdot32\approx-304$, $327\approx32\log_2 1200$, rms ОБПФ ≈ 1750, `MU_ROM[0]=255`, `T=μ/8`, константа $3y_1$ из $A=3\cdot2^{12}$, $1200 = 1024+128+32+16$ |

## 13.8. Открытые вопросы к автору репозитория

Вопросы, на которые не удалось ответить по коду. Нумерация сквозная; «Где» — файл или раздел книги, где вопрос возник.

### A. Область и сборка

1. **FSK.** В задании для этой книги упомянут FSK, но в `rtl/rx`, `rtl/tx`, `tb`, `python` его нет; рядом лежит отдельный проект `nano_sdr_pluto_gfsk_demod`. Нужно ли описывать его в этой же документации? *(§0.1.1.)*
2. **Сборочные проекты.** `nano_sdr_pluto_ofdm_rx/`, `nano_sdr_pluto_ofdm_tx/` (Vivado, XDC, block design) лежат вне этого каталога. Где они и какие там ограничения по тактам (`create_clock`), false-path, расположение `axi_dmac`? *(§7.1, §10.5.)*
3. **Частота `l_clk`.** Какая она на самом деле — 61,44 МГц (2 такта на отсчёт, как в TB) или 122,88 МГц? В `STATUS.md` — «невідомо до першого запуску»; `synth_est.tcl` использует 8 нс. От этого зависит, укладывается ли LDPC в период символа. *(§7.7.)*
4. **Жёсткий путь к Vivado** в `sim/run_tb.sh` (`/c/AMDDesignTools/2025.2/Vivado/bin`): предполагается ли параметризация (переменной окружения)? *(§11.2.)*
5. `python/check_params.py` упомянут в `phy_pkg.sv:2` («checked by»), но файла нет. Он был удалён, не добавлялся или проверка живёт в другом месте? *(§11.7.)*

### B. Синхронизация

6. **Целая часть CFO.** В RTL не реализована (README `plutolink`: «the integer-CFO stage is not implemented»), хотя в float-модели `STATUS.md` её упоминает. Предельное CFO ±15 кГц — осознанное ограничение? *(§4.3.5.)*
7. **`cfg_rmin` и CORDIC.** `phy_cfo_coarse` требует $|P|\ge2^{16}$, что «гарантируется порогом `rmin ≥ 2^18`»; но `RMIN` — регистр 0x14, который можно записать любым значением. Какая защита от `rmin < 2^18`? *(§4.3.3.)*
8. **Константы тайминга.** Откуда `W0_OFFSET = 104` (мы реконструировали: $8+144-48$), `TRACK_LEN = 704`, и почему `TAU_TGT = 56` в `phy_rx_axis_top.v`, а в комментарии `phy_rx_top.sv:43` — «~54»? *(§4.4.3, §10.2.1.)*
9. `W0_OFFSET`, `WIN_DELAY` соответствуют длине CP = 144; как они должны меняться при `PHY_CP_LEN` 256/384 (Python-эксперимент)? В RTL — нет параметризации? *(§2.4.3.)*

### C. Канал и мягкие решения

10. **Множитель 0,9** в оценке шума $\nu=0{,}9\,S/649$ (`CNU = −304`): откуда (калибровка, потеря окна БПФ, эмпирика)? *(§4.6.1.)*
11. **Пороги `D_MIN/D_MAX/D_STEP`**, `SH_C = 18`, `REL_THR = 24`, `W0H = 216`, `SFO_KB = 4849`: как выбирались (в коде даны значения, но не обоснование)? *(§4.7, §9.11.1, §9.13.)*
12. **Вращение нечётных слов в интерливере** (`ROT_UNIT`): зачем (в комментариях только механика)? *(§5.3.3.)*
13. **Согласованность комментариев:** сглаживание в `phy_channel_estimator.sv:10` заявляет «noise drops by 9,5 dB» (≈9 бин), `phy_g_smooth.sv:3` — 4,8 дБ (3 бин), в `rc_of` комментарий «never used with S = 4 (count ≥ 5)», а `S = 1`. Какая версия актуальна? *(§4.9.)*

### D. Декодер и пакеты

14. **Латентность LDPC.** Шапка `phy_ldpc_dec.sv`: ≈ 260 тактов/итерацию; `STATUS.md` v0.7.0: ≈460 (1/2) и ≈300 (5/6) — до оптимизации v0.8.0? Какие числа актуальны? *(§9.11.1.)*
15. **Максимальное число символов.** TX принимает до 4400 байт (`MAX_SYMS × 550`), то есть до 10 символов MAX RATE (450 Б) и до 33 символов MAX RANGE (135 Б), а RX рассчитан на `MAX_SYMS = 8` (`cfg_nsyms` 1…8; `plutolink` ограничивает `--nsyms 1..8`). Что должно происходить на уровне PL при превышении? *(§8.2.3.)*
16. **Два бита `CODED` в beat 4** записи пакета: `[20]` и `[24]` — намеренный дубль? Шапка `phy_rx_pkt_out.sv:11` описывает beat 4 иначе. *(§10.4.1.)*
17. **Бит 6 `flags_q`** всегда 0 — резерв? *(§9.14.3.)*
18. **CRC-32.** `phy_crc` нигде не инстанцируется; `ARCHITECTURE_DUALMODE.md` на схеме TX пишет «CRC/framing (axis wrapper)». Предполагалось ли вычислять CRC в PL, и достаточно ли CRC-32 в программе (`plutolink`)? *(§5.2.1.)*
19. **Регистр `MODE` RX (бит 2) при `HDR_EN=0`.** Подтвердить: тогда `mode_q = cfg_mode` и символ-заголовок всё равно ожидается в кадре и декодируется (только игнорируется результат)? *(§9.14.2.)*
20. **CDC конфигурации.** `phy_regs_axil` передаёт многобитные конфиг-слова через 2-FF без согласования отдельных бит — допустимо ли для `RMIN` (32 бита) при записи во время активной обработки, или запись всегда делается в простое/в soft reset? *(§10.3.3.)*

### E. Прочее

21. **Латентность БПФ:** `STATUS.md` называет 2105 (TX) и «2114+» (RX), формула `fft_core_latency` + обёртка даёт 2114 и 2115. Какое число актуально? *(§3.3.1.)*
22. **Лишние/неиспользуемые константы** в `phy_pkg.sv` (`LDPC_RATE_NUM/DEN`, `SYMBOL_RATE_HZ`, `CODED_BITS_PER_OFDM`, `LLR_*` для uncoded): убирать ли? *(§13.2.1.)*
23. **`phy_fifo`** в шапке ссылается на `tb_phy_fifo`, которого нет. *(§11.7.)*
24. **Старый комментарий «550 байт»** в `phy_tx_top.sv:7-8` и `phy_tx_axis_top.v:9-10`. Обновить? *(§8.2, §10.1.4.)*
25. **Реальное железо.** Что известно про фактический формат данных АЦП (12 бит, расширение знаком), поведение DMA на `tlast` (README `plutolink`, «Unverified on hardware»)? *(гл. 12.)*
26. **Ресурсы.** В `STATUS.md` для Z7020 после v0.8.0: Baseline 27874 LUT / 58 BRAM / 84 DSP (синтез); полная имплементация с Patch A/B не сделана. Планируется ли? *(§9.16.)*
27. **Как переключается Patch B на время приёма** (занятость до 0,58 мс на 4 символа): какое влияние на пропуск следующего пакета при `GAP_SAMPLES` по умолчанию 16384 (0,53 мс — меньше времени второго прохода)? *(§9.13; расчёт: 16384 / 30,72 МГц = 0,533 мс.)*
