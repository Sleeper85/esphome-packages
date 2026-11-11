# EPEVER solar charge controllers

ESPHome packages to monitor and configure EPEVER MPPT solar charge controllers over Modbus RTU (RS485).

EPEVER controllers use two Modbus register maps that are **not compatible** with each other.
This project calls them **Gen1** and **Gen2**. This naming is specific to this project: it is not
an EPEVER name.

> [!WARNING]
> Using the YAML of the wrong generation reads wrong values and, worse, **writes the battery
> settings to the wrong registers**. Check your model in the tables below before flashing.

## Which YAML for which controller?

| Generation | Series | Load output | PV inputs | Root YAML |
|---|---|:---:|:---:|---|
| Gen1 | XTRA, Triron, Tracer AN, Tracer BP | ✅ | 1 | `epever_solar_charge_controller_Gen1.yaml` |
| Gen2 | ET-NC G3 | ❌ | 1 or 2 | `epever_solar_charge_controller_Gen2_ET-NC.yaml` |
| Gen2 | IT-NC G3 / IT-NC G3 BLE | ✅ | 1 or 2 | `epever_solar_charge_controller_Gen2_IT-NC.yaml` |
| Gen2 | TES | ❌ | 1 or 2 | `epever_solar_charge_controller_Gen2_TES.yaml` |
| Gen2 | TIS | ✅ | 1 or 2 | `epever_solar_charge_controller_Gen2_TIS.yaml` |

How to recognise a Gen2 controller: it has a dedicated **BMS port** and **CAN-PARA** ports for
parallel operation (up to 6 controllers). Gen1 controllers have neither.

## Gen1

Register map: `documents/Epever_Modbus_A_or_B_Series_Solar_Charge_Controller_Protocol_v2.6.pdf`.

Compatible models: XTRA, Triron, Tracer AN, Tracer BP (series listed in the protocol document).

| Package | Content |
|---|---|
| `epever_scc_Gen1_full.yaml` | Sub-device, Modbus controller, includes the two files below |
| `epever_scc_Gen1_entities_sensor.yaml` | PV, battery, statuses, energy statistics, battery settings (read) |
| `epever_scc_Gen1_entities_number.yaml` | Battery settings (write) |

## Gen2

Register map: `documents/Epever_Modbus_ET-NC_IT-NC_TES_TIS_Series_Solar_Charge_Controller_Protocol_V1.00.md`.

In the series names, **E** means without load output (ET-NC, TES) and **I** with load output
(IT-NC, TIS).

### Models

| Series | Models | Two PV inputs |
|---|---|---|
| ET-NC G3 | 50 A to 100 A (model list to be completed) | selected models, see the manual |
| IT-NC G3 | IT5420NC G3, IT6415NC G3, IT6420NC G3, IT7415NC G3, IT7420NC G3, IT8420NC G3, IT10415NC G3, IT10420NC G3, IT6415NC G3 BLE, IT10415NC G3 BLE | IT8420NC G3, IT10415NC G3, IT10415NC G3 BLE, IT10420NC G3 |
| TES | 60 A to 100 A (model list to be completed) | selected models, see the manual |
| TIS | TIS6415, TIS8415, TIS10415, TIS6425, TIS7425, TIS8425, TIS10425 | TIS8425, TIS10415, TIS10425 |

Naming rule (IT-NC G3 manual), for example `IT 10 4 20 N C G3`:

| Part | Meaning |
|---|---|
| `IT` | Series |
| `10` | Rated current: 5 = 50 A, 6 = 60 A, 7 = 75 A, 8 = 80 A, 10 = 100 A |
| `4` | Battery voltage: 4 = 12/24/36/48 V |
| `20` | PV maximum open-circuit voltage: 15 = 150 V, 20 = 200 V, 25 = 250 V |
| `N` | Common negative system |
| `C` | LCD screen |
| `G3` | 3rd generation of the IT-NC series |

The TIS series follows the same rule without the `NC G3` suffix (`TIS 6 4 15`).

### Packages

| Package | Content | ET-NC | IT-NC | TES | TIS |
|---|---|:---:|:---:|:---:|:---:|
| `epever_scc_Gen2_full.yaml` | Sub-device, Modbus controller, common entities, PV inputs | ✅ | ✅ | ✅ | ✅ |
| `epever_scc_Gen2_entities_sensor.yaml` | Rated data, battery, PV totals, statuses, BMS flags, parallel status, energy statistics, battery settings (read) | ✅ | ✅ | ✅ | ✅ |
| `epever_scc_Gen2_entities_number.yaml` | Battery settings (write) | ✅ | ✅ | ✅ | ✅ |
| `epever_scc_Gen2_entities_switch.yaml` | Charging ON/OFF | ✅ | ✅ | ✅ | ✅ |
| `epever_scc_Gen2_pv_1.yaml` | PV1 input, charging stage, day / night | ✅ | ✅ | ✅ | ✅ |
| `epever_scc_Gen2_pv_2.yaml` | PV1 + PV2 inputs, PV2 and relay statuses | `scc_pv_input: '2'` | `scc_pv_input: '2'` | `scc_pv_input: '2'` | `scc_pv_input: '2'` |
| `epever_scc_Gen2_load.yaml` | Load output: values, status, consumed energy, ON/OFF, control mode, light control, timers | ❌ | ✅ | ❌ | ✅ |
| `epever_scc_Gen2_battery_pack.yaml` | Lithium battery data received from the BMS | optional | optional | optional | optional |

