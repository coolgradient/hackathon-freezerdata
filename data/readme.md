# Lucend Hackathon — Dataset Documentation

You have chosen one of two challenges:

| Challenge | Goal |
|---|---|
| **Predict Setpoint Effect** | Given known setpoint changes, predict how the system responds over time. |
| **Classify Error Type** | Given sensor readings, classify which type of malfunction is occurring — or detect that none is. |

---

## The Physical System

Data comes from a physical cooling system with an extensive sensor network. There are two data sources that differ significantly in scope:

| Source | Description | Signals |
|---|---|---|
| **PLC** | Directly connected to this test setup. Rich, high-resolution data specific to this machine. | Temperature, pressure, fan control, valve states, coolant weight, and more. |
| **Freezerdata Platform** | A cloud platform shared across multiple (non-test) installations from the same company. | Primarily temperature and energy — a subset of what the PLC captures. |

> **Important:** PLC data is richer but unique to this test installation. Freezerdata Platform data is less detailed but representative of how real-world production installations are monitored.

---

## Data Sources — Key Columns

You don't need to use all columns, but here is an overview of what each source contains.

### Freezerdata Platform
`Date, Refrigerated_1, Refrigerated_2, HotGasPipe, LiquidPipe, SuctionPipe, Environment, EnergyKWh, Door`

Temperature readings across key points in the cooling circuit, plus energy consumption and door state.

### PLC
`Time, Tankdruk, Verdampingsdruk, Verdampingstemperatuur, Condensatiedruk, Condensatietemperatuur, ...`

A much wider set of signals including pressures, temperatures, fan control, valve positions, compressor state, coolant weight, and fault indicators. See the CSV headers for the full list.

### Setpoint Experiments (added to both sources)
`setpoint_min, setpoint_max`

---

## ⚠️ Datetime Index Differences

The PLC, Freezerdata Platform, and merged files all use datetime-based indexes, but the **column name and format differ between sources**. Check the index column carefully when loading files — especially for the merged datasets.

---

## File Naming Convention

Files follow this pattern:

```
dd_mm_yyyy_[plc | freezerdata | merged].csv
dd_mm_yyyy-dd_mm_yyyy_[plc | freezerdata | merged].csv   ← for date ranges
```

---

## Folder Structure

```
📦 Hackathon Data
├── 📂 Data Normal
├── 📂 Data Setpoints
├── 📂 Data Errors
│   ├── 📂 Condensor Blocked
│   ├── 📂 Leakage
│   └── 📂 Evaporator Blocked
└── 📂 Data Unlabeled
```

---

## 📂 Data Normal

Baseline data from the system running under normal, healthy conditions. Useful for understanding typical system behaviour and as a training baseline.

| # | Source | Notes |
|---|---|---|
| 4 files | PLC | Different date ranges |
| 1 file | Freezerdata Platform | — |
| 1 file | Merged (PLC + Freezerdata) | Overlapping date range between one PLC file and the Freezerdata file |

---

## 📂 Data Setpoints

Data from a controlled experiment where temperature setpoints were changed at known intervals. Both PLC and Freezerdata Platform files cover the same period.

| # | Source |
|---|---|
| 1 file | PLC |
| 1 file | Freezerdata Platform |
| 1 file | Merged (PLC + Freezerdata) |

All three files include the columns `setpoint_min` and `setpoint_max`.

### Setpoint Change Log

| Timestamp | setpoint_min | setpoint_max |
|---|---|---|
| 22-05-2023 17:39 | 12 | 14 |
| 22-05-2023 19:33 | 14 | 16 |
| 22-05-2023 20:32 | 16 | 18 |
| 22-05-2023 21:35 | 18 | 20 |
| 22-05-2023 22:40 | 20 | 20 |
| 22-05-2023 23:45 | 15 | 16 |
| 23-05-2023 09:02 | 16 | 17 |
| 23-05-2023 10:06 | 17 | 18 |
| 23-05-2023 11:06 | 18 | 19 |
| 23-05-2023 12:15 | 16 | 18 |

---

## 📂 Data Errors

Data from three types of deliberately induced malfunctions. All files in this folder are **PLC data** unless noted otherwise. Defrost cycles have been removed from the condensor and evaporator datasets.

---

### 📂 Condensor Blocked

The condenser airflow was progressively restricted. Three test runs from different dates.

| # | Source |
|---|---|
| 3 files | PLC |

**Key column:**

| Column | Description |
|---|---|
| `Condenser schuif` | Condenser opening percentage. `100` = fully open (no blockage), `0` = fully closed (complete blockage). |

---

### 📂 Leakage

A coolant leak scenario. One test run.

| # | Source |
|---|---|
| 1 file | PLC |

**Key column:**

| Column | Description |
|---|---|
| `Gewicht Opslagtank` | Weight of the coolant storage tank. A steadily decreasing value indicates coolant is leaking from the system. |

---

### 📂 Evaporator Blocked

The evaporator airflow was progressively blocked using cardboard. Three test runs from different dates.

| # | Source |
|---|---|
| 3 files | PLC |

**Key column:**

| Column | Description |
|---|---|
| `Verdamperblokade` | Blockage level code. See table below. |

**Blockage level reference:**

| Value | Blockage level |
|---|---|
| `0` | 100% blocked |
| `3` | 70% blocked |
| `4` | 60% blocked |
| `5` | 50% blocked |

> **Note:** A value of `0` means **maximum blockage**, not zero blockage. This is an artifact of how the experiment was logged.

---

## 📂 Data Unlabeled

A large dataset from the Freezerdata Platform. The type and number of events in this data are unknown.

| # | Source | Labels |
|---|---|---|
| 1 file | Freezerdata Platform | None |

Suitable for unsupervised exploration or as a test set for models trained on the labeled data above.

---

## Additional Data

During the hackathon, the following may be available on request:

- **Live data from the Freezerdata Platform** — fresh pulls during the event are possible.
- **Direct access to the physical system** — you can run your own experiments on the cooling setup.
- **Additional datasets** — more data is available, though much of it is partially unlabeled.

Ask the Lucend team if you want any of the above.