#  Windows Service Monitoring Tool

This tool monitors the status of specified Windows services across multiple servers and sends alert emails if any service is not running. It is designed to be run periodically (e.g., every 15 minutes) using **Windows Task Scheduler**.

---

##  Features

- Monitors service status (Running, Stopped, Paused, etc.) across remote or local Windows machines.
- Sends HTML-formatted alert emails with service details and recovery instructions.
- Centralized logging with timestamped log files.
- Configurable via `.env` file.
- Uses shared libraries (`SharedLogger`, `O365Manager`) for logging and email.

---

##  Project Structure

```

ServiceMonitor/
├── monitor\_services.py      # Main monitoring script
├── .env                     # Configuration file (service list, email DL, etc.)
├── log/                     # Directory for log files
└── SharedLibraries/         # Path to shared modules (outside of this folder)

````

---

##  Setup Instructions

### 1. Prerequisites

- Python 3.7+
- `pywin32` package
- `python-dotenv` package
- Access to `SharedLogger` and `O365Manager` modules
- Email distribution list or mailbox for alerts
- Admin privileges to query services on remote servers

Install required packages:

```bash
pip install pywin32 python-dotenv
````

### 2.  Configure `.env`

Create a `.env` file in the root directory with the following format:

```dotenv
# Shared libraries and config
SHARED_LIBRARIES=E:\path\to\SharedLibraries
KSM_CONFIG=E:\path\to\SharedLibraries\ksm-config.json
KSM_CONFIG_SKIP_MODE_WARNING=TRUE

# Email distribution list
DL=someone@versantpower.com

# Logging
PROGRAM_NAME=ServiceMonitor

# Service Definitions (Format: SERVICE_<n>=<ServiceName>|<ServerName>)
SERVICE_1=MyWindowsService|SERVER01
SERVICE_2=AnotherService|SERVER02
...
```

 *Comment out any services you don’t want to monitor using `#`.*

---

##  How It Works

1. Loads configuration from `.env`.
2. Loops through each `SERVICE_<n>` entry.
3. Checks the status of each service using `win32serviceutil`.
4. If a service is **not running**, sends an email with:

   * Service name
   * Server name
   * Current status
   * Manual restart instructions
5. Logs all activity to a dated log file in the `log/` directory.

---

##  Sample Alert Email

```html
Subject: ALERT: CayentaMultispeak not running on MECISDEV01

Service 'CayentaMultispeak' is STOPPED on MECISDEV01.

Instructions:
- Open Services (services.msc)
- Start the service manually
- Or run: sc \\MECISDEV01 start "CayentaMultispeak"
```

---

##  Scheduling with Windows Task Scheduler

Run the script every 15 minutes:

1. Open Task Scheduler.
2. Create a new task.
3. Set trigger: *One Time, Repeat every 15 minutes Indefinitely*.
4. Action: Run `python.exe` with argument `\\PathToFolder\\main.py`.
5. Set working directory to the script folder.

---

##  Dependencies

* `pywin32`: Windows service management
* `python-dotenv`: Environment variable management
* `SharedLogger`: Custom logging utility (shared module)
* `O365Manager`: Custom email sending utility (shared module)

---

##  Permissions

* The user running the script must have permission to query service status on the target machines.

---

##  Author

**John McGinnes**
Versant Power

---

##  License

This script is internal use only. 