The `full` package is included by every Gen2 root YAML. The other packages are included by the
root YAML of each series, as shown above.

The parallel charging status (`0x3206`) is only documented for ET-NC, but the 4 series support
parallel operation (up to 6 controllers): it is read on all of them.

### Why so many packages?

ESPHome reads consecutive registers in one single Modbus request. If one register of that request
does not exist on the controller, the whole request is rejected and **all** the values of the block
are lost. Each package therefore only contains registers that exist on the series that load it:
the PV2 and load registers are never requested from a controller that does not have them.

## Variables

| Variable | Gen1 | Gen2 | Description |
|---|:---:|:---:|---|
| `scc_id` | ✅ | ✅ | ID of the controller, prefix of all its IDs (no space). Must be the same in all the packages of one controller |
| `scc_name` | ✅ | ✅ | Name of the sub-device in Home Assistant |
| `scc_modbus_id` | ✅ | ✅ | ID of the Modbus bus |
| `scc_modbus_address` | ✅ | ✅ | Modbus address of the controller (default `1`) |
| `scc_update_interval` | ✅ | ✅ | Polling interval |
| `scc_input_number_mode` | ✅ | ✅ | `box` or `slider`. Gen2: also passed to the load package |
| `scc_pv_input` | | ✅ | Number of PV inputs of the model: `1` or `2` |
| `scc_overvoltage_disconnect_voltage` … `scc_discharging_limit_voltage` | ✅ | ✅ | The 12 battery voltage parameters, see below |

### Battery voltage parameters

The 12 voltage parameters are only used by the controller when the battery type is **User**.
They are written together in one request, each time one of them changes and at boot (restored
values). The root YAML gives the rules they must follow for a lithium battery.

The write happens 2 s after the last change (several changes in a row give one single write), and
never while one of the 12 values is still unknown.

All the settings are written with the Modbus function code `0x10` (write multiple registers), as
required by both protocol documents.

### Load output timers (Gen2, IT-NC and TIS)

| Entity | Purpose |
|---|---|
| Load Control Mode | Manual, Light ON/OFF, Light ON + Timer, Timer, Always ON, ON at Sunrise |
| Load Night Threshold Voltage, Load Night Delay | Night detection: PV voltage below the threshold for longer than the delay |
| Load Day Threshold Voltage, Load Day Delay | Day detection: PV voltage above the threshold for longer than the delay |
| Load Night Time | Length of the whole night |
| Load Working Duration 1 / 2 | Load output durations of the Light ON + Timer mode |
| Load Timer Periods, Load Timer 1 / 2 ON / OFF Hour and Minute | ON/OFF times of the Timer mode (one or two periods) |
| Load Manual Mode Default State | Manual mode: load state when the controller starts (default ON), applied at the next restart of the controller |
| Load (switch) | Manual mode: load ON/OFF |

Night Time and Working Durations are shown in minutes: the controller stores them as hours and
minutes in one register, the conversion is done by the package.

## RS485 connection

RJ45 pinout of the RS485 COM port (same on Gen1 and Gen2):

| Pin | Signal |
|:---:|---|
| 1, 2 | +5 VDC (do not use) |
| 3, 4 | RS485-B |
| 5, 6 | RS485-A |
| 7, 8 | GND |

Default communication settings: 115200 bps, 8N1, Modbus address 1.

## Known limitations

### Gen2

- **Partially tested on a real controller**: the packages are written from the protocol documents,
  then corrected and checked on a **TIS8425** (ARM firmware 1.08, DSP firmware 1.06) against the
  EPEVER Solar Guardian app: battery SOC, battery voltage parameters, load control mode, load
  ON/OFF, night / day thresholds and delays, load timers.
- **Load timers**: the seconds of the ON/OFF times (`0x902C`, `0x902F`, `0x9032`, `0x9035`) are
  not exposed.
- **Not exposed on purpose**:
  - *Communication port enable* (CPE, `0x904A`): when set OFF, the RS485 port (communication and
    +5 V) is turned off whenever there is no PV input and no charging, typically every night. The
    ESP32 then loses the controller, and switches off if it is powered by the RJ45 port.
  - *Factory reset* (coil `0x0007`).
- The charging status 2 register (`0x3203`: relays, MPPT status, dry contact, PV mode alarms) is
  only read with `scc_pv_input: '2'`, as it may not exist on single PV input models.
