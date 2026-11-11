# EPEVER ET-NC / IT-NC / TES / TIS Series Solar Charge Controller Communication Protocol V1.00

## Applicable models

| Series | Load output | Source of this documentation |
|---|:---:|---|
| **ET-NC** (G3) | ❌ | *ET-NC G3 Series Solar Charge Controller Communication Protocol V1.00* (Chinese, 10 pages) |
| **IT-NC** | ✅ | **Assumed** = ET-NC + the load registers of the TIS series (no official document yet) |
| **TES** | ❌ | **Assumed** = TIS without the load registers (no official document yet) |
| **TIS** | ✅ | *TIS Series Solar Charge Controller Communication Protocol V1.00* (English, 10 pages) |

> [!NOTE]
> The IT-NC and TES columns are deduced, not taken from an official EPEVER document.
> They must be corrected when the official protocols for these series are available.

> [!WARNING]
> This register map is **not compatible** with the legacy EPEVER protocol used by the XTRA, Triron and Tracer AN/BN series
> (`Epever_Modbus_A_or_B_Series_Solar_Charge_Controller_Protocol_v2.6.pdf`).
> Many addresses are shifted or have a different meaning (e.g. `0x311A`, `0x3201`, `0x330C`, `0x9003`–`0x900E`, `0x903D`).

## Legend

| Symbol | Meaning |
|:---:|---|
| ✅ | Register present |
| ❌ | Register absent |

