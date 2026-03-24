## Instrument Registered in Redis

This module is responsible for:

- Parsing raw instrument files received from NSE & BSE
- Normalizing and combining them into a single `instruments.csv`
- Enriching instruments with derived fields (Instrument IDs, Index instruments, ELM%)
- Storing the final instruments data into **Redis** under a single key

It is designed to be run as a **CLI application** and can be safely re-run multiple times per day.

### InstrumentsRegisterInRedis — Folder Structure
```
`InstrumentsRegisterInRedis/
│
├── application.py                # Main CLI entry point
├── instrument_parser.py          # Core parsing & CSV generation logic
├── auto_run.bat                  # Windows helper script
├── ael_elm_updater.py            # Updates ELM% from AEL
├── common.py                     # Enums & shared constants
├── utils.py                      # Utility helpers (token generation, etc.)
│
├── config/
│   └── column_info.py            # Column definitions & rename mappings
│
├── parsers/                      # Exchange-specific parsers
│   ├── bse_parser.py
│   ├── bsecm_parser.py
│   ├── bsefo_parser.py
│   ├── nse_parser.py
│   ├── nsecm_parser.py
│   ├── nsefo_parser.py
│   └── indexes_parser.py
│
├── InstrumentFiles/              # Raw input files (exchange-provided)
│   ├── contract
│   ├── contract_1706
│   ├── contract_11082025
│   ├── EQD.csv
│   ├── indexes
│   ├── SCRIP
│   ├── security
│   ├── security_1706
│   └── security_11082025
│
├── Instruments/                  # Generated output
│   └── instruments.csv
│
├── __pycache__/
└── .gitignore`

```



### Root Files

| File | Description |
| --- | --- |
| `application.py` | Main CLI entry point |
| `instrument_parser.py` | Core parsing & CSV generation logic |
| `auto_run.bat` | Windows helper script |
| `ael_elm_updater.py` | Updates ELM% from AEL |
| `common.py` | Enums & shared constants |
| `utils.py` | Utility helpers (token generation, etc.) |

### Directories

| Directory | Description |
| --- | --- |
| `config/` | Column definitions & rename mappings |
| `parsers/` | Exchange-specific parser modules |
| `InstrumentFiles/` | Raw input files provided by exchanges |
| `Instruments/` | Generated output (e.g., `instruments.csv`) |

### Parsers

| File | Exchange |
| --- | --- |
| `bse_parser.py` | BSE |
| `bsecm_parser.py` | BSE Capital Market |
| `bsefo_parser.py` | BSE Futures & Options |
| `nse_parser.py` | NSE |
| `nsecm_parser.py` | NSE Capital Market |
| `nsefo_parser.py` | NSE Futures & Options |
| `indexes_parser.py` | Indexes |




## How to Run the Instrument Registration Module

!!! info
    The **InstrumentsRegisterInRedis** module is executed as a **CLI application** using `application.py`.  
    Since the target Linux server does **not have internet access**, all required Python dependencies must be downloaded on a Windows machine and transferred offline before execution.

---

### Step 1 — Prepare Python Dependencies (Windows Machine)

On a Windows system with internet access, create a directory to store offline packages and download all required dependencies listed in `requirements.txt`.

**Dependencies:**
- apscheduler
- requests
- redis
- pandas
- psutil
- cryptography
- pymysql
- sqlalchemy

Download the packages:
```bash
pip download -r requirements.txt -d offline_packages
```

This command downloads all required wheels and source distributions into the `offline_packages` directory.

---

### Step 2 — Transfer Dependencies to the Linux Server

Using WinSCP or any SCP-based tool:

1. Connect to the Linux server.
2. Copy the `offline_packages` directory to a suitable location, for example:
```
/opt/apps/offline_packages
```

---

### Step 3 — Install Dependencies on the Linux Server (Offline)

On the Linux server, navigate to the directory containing the offline packages and install them:
```bash
pip install --no-index --find-links=/opt/apps/offline_packages -r requirements.txt
```

!!! warning
    Ensure the correct Python environment is active before installing dependencies.

---

### Step 4 — Run the Application

Once all dependencies are installed, navigate to the project root directory and run the application:
```bash
python application.py
```

!!! success
    The application will parse instrument files, generate the consolidated `instruments.csv`, and register the final instrument data into Redis.

---

### Notes

- The application can be safely re-run multiple times per day.
- Existing Redis data will be updated based on the latest instrument files.
- Ensure Redis is running and accessible before execution.