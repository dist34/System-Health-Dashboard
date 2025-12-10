System Health Monitoring Dashboard
==================================

This project is a complete real-time system monitoring solution that I built using **Python**, **MySQL/MariaDB**, **Flask**, and **Power BI**. The system continuously collects live system metrics, stores them in a database, exposes API endpoints, and visualizes the data through a connected Power BI dashboard.

* * * * *

Project Overview
----------------

The project consists of three main components:

### 1\. **system_info.py -- Data Collection**

This script is responsible for collecting real-time system metrics using the `psutil` library.\
It gathers the following information:

-   CPU usage

-   Memory usage (in MB and percentage)

-   Disk usage (in MB and percentage)

-   Network bytes sent & received

-   Battery percentage and charging status

The script inserts these metrics into a MySQL/MariaDB database every **5 seconds**, and it automatically deletes entries older than **5 minutes** to maintain fast performance.

### 2\. **app.py -- Flask API**

This file handles the routing layer.\
It provides a simple Flask-based API with the following endpoints:

-   `/` → Health check

-   `/api/system` → Returns real-time system information

-   `/api/processes` → Returns top processes (optional based on implementation)

### 3\. **Power BI Dashboard**

Power BI is connected directly to the database using the **MariaDB connector**, since the standard MySQL connector does not support real-time streaming.

The dashboard updates automatically and visualizes:

-   CPU usage gauge

-   Disk utilization trend

-   Memory consumption graph

-   Network activity

-   Battery performance

-   Overall system performance summary

Screenshots of the live dashboard are available in the **Output Screenshots** folder.

* * * * *

Repository Structure
--------------------

`.
├── app.py
├── system_info.py
├── Dashboard/
├── Database/
├── Output Screenshots/
├── utils/
├── __pycache__/
└── .vscode/`

* * * * *

Database Schema
---------------

The project uses a database named **system_metrics** with a `metrics` table to store all system data.

`CREATE DATABASE system_metrics;
USE system_metrics;

CREATE TABLE metrics (
    id INT AUTO_INCREMENT PRIMARY KEY,
    timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    cpu_usage DOUBLE,
    memory_used DOUBLE,
    memory_percent DOUBLE,
    disk_used DOUBLE,
    disk_percent DOUBLE,
    bytes_sent DOUBLE,
    bytes_recv DOUBLE,
    battery_percent DOUBLE,
    battery_plugged BOOLEAN
);`

* * * * *

Code Used in the Project
------------------------

### app.py

`from flask import Flask, jsonify
from system_info import get_system_info, get_top_processes

app = Flask(__name__)

@app.route('/')
def home():
    return "System Monitoring API is running. Endpoints: /api/system , /api/processes"

@app.route('/api/system')
def system_api():
    return jsonify(get_system_info())

@app.route('/api/processes')
def processes_api():
    return jsonify(get_top_processes())

if __name__ == '__main__':
    app.run(debug=True)`

* * * * *

### system_info.py

`import psutil
import mysql.connector
import time

conn = mysql.connector.connect(
    host="localhost",
    port=3306,
    user="root",
    password="admin321",
    database="system_metrics"
)
cursor = conn.cursor()

def insert_metrics():

    cpu = psutil.cpu_percent(interval=1)
    mem = psutil.virtual_memory()
    disk = psutil.disk_usage('/')
    net = psutil.net_io_counters()

    try:
        battery = psutil.sensors_battery()
        battery_percent = battery.percent
        battery_plugged = battery.power_plugged
    except:
        battery_percent = None
        battery_plugged = None

    memory_used_mb = mem.used / (1024 * 1024)
    disk_used_mb = disk.used / (1024 * 1024)
    bytes_sent_mb = net.bytes_sent / (1024 * 1024)
    bytes_recv_mb = net.bytes_recv / (1024 * 1024)

    query = """
        INSERT INTO metrics
        (cpu_usage, memory_used, memory_percent, disk_used, disk_percent, bytes_sent, bytes_recv, battery_percent, battery_plugged)
        VALUES (%s,%s,%s,%s,%s,%s,%s,%s,%s)
    """
    values = (cpu, memory_used_mb, mem.percent, disk_used_mb, disk.percent,
              bytes_sent_mb, bytes_recv_mb, battery_percent, battery_plugged)
    cursor.execute(query, values)
    conn.commit()

    cursor.execute("DELETE FROM metrics WHERE timestamp < NOW() - INTERVAL 5 MINUTE")
    conn.commit()

    print("Live metrics updated at", time.strftime("%Y-%m-%d %H:%M:%S"))

while True:
    insert_metrics()
    time.sleep(5)`

* * * * *

How to Run the Project
----------------------

### Step 1: Install Dependencies

`pip install flask psutil mysql-connector-python`

### Step 2: Start the Data Collector

`python system_info.py`

This script must remain running in the background.

### Step 3: Start the Flask API

`python app.py`

### Step 4: Connect Power BI to MariaDB

1.  Open Power BI

2.  Select "Get Data" → "MariaDB"

3.  Enter your DB credentials

4.  Choose **DirectQuery** for real-time

5.  Load the `metrics` table

6.  Build or open the dashboard

* * * * *

Screenshots
-----------

All generated dashboard screenshots are stored in:

`Output Screenshots/`

They show:

-   Full dashboard

-   Disk, CPU, and network graphs

-   Memory usage

-   Battery performance

-   System summary cards

* * * * *

Summary
-------

This project gives a complete end-to-end monitoring system with:

-   Live metric collection

-   Database storage

-   REST API access

-   Real-time Power BI visualization

It is suitable for demonstrations, diagnostics, system monitoring tools, and projects involving integration between Python, databases, and BI tools.