| Column | Meaning |
|---|---|
| **FC** | Modbus function code: `0x04` read input registers, `0x03` read holding registers, `0x10` write multiple registers, `0x01` read coils, `0x05` write single coil |
| **Type** | Data type as documented (see [Errata](#errata-in-the-original-documents) for signed values documented as `uint16`) |
| **Scale** | `÷100` = raw value / 100 (two decimals), `÷10` = raw value / 10 (one decimal), `1` = no decimal, `×100` = raw value × 100 |
| **Access** | `R` = read only, `RW` = read and write |

## Communication characteristics

1. Standard Modbus-RTU protocol.
2. Default device ID: `1` (configurable from 1 to 100 in register `0x9045`).
3. Serial parameters: 115200 bps, 8 data bits, 1 stop bit, no parity, no flow control (baud rate configurable in register `0x9046`).
4. Register addresses are in hexadecimal, with a base address offset of `0x00`.
5. All 32-bit values use two consecutive 16-bit registers: **L** (low word, lower address) then **H** (high word).
   `value = (H << 16) + L`.
6. Signed values use two's complement.

### Register address format

`aaaa.bb-cc`

- `aaaa`: four-digit hexadecimal register address.
- `bb`: a single bit of the register (00 to 15).
- `bb-cc`: the consecutive bits `bb` to `cc` of the register.

Examples: `3202.01` is bit D1 of register `0x3202`; `3202.02-03` are bits D2 and D3 of register `0x3202`.

---

## 1. Rated data (FC `0x04`, read only)

| Address | Name | Unit | Type | Scale | ET-NC | IT-NC | TES | TIS | Description |
|---|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|---|
| `0x3002` | PV maximum input voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Rated voltage of the PV input |
| `0x3003` | PV maximum input current | A | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Rated current of the PV input |
| `0x3004` | PV input rated power L | W | uint32 | ÷100 | ✅ | ✅ | ✅ | ✅ | Rated power of the PV input |
| `0x3005` | PV input rated power H | W | | | ✅ | ✅ | ✅ | ✅ | |
| `0x3006` | Battery rated voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Rated voltage of the DC (battery) terminal |
| `0x3007` | Battery rated charging current | A | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Rated charging current of the battery |
| `0x3008` | Battery rated charging power L | W | uint32 | ÷100 | ✅ | ✅ | ✅ | ✅ | Rated charging power of the battery |
| `0x3009` | Battery rated charging power H | W | | | ✅ | ✅ | ✅ | ✅ | |
| `0x300A` | Load rated voltage | V | uint16 | ÷100 | ❌ | ✅ | ❌ | ✅ | Rated voltage of the load output |
| `0x300B` | Load rated current | A | uint16 | ÷100 | ❌ | ✅ | ❌ | ✅ | Rated current of the load output |
| `0x300C` | Load rated power L | W | uint32 | ÷100 | ❌ | ✅ | ❌ | ✅ | Rated power of the load output |
| `0x300D` | Load rated power H | W | | | ❌ | ✅ | ❌ | ✅ | |

## 2. Real-time data (FC `0x04`, read only)

| Address | Name | Unit | Type | Scale | ET-NC | IT-NC | TES | TIS | Description |
|---|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|---|
| `0x3100` | PV1 input voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x3101` | PV1 input current | A | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x3102` | PV1 input power L | W | uint32 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x3103` | PV1 input power H | W | | | ✅ | ✅ | ✅ | ✅ | |
| `0x3104` | PV1 output voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Output side of the PV1 MPPT stage |
| `0x3105` | PV1 output current | A | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x3106` | PV1 output power L | W | uint32 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x3107` | PV1 output power H | W | | | ✅ | ✅ | ✅ | ✅ | |
| `0x3108` | PV2 input voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x3109` | PV2 input current | A | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x310A` | PV2 input power L | W | uint32 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x310B` | PV2 input power H | W | | | ✅ | ✅ | ✅ | ✅ | |
| `0x310C` | PV2 output voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Output side of the PV2 MPPT stage |
| `0x310D` | PV2 output current | A | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x310E` | PV2 output power L | W | uint32 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x310F` | PV2 output power H | W | | | ✅ | ✅ | ✅ | ✅ | |
| `0x3110` | Load output voltage | V | uint16 | ÷100 | ❌ | ✅ | ❌ | ✅ | |
| `0x3111` | Load output current | A | uint16 | ÷100 | ❌ | ✅ | ❌ | ✅ | |
| `0x3112` | Load output power L | W | uint32 | ÷100 | ❌ | ✅ | ❌ | ✅ | |
| `0x3113` | Load output power H | W | | | ❌ | ✅ | ❌ | ✅ | |
| `0x3114` | Battery voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x3115` | Battery total charging current | A | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x3116` | Battery total discharging current | A | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x3117` | Battery total current | A | uint16 ¹ | ÷100 | ✅ | ✅ | ✅ | ✅ | Net battery current |
| `0x3118` | Remote battery temperature | °C | int16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x3119` | Battery SOC | % | uint16 | ÷100 ² | ✅ | ✅ | ✅ | ✅ | |
| `0x311A` | Device temperature | °C | int16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x311D` | Current system voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x311E` | Highest PV voltage of the two inputs | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x311F` | PV total charging current | A | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x3120` | PV total charging power L | W | uint32 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x3121` | PV total charging power H | W | | | ✅ | ✅ | ✅ | ✅ | |

¹ Probably signed (`int16`): see [Errata](#errata-in-the-original-documents).
² Both documents give a coefficient of 100 for the SOC. This must be checked on a real device: the SOC of the BMS (`0x3406`) uses a coefficient of 1.

## 3. Status registers (FC `0x04`, read only)

| Address | Name | Type | ET-NC | IT-NC | TES | TIS | Description |
|---|---|:---:|:---:|:---:|:---:|:---:|---|
| `0x3200` | Battery status | uint16 | ✅ | ✅ | ✅ | ✅ | See [0x3200](#0x3200--battery-status) |
| `0x3201` | Load status | uint16 | ❌ | ✅ | ❌ | ✅ | See [0x3201](#0x3201--load-status) |
| `0x3202` | Charging status 1 (PV1) | uint16 | ✅ | ✅ | ✅ | ✅ | See [0x3202](#0x3202--charging-status-1-pv1) |
| `0x3203` | Charging status 2 (PV2) | uint16 | ✅ | ✅ | ✅ | ✅ | See [0x3203](#0x3203--charging-status-2-pv2) |
| `0x3205` | Device status (BMS) | uint16 | ✅ | ✅ | ✅ | ✅ | See [0x3205](#0x3205--device-status-bms) |
| `0x3206` | Parallel charging status | uint16 | ✅ | ✅ | ✅ | ✅ | See [0x3206](#0x3206--parallel-charging-status). Only in the ET-NC document, see [Errata](#errata-in-the-original-documents) |

### `0x3200` – Battery status

| Bits | Description |
|---|---|
| D3-D0 | `00H` Normal, `01H` Overvoltage, `02H` Undervoltage, `03H` Over-discharge |
| D7-D4 | `00H` Normal, `01H` Over temperature (above the over temperature alarm setting), `02H` Low temperature (below the low temperature alarm setting) ³ |
| D9 | Lithium battery charging protection |
| D10 | Lithium battery discharging protection |

³ `02H` (low temperature) is only listed in the ET-NC document.

### `0x3201` – Load status

| Bits | Description |
|---|---|
| D13-D12 | Output power: `00` Normal, `03` Overload |
| D11 | Short circuit |
| D1 | `0` Normal, `1` Fault |
| D0 | `1` Running, `0` Standby |

### `0x3202` – Charging status 1 (PV1)

| Bits | Description |
|---|---|
| D15-D14 | PV1 input voltage status: `00` Normal, `02H` Input voltage too high |
| D13 | Phase 1 charging MOSFET short circuit |
| D12 | Phase 2 charging MOSFET short circuit |
| D11 | Phase 3 charging MOSFET short circuit |
| D10 | Phase 4 charging MOSFET short circuit |
| D5 | Device over temperature |
| D4 | PV1 or battery reverse connection |
| D3-D2 | Charging stage: `00` Not charging, `01` Float, `02` Boost (bulk), `03` Equalization |
| D1 | Day / night: `0` Day, `1` Night |
| D0 | PV1 status: `1` Running, `0` Standby |

### `0x3203` – Charging status 2 (PV2)

| Bits | Description |
|---|---|
| D15-D14 | PV2 input voltage status: `00` Normal, `02H` Input voltage too high |
| D11 | PV mode error alarm 1 |
| D10 | PV mode error alarm 2 |
| D9 | `0` Normal, `1` PLL low battery |
| D8 | Hard dry contact status: `0` Charging allowed, `1` Charging not allowed |
| D7 | PV2 relay closing fault |
| D6 | PV1 relay closing fault |
| D5 | MPPT status |
| D4 | PV2 or battery reverse connection |
| D0 | PV2 status: `1` Running, `0` Standby |

### `0x3205` – Device status (BMS)

| Bits | Description | Action of the controller |
|---|---|---|
| D15 | Parallel ID duplicated | |
| D14 | DSP communication fault | |
| D13 | SOC full | |
| D12 | SOC too low | Discharging OFF |
| D11 | BMS other fault | Charging and discharging OFF |
| D10 | BMS overcharge protection | Charging OFF |
| D9 | Cell overvoltage | None |
| D8 | Cell undervoltage | None |
| D7 | Cell over temperature | None |
| D6 | Cell low temperature | None |
| D5 | BMS sensor fault | None |
| D4 | BMS over-discharge | Discharging OFF |
| D3 | BMS charging protection | Charging OFF |
| D2 | BMS discharging protection | Discharging OFF |
| D1 | Low SOC alarm | None |
| D0 | `1` BMS online, `0` BMS offline | |

### `0x3206` – Parallel charging status

| Bits | Description |
|---|---|
| D0 | `1` Controller in parallel mode, `0` Not in parallel |
| D1 | `1` Charging, `0` Not charging |
| D2 | `1` Global scan in progress, `0` Scan finished |
| D3 | `1` Current limited constant duty cycle mode, `0` Normal charging mode |
| D4 | `1` MPPT maximum power point reached, `0` Not reached |

## 4. Statistics (FC `0x04`, read only)

| Address | Name | Unit | Type | Scale | ET-NC | IT-NC | TES | TIS | Description |
|---|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|---|
| `0x3300` | Today maximum PV input voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x3301` | Today maximum battery voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x3302` | Today minimum battery voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x3303` | Consumed energy today L | kWh | uint32 | ÷100 | ❌ | ✅ | ❌ | ✅ | Load output |
| `0x3304` | Consumed energy today H | kWh | | | ❌ | ✅ | ❌ | ✅ | |
| `0x3305` | Consumed energy this month L | kWh | uint32 | ÷100 | ❌ | ✅ | ❌ | ✅ | |
| `0x3306` | Consumed energy this month H | kWh | | | ❌ | ✅ | ❌ | ✅ | |
| `0x3307` | Consumed energy this year L | kWh | uint32 | ÷100 | ❌ | ✅ | ❌ | ✅ | |
| `0x3308` | Consumed energy this year H | kWh | | | ❌ | ✅ | ❌ | ✅ | |
| `0x3309` | Total consumed energy L | kWh | uint32 | ÷100 | ❌ | ✅ | ❌ | ✅ | |
| `0x330A` | Total consumed energy H | kWh | | | ❌ | ✅ | ❌ | ✅ | |
| `0x330B` | Generated energy today L | kWh | uint32 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x330C` | Generated energy today H | kWh | | | ✅ | ✅ | ✅ | ✅ | |
| `0x330D` | Generated energy this month L | kWh | uint32 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x330E` | Generated energy this month H | kWh | | | ✅ | ✅ | ✅ | ✅ | |
| `0x330F` | Generated energy this year L | kWh | uint32 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x3310` | Generated energy this year H | kWh | | | ✅ | ✅ | ✅ | ✅ | |
| `0x3311` | Total generated energy L | kWh | uint32 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x3312` | Total generated energy H | kWh | | | ✅ | ✅ | ✅ | ✅ | |
| `0x3313` | Learned battery capacity L | Ah | uint32 | 1 | ✅ | ✅ | ✅ | ✅ | |
| `0x3314` | Learned battery capacity H | Ah | | | ✅ | ✅ | ✅ | ✅ | |

## 5. Lithium battery BMS data (FC `0x04`, read only)

Data read by the controller from the lithium battery BMS.

| Address | Name | Unit | Type | Scale | ET-NC | IT-NC | TES | TIS | Description |
|---|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|---|
| `0x3400` | Number of cells | pcs | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Number of cells in the battery pack |
| `0x3401` | Battery pack voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x3402` | Battery pack main circuit current | A | uint16 ¹ | ÷100 | ✅ | ✅ | ✅ | ✅ | Positive when charging, negative when discharging |
| `0x3403` | Battery pack power L | W | uint32 | ÷100 | ✅ | ✅ | ✅ | ✅ | `power = (H << 16) + L` |
| `0x3404` | Battery pack power H | W | | | ✅ | ✅ | ✅ | ✅ | |
| `0x3405` | Battery pack full charge capacity | Ah | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | |
| `0x3406` | Battery pack remaining capacity | % | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | |
| `0x3407` | Battery pack remaining operating time | min | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | |
| `0x3408` | Maximum cell temperature | °C | int16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x3409` | Minimum cell temperature | °C | int16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x340A` | Balancing temperature | °C | int16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Cell balancing circuit temperature ⁴ |
| `0x340B` | Ambient temperature | °C | int16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x340C` | MOSFET temperature | °C | int16 | ÷100 | ✅ | ✅ | ✅ | ✅ | |
| `0x340D` | Cycle count | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Charge / discharge cycles of the battery pack |
| `0x340E` | Balancing status | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | `0` Balancing OFF, `1` Balancing ON ⁴ |
| `0x340F` | Battery pack voltage status | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | See [voltage / current status codes](#0x340f--0x3410--0x3416--voltage-current-and-cell-status-codes) |
| `0x3410` | Battery pack current status | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | See [voltage / current status codes](#0x340f--0x3410--0x3416--voltage-current-and-cell-status-codes) |
| `0x3411` | MOSFET status | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | D0: `1` Charging MOSFET ON. D1: `1` Discharging MOSFET ON |
| `0x3412` | Cell temperature status | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | See [temperature status codes](#0x3412-to-0x3415--temperature-status-codes) |
| `0x3413` | Balancing temperature status | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | See [temperature status codes](#0x3412-to-0x3415--temperature-status-codes) |
| `0x3414` | Ambient temperature status | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | See [temperature status codes](#0x3412-to-0x3415--temperature-status-codes) |
| `0x3415` | MOSFET temperature status | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | See [temperature status codes](#0x3412-to-0x3415--temperature-status-codes) |
| `0x3416` | Cell status | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | See [voltage / current status codes](#0x340f--0x3410--0x3416--voltage-current-and-cell-status-codes) |
| `0x3417` | Battery protocol | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Refer to the BMS protocol list of the battery manufacturers |
| `0x3418` | Battery status | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | See [0x3418](#0x3418--bms-battery-status) |
| `0x3419` | Battery pack protocol type | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | `0` Standard protocol, `1` High current protocol (several battery packs in parallel) |
| `0x341A` | Battery pack voltage | V | uint16 | ÷10 | ✅ | ✅ | ✅ | ✅ | Valid range 0 to 6500 V |
| `0x341B` | Battery pack current | A | uint16 ¹ | ÷10 | ✅ | ✅ | ✅ | ✅ | Positive when charging, negative when discharging. Range -3276 to +3276 A |
| `0x341C` | Battery pack undervoltage protection | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Equivalent to the low voltage disconnect voltage. The BMS protects after 2 s, the controller must cut the load after 1 s |
| `0x341D` | Battery pack overvoltage alarm | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Equivalent to the charging limit voltage |
| `0x341E` | Battery pack charging current limit | A | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Real-time charging current limit requested by the BMS |
| `0x341F` | Battery pack discharging current limit | A | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Real-time discharging current limit requested by the BMS |
| `0x3420` | Battery bank undervoltage protection | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Same as `0x341C`, for the whole battery bank |
| `0x3421` | Battery bank overvoltage alarm | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Same as `0x341D`, for the whole battery bank |
| `0x3422` | Battery bank charging current limit | A | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Same as `0x341E`, for the whole battery bank |
| `0x3423` | Battery bank discharging current limit | A | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Same as `0x341F`, for the whole battery bank |

⁴ The Chinese term 均衡 is translated as "equalization charging" in the TIS document. In this BMS context it means **cell balancing**.

### `0x340F` / `0x3410` / `0x3416` – Voltage, current and cell status codes

| Value | `0x340F` Pack voltage | `0x3410` Pack current | `0x3416` Cell | Action of the controller |
|---|---|---|---|---|
| `0x00` | Normal | Normal | Normal | |
| `0x01` | Undervoltage alarm | Over-discharge alarm | Undervoltage alarm | Discharging OFF |
| `0x02` | Overvoltage alarm | Overcharge alarm | Overvoltage alarm | Charging OFF |
| `0xF0` | | | Cell test | |
| `0xF1` | Undervoltage protection | Over-discharge protection | Undervoltage protection | Discharging OFF |
| `0xF2` | Overvoltage protection | Overcharge protection | Overvoltage protection | Charging OFF |

### `0x3412` to `0x3415` – Temperature status codes

| Value | Description |
|---|---|
| `0x00` | Normal |
| `0x01` | Low temperature alarm |
| `0x02` | High temperature alarm |
| `0xF0` | NTC fault |
| `0xF1` | Low temperature protection (charging and discharging OFF) |
| `0xF2` | High temperature protection (charging and discharging OFF) |

### `0x3418` – BMS battery status

| Bits | Description |
|---|---|
| D0 | `1` Charging protection, `0` Normal |
| D1 | `1` Discharging protection, `0` Normal |
| D3 | `1` Other protection (charging and discharging OFF), `0` Normal |
| D4 | `1` Charging over temperature, `0` Normal |
| D5 | `1` Discharging over temperature, `0` Normal |
| D12 | `1` Full charge request, `0` Normal |
| D13 | `1` Forced charging request, `0` Normal |
| D14 | Discharging: `0` Enabled, `1` Disabled |
| D15 | Charging: `0` Enabled, `1` Disabled |

## 6. Battery and device parameters (FC `0x03` read, `0x10` write)

`N` = rated voltage level of the system (`1` = 12 V, `2` = 24 V, `3` = 36 V, `4` = 48 V).

| Address | Name | Unit | Type | Scale | ET-NC | IT-NC | TES | TIS | Description |
|---|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|---|
| `0x9000` | Battery type | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | `0000H` User defined, `0001H` Sealed (AGM), `0002H` Gel, `0003H` Flooded, `0004H` LiFePO4 4S, `0005H` LiFePO4 8S, `0006H` LiFePO4 15S, `0007H` LiFePO4 16S, `0008H` Li(NiCoMn)O2 3S, `0009H` Li(NiCoMn)O2 6S, `000AH` Li(NiCoMn)O2 7S, `000BH` Li(NiCoMn)O2 13S, `000CH` Li(NiCoMn)O2 14S. Default `1` |
| `0x9001` | Battery capacity | Ah | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Range 1 to 4000. Default 100 |
| `0x9002` | Temperature compensation coefficient | mV/°C/2V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Used for the equalize, boost, float and low voltage disconnect voltages. Range 0 to 900. Default 300 |
| `0x9003` | Temperature unit | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Display unit: `1` °C, `0` °F. Default 1 |
| `0x9004` | Screen backlight time | s | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Backlight turns off after this delay. `0` Always OFF, `100` Always ON. Range 0 to 100. Default 0 |
| `0x9005` | Screen cycle time | s | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Automatic page rotation period. `0` Fixed display. Range 0 to 100. Default 0 |
| `0x9006` | Rated voltage level | | int16 | 1 | ✅ | ✅ | ✅ | ✅ | `0` Auto recognition, `1` 12 V, `2` 24 V, `3` 36 V, `4` 48 V. Default 0 |
| `0x9007` | Overvoltage disconnect voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Charging stops when the battery voltage is above this value. Range 900 to 1700 × N |
| `0x9008` | Charging limit voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Highest charging voltage in any case. Compensated equalize, boost and float voltages are capped at this value. Range 900 to 1700 × N |
| `0x9009` | Overvoltage reconnect voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Calculated from the other values. Charging and discharging resume when the battery voltage is below this value. Range 900 to 1700 × N |
| `0x900A` | Equalize charging voltage | V | int16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Target voltage of the equalize stage. Range 900 to 1700 × N |
| `0x900B` | Boost charging voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Constant voltage stage (usually 1 to 3 h, typically 2 h), followed by the float stage. Range 900 to 1700 × N |
| `0x900C` | Float charging voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Voltage held after the equalize or boost stage. Range 900 to 1700 × N |
| `0x900D` | Boost reconnect charging voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Calculated from the other values. The boost stage restarts when the battery voltage stays below this value (and above the low voltage disconnect voltage) for at least 1 min |
| `0x900E` | Low voltage reconnect voltage | V | int16 | ÷100 | ✅ | ✅ | ✅ | ✅ | The load output is restored above this value. Range 900 to 1700 × N |
| `0x900F` | Undervoltage warning recover voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | The undervoltage warning is cleared above this value (e.g. to stop an auxiliary charger). Range 900 to 1700 × N |
| `0x9010` | Undervoltage warning voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Battery is low, close to the low voltage disconnect (e.g. to start a generator). Range 900 to 1700 × N |
| `0x9011` | Low voltage disconnect voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | The load output is disconnected below this value (typically 20 % remaining capacity). Range 900 to 1700 × N |
| `0x9012` | Discharging limit voltage | V | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Calculated from the other values. Lowest discharge voltage in any case. Range 900 to 1700 × N |
| `0x9013` | Maximum charging current | A | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Charging current limit, lower than the rated charging current. Range 100 to 10000. Default depends on the model |
| `0x9014` | Equalize charging time | min | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Cumulative time above the equalize voltage. Range 0 to 120. Default 120 |
| `0x9015` | Boost charging time | min | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Cumulative time above the boost voltage. Range 0 to 120. Default 120 |
| `0x9016` | Lithium battery protection | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Low temperature charging / discharging protection: `3` Enabled, `4` Disabled. From the device keypad, it can only be changed with a lithium or user battery type |
| `0x9017` | Low temperature charging limit | °C | int16 | ÷100 | ✅ | ✅ | ✅ | ✅ | With lithium protection enabled, charging stops below this value. Range -2500 to 1000 |
| `0x9018` | Low temperature discharging limit | °C | int16 | ÷100 | ✅ | ✅ | ✅ | ✅ | With lithium protection enabled, discharging stops below this value. Range -2500 to 1000 |
| `0x9019` | Real-time clock (seconds / minutes) | | uint16 | | ✅ | ✅ | ✅ | ✅ | D7-D0: Second, D15-D8: Minute |
| `0x901A` | Real-time clock (hours / days) | | uint16 | | ✅ | ✅ | ✅ | ✅ | D7-D0: Hour, D15-D8: Day |
| `0x901B` | Real-time clock (month / year) | | uint16 | | ✅ | ✅ | ✅ | ✅ | D7-D0: Month, D15-D8: Year |
| `0x901C` | Battery high temperature limit | °C | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Alarm, charging and discharging stop above this value. Normal operation resumes 10 °C (1000) below. Range 4000 to 10000. Default 6500 |
| `0x901D` | Battery low temperature limit | °C | int16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Alarm below this value. Normal operation resumes 10 °C (1000) above. Range -6000 to 0 |
| `0x901E` | Device high temperature limit | °C | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Internal temperature. Alarm, charging and discharging stop above this value. Range 6500 to 10000 |
| `0x901F` | Device high temperature recovery | °C | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Normal operation resumes below this value. Range 5500 to 10000 |
| `0x9020` | Power module high temperature limit | °C | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Power components surface temperature. Alarm, charging and discharging stop above this value. Range 6500 to 10000 |
| `0x9021` | Power module high temperature recovery | °C | uint16 | ÷100 | ✅ | ✅ | ✅ | ✅ | Normal operation resumes below this value. Range 5500 to 10000 |

### Voltage parameter constraints

The constraints below come from the legacy A/B protocol (v2.6), which uses the same 12 voltage parameters in the same order (at `0x9003`–`0x900E`).
They are not repeated in the ET-NC / TIS documents but are expected to apply.

Lead-acid battery:

1. Overvoltage disconnect > Charging limit > Equalize charging > Boost charging > Float charging > Boost reconnect charging
2. Undervoltage warning recover > Undervoltage warning > Low voltage disconnect > Discharging limit
3. Overvoltage disconnect > Overvoltage reconnect
4. Low voltage reconnect > Low voltage disconnect

Lithium battery:

1. Overvoltage disconnect > BMS overcharge protection voltage + 0.2 V
2. Overvoltage disconnect > Overvoltage reconnect = Charging limit ≥ Equalize charging = Boost charging ≥ Float charging > Boost reconnect charging
3. Low voltage reconnect > Low voltage disconnect ≥ Discharging limit
4. Undervoltage warning recover > Undervoltage warning ≥ Discharging limit
5. Boost reconnect charging > Low voltage reconnect
6. Low voltage disconnect ≥ BMS over-discharge protection voltage + 0.2 V

## 7. Load output parameters (FC `0x03` read, `0x10` write)

| Address | Name | Unit | Type | Scale | ET-NC | IT-NC | TES | TIS | Description |
|---|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|---|
| `0x9022` | Load control mode | | uint16 | 1 | ❌ | ✅ | ❌ | ✅ | `0` Manual (default), `1` Light ON/OFF (dusk to dawn), `2` Light ON + Timer, `3` Timer, `6` Always ON, `7` ON at sunrise |
| `0x9023` | Manual mode default state | | uint16 | 1 | ❌ | ✅ | ❌ | ✅ | `0` OFF, `1` ON |
| `0x9024` | Night threshold voltage (turn-on voltage) | V | uint16 | ÷100 | ❌ | ✅ | ❌ | ✅ | Below this PV voltage, the controller considers that night is coming. Range 500 to 5000 |
| `0x9025` | Night delay (turn-on delay) | min | uint16 | 1 | ❌ | ✅ | ❌ | ✅ | Night is detected when the PV voltage stays below the night threshold for this time. Range 0 to 9900 (as documented) |
| `0x9026` | Day threshold voltage (turn-off voltage) | V | uint16 | ÷100 ⁵ | ❌ | ✅ | ❌ | ✅ | Above this PV voltage, the controller considers that day is coming. Range 60 to 8000. Default 600 |
| `0x9027` | Day delay (turn-off delay) | min | uint16 | 1 | ❌ | ✅ | ❌ | ✅ | Day is detected when the PV voltage stays above the day threshold for this time. Range 0 to 9900 (as documented) |
| `0x9028` | Night time | | uint16 | 1 | ❌ | ✅ | ❌ | ✅ | Length of the whole night, detected automatically or set by default. D15-D8: Hour, D7-D0: Minute. Range 3 to 24 h |
| `0x9029` | Timer period selection | | uint16 | 1 | ❌ | ✅ | ❌ | ✅ | `0` One period, `1` Two periods. Default 0 |
| `0x902A` | Working duration 1 | | uint16 | 1 | ❌ | ✅ | ❌ | ✅ | First load output duration (Light ON + Timer). D15-D8: Hour, D7-D0: Minute. Range 0 to 24 h. Default 1 h |
| `0x902B` | Working duration 2 | | uint16 | 1 | ❌ | ✅ | ❌ | ✅ | Second load output duration (Light ON + Timer). D15-D8: Hour, D7-D0: Minute. Range 0 to 24 h. Default 1 h |
| `0x902C` | Load ON time 1 – second | s | uint16 | 1 | ❌ | ✅ | ❌ | ✅ | Timer mode |
| `0x902D` | Load ON time 1 – minute | min | uint16 | 1 | ❌ | ✅ | ❌ | ✅ | |
| `0x902E` | Load ON time 1 – hour | h | uint16 | 1 | ❌ | ✅ | ❌ | ✅ | |
| `0x902F` | Load OFF time 1 – second | s | uint16 | 1 | ❌ | ✅ | ❌ | ✅ | |
| `0x9030` | Load OFF time 1 – minute | min | uint16 | 1 | ❌ | ✅ | ❌ | ✅ | |
| `0x9031` | Load OFF time 1 – hour | h | uint16 | 1 | ❌ | ✅ | ❌ | ✅ | |
| `0x9032` | Load ON time 2 – second | s | uint16 | 1 | ❌ | ✅ | ❌ | ✅ | Used when `0x9029` = `1` |
| `0x9033` | Load ON time 2 – minute | min | uint16 | 1 | ❌ | ✅ | ❌ | ✅ | |
| `0x9034` | Load ON time 2 – hour | h | uint16 | 1 | ❌ | ✅ | ❌ | ✅ | |
| `0x9035` | Load OFF time 2 – second | s | uint16 | 1 | ❌ | ✅ | ❌ | ✅ | |
| `0x9036` | Load OFF time 2 – minute | min | uint16 | 1 | ❌ | ✅ | ❌ | ✅ | |
| `0x9037` | Load OFF time 2 – hour | h | uint16 | 1 | ❌ | ✅ | ❌ | ✅ | |

⁵ The TIS document gives a coefficient of `0.01` (× 100) for `0x9026`, which is inconsistent with its default value (600 = 6.00 V) and with `0x9024`. ÷100 is assumed.

## 8. SOC, BMS and communication parameters (FC `0x03` read, `0x10` write)

| Address | Name | Unit | Type | Scale | ET-NC | IT-NC | TES | TIS | Description |
|---|---|:---:|:---:|:---:|:---:|:---:|:---:|:---:|---|
| `0x9038` | Charging and discharging mode | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Range 0 to 1. Default 0. Meaning not documented (probably `0` voltage, `1` SOC) |
| `0x9039` | Full charge protection SOC | % | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Range 1 to 100. Default 99 |
| `0x903A` | Full charge protection recovery SOC | % | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Range 1 to 100. Default 95 |
| `0x903B` | Discharging protection recovery SOC | % | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Range 1 to 100. Default 10 |
| `0x903C` | Low battery alarm recovery SOC | % | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Range 1 to 100. Default 10 |
| `0x903D` | Low battery alarm SOC | % | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Range 1 to 100. Default 8 |
| `0x903E` | Discharging protection SOC | % | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Range 1 to 100. Default 5 |
| `0x903F` | Data record period | min | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Interval for recording the operating status. Range 10 to 120. Default 10 |
| `0x9040` | BMS protocol | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Protocol number of the lithium battery BMS manufacturer. Range 1 to 230 |
| `0x9041` | Use BMS settings | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | `0` Disabled, `1` Enabled. When enabled and valid BMS data is read, the 12 charging voltages are derived from the BMS undervoltage protection and overvoltage alarm values, and the charging / discharging currents are limited to the BMS limits |
| `0x9042` | PV connection mode | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | `0` Independent inputs, `1` Centralized (parallel) input |
| `0x9043` | Simulated BMS mode | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | `1` Enable simulated BMS end-of-charge current limiting (MAP table), `0` Disabled |
| `0x9045` | Device address | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Modbus ID. Range 1 to 100. Default 1 |
| `0x9046` | Baud rate | bps | uint16 | ×100 | ✅ | ✅ | ✅ | ✅ | `1152` 115200, `96` 9600, `24` 2400. Default 1152. Applied after a restart |
| `0x9047` | Parallel battery maximum charging current | A | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Range 100 to 1200 |
| `0x9049` | PV charging restart period | min | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | Range 0 to 60. Default 10 |
| `0x904A` | Communication port enable | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | `1` Normal, `0` OFF: communication is turned off when the device is neither charging nor discharging. Default 1 |
| `0x904B` | Manual equalize charging | | uint16 | 1 | ✅ | ✅ | ✅ | ✅ | `0` Normal, `1` Trigger one equalize charge. Default 0 |

> [!NOTE]
> `0x9044` and `0x9048` are not documented.

## 9. Coils (FC `0x01` read, `0x05` write)

| Address | Name | Type | ET-NC | IT-NC | TES | TIS | Description |
|---|---|:---:|:---:|:---:|:---:|:---:|---|
| `0x0000` | Charging ON/OFF | bool | ✅ | ✅ | ✅ | ✅ | `1` Charging ON, `0` Charging OFF |
| `0x0003` | Load manual ON/OFF | bool | ❌ | ✅ | ❌ | ✅ | When the load control mode is manual: `1` Load ON, `0` Load OFF |
| `0x0007` | Restore factory settings | bool | ✅ | ✅ | ✅ | ✅ | `1` Restore the default settings |
| `0x0008` | Clear energy statistics | bool | ✅ | ✅ | ✅ | ✅ | `1` Clear the statistics. Root permission required |
| `0x0009` | Clear faults | bool | ✅ | ✅ | ✅ | ✅ | `1` Clear the device faults |

---

## Parsing examples

The examples below are corrected versions of those found in the original documents (see [Errata](#errata-in-the-original-documents)).
The CRCs have been recalculated.

### 1. Read the battery voltage (`0x3114`)

Request: `01 04 31 14 00 01 7F 32`

| Value | Meaning |
|---|---|
| `01` | Device ID |
| `04` | Function code (read input registers) |
| `31 14` | Start address `0x3114` |
| `00 01` | Number of registers |
| `7F 32` | CRC |

Response: `01 04 02 0A 25 7E 4B`

| Value | Meaning |
|---|---|
| `01` | Device ID |
| `04` | Function code |
| `02` | Byte count |
| `0A 25` | `0x0A25` = 2597 → battery voltage = 25.97 V (÷100) |
| `7E 4B` | CRC |

### 2. Read the battery parameters (`0x9000`–`0x9002`)

Request: `01 03 90 00 00 03 28 CB`

| Value | Meaning |
|---|---|
| `01` | Device ID |
| `03` | Function code (read holding registers) |
| `90 00` | Start address `0x9000` |
| `00 03` | Number of registers |
| `28 CB` | CRC |

Response: `01 03 06 00 00 00 C8 01 2C A0 C6`

| Value | Meaning |
|---|---|
| `01` | Device ID |
| `03` | Function code |
| `06` | Byte count |
| `00 00` | `0x9000` battery type = `0` User defined |
| `00 C8` | `0x9001` battery capacity = 200 Ah |
| `01 2C` | `0x9002` temperature compensation coefficient = 300 → 3 mV/°C/2V (applied as -3 mV/°C/2V) |
| `A0 C6` | CRC |

### 3. Write the load control mode (`0x9022`) – IT-NC / TIS only

Request: `01 10 90 22 00 01 02 00 02 B1 1A`

| Value | Meaning |
|---|---|
| `01` | Device ID |
| `10` | Function code (write multiple registers) |
| `90 22` | Start address `0x9022` |
| `00 01` | Number of registers |
| `02` | Byte count |
| `00 02` | Value `2` = Light ON + Timer |
| `B1 1A` | CRC |

Response: `01 10 90 22 00 01 8C C3`

| Value | Meaning |
|---|---|
| `01` | Device ID |
| `10` | Function code |
| `90 22` | Start address |
| `00 01` | Number of registers written |
| `8C C3` | CRC |

### 4. Switch charging OFF / ON (coil `0x0000`)

| Command | Frame |
|---|---|
| Charging OFF | `01 05 00 00 00 00 CD CA` |
| Charging ON | `01 05 00 00 FF 00 8C 3A` |

The device echoes the request as its response.

### 5. 32-bit values

Example: PV input rated power = 3000.00 W, scale ÷100 → raw value 300000 = `0x000493E0`.

| Register | Value |
|---|---|
| `0x3004` (L) | `0x93E0` |
| `0x3005` (H) | `0x0004` |

### 6. Signed values

Signed values (currents, temperatures) use two's complement.
Example: battery total current `0x3117` reads `0xFFF9` → 65529 − 65536 = −7 → −0.07 A (÷100).

---

## Errata in the original documents

| # | Document | Error | Correction |
|---|---|---|---|
| 1 | ET-NC, TIS | Introduction copied from the inverter protocol: "default Inverter ID", `300F` "Utility voltage", `90CE.04` "Battery connection method", `90C8.09-11` "Discharging mode" | These registers do not exist on these controllers |
| 2 | ET-NC | All parsing examples are copied from the inverter protocol (`0x3021`, `0x90C8`, `0x9108` mains voltage settings…) | Use the examples above |
| 3 | TIS | Example 1 reads `0x3108` as "Battery voltage" ("starting from 0x301A") | `0x3108` is the PV2 input voltage. The battery voltage is `0x3114` |
| 4 | TIS | Example 3 writes the load control mode to `0x903D` ("starting from 0x913D"), copied from the A/B protocol | `0x903D` is the **low battery alarm SOC**. The load control mode is `0x9022` |
| 5 | TIS | Example 4 restores the factory settings with coil `0x0013`, copied from the A/B protocol | The factory reset coil is `0x0007` |
| 6 | ET-NC, TIS | The 32-bit example uses `0x3002`/`0x3003` as "charging input power" | `0x3002`/`0x3003` are the PV maximum voltage and current. The PV rated power is `0x3004`/`0x3005` |
| 7 | ET-NC, TIS | The signed value example uses battery current `0x3022` with a ÷10 scale | The battery total current is `0x3117` (÷100) |
| 8 | ET-NC, TIS | `0x3117`, `0x3402` and `0x341B` are documented as `uint16` but can be negative | Read them as `int16` |
| 9 | TIS | `0x9026` coefficient `0.01` | ÷100, like `0x9024` |
| 10 | TIS | `0x340A`, `0x340E`, `0x3413` translated as "Equalization charging" | Cell **balancing** (BMS) |
| 11 | ET-NC (PDF metadata) | Title "B-Series MODBUS Specification" | Not related to the A/B (B-Series) protocol |
| 12 | TIS | Rows numbered 53 and 58 for `0x3303`/`0x3304` | Numbering only |
| 13 | TIS | `0x3206` (parallel charging status) is missing, while the 4 series support parallel operation of up to 6 controllers (IT-NC G3 and TIS manuals, EPEVER product descriptions) | Assumed present on the 4 series |
