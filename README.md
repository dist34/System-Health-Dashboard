⚙️ System Health Monitoring Dashboard
=====================================

This project is a **real-time system performance monitoring solution** that I built using **Python**, **Flask**, **MySQL/MariaDB**, and **Power BI**.\
It continuously collects system metrics, stores them in a database, exposes API endpoints for external use, and displays everything inside a **live-updating Power BI dashboard**.

* * * * *

📂 Project Overview
-------------------

### 🟦 1. system_info.py --- *Data Collector*

This script runs continuously in the background and is responsible for:

-   Reading live system metrics

    -   CPU usage

    -   Memory usage

    -   Disk usage

    -   Network bytes sent/received

    -   Battery percentage and charging status

-   Inserting the collected data into the database every **5 seconds**

-   Automatically clearing older records to keep only the latest 5 minutes of data

-   Acting as the backbone of the live streaming pipeline for Power BI

This component ensures Power BI always receives fresh, real-time values.

* * * * *

### 🟧 2. app.py --- *Flask API Layer*

This file serves as the routing and API interface for the project.

It provides:

-   A root endpoint confirming the API is running

-   An endpoint that returns current system information

-   An endpoint that returns active or top processes (based on configuration)

This allows external tools or scripts to retrieve live system metrics without interacting directly with the database.

* * * * *

### 🟨 3. Database (MySQL/MariaDB) --- *Data Storage Layer*

All system metrics collected by `system_info.py` are stored here.

Key responsibilities:

-   Storing time-stamped system performance data

-   Providing live data source for Power BI through the **MariaDB DirectQuery connector**

-   Maintaining up-to-date information by automatically removing older entries

**Screenshot of Database Table:**

* * * * *

### 🟩 4. Power BI Dashboard --- *Real-Time Visualization*

Power BI is connected to the MariaDB database in **DirectQuery mode**, enabling near real-time updates.

The dashboard displays:

-   CPU performance (gauge or line chart)

-   Memory consumption

-   Disk utilization trends

-   Network activity (sent/received)

-   Battery behavior

-   Overall system summary and performance indicators

This creates a fully interactive monitoring panel that updates automatically.

* * * * *

📁 Repository Structure
-----------------------

`.
├── app.py                     # API routing layer
├── system_info.py             # Real-time data collector
├── Dashboard/                 # Power BI files
├── Database/                  # SQL scripts / schema files
├── Output Screenshots/        # Dashboard and database images
├── utils/                     # Helper modules
└── .vscode/                   # Development environment settings`

* * * * *

🚀 How the System Works (High-Level Flow)
-----------------------------------------

1.  **system_info.py** collects system metrics every few seconds

2.  Data is stored in the **metrics** table inside MySQL/MariaDB

3.  **Power BI** reads this data using the MariaDB connector (DirectQuery)

4.  The dashboard updates automatically in near real-time

5.  **app.py** exposes the data through clean API endpoints for external use

* * * * *

📝 Purpose of the Project
-------------------------

This system demonstrates:

-   Real-time data ingestion

-   Database-backed monitoring

-   API-based access to live metrics

-   Integration between Python and Power BI

-   Automated data refresh cycles

-   System diagnostics and performance visualization

It can be extended into:

-   Enterprise monitoring tools

-   IoT dashboards

-   DevOps system health panels

-   Remote server monitoring

-   Multi-device analytics

* * * * *

📸 Additional Screenshots
-------------------------

You can find dashboard images and visual outputs inside:

`Output Screenshots/`
