# Pump Scheduling Optimizer - User Guide

## Overview

This system optimizes water pump operation schedules based on electricity price forecasts. It minimizes electricity costs while meeting daily water pumping requirements.

---

## Table of Contents

1. [Getting Started](#getting-started)
2. [Step-by-Step Operation Guide](#step-by-step-operation-guide)
3. [Parameter Settings Explained](#parameter-settings-explained)
4. [Understanding the Results](#understanding-the-results)
5. [System Logic and Constraints](#system-logic-and-constraints)
6. [Troubleshooting](#troubleshooting)

---

## Getting Started

### Online Access

Open your web browser and navigate to the deployed application URL. The system is ready to use immediately.

### Local Installation

If running locally, open a terminal and execute the following commands:

First, install the required dependencies by running: `pip install -r requirements.txt`

Then start the application by running: `streamlit run app_streamlit.py`

Finally, open your web browser and go to: `http://localhost:8501`

---

## Step-by-Step Operation Guide

### Step 1: Configure System Parameters

On the left sidebar, you will find the System Parameters section. Review and adjust these settings according to your pump station specifications:

Set the Flow Rate to match your pump's actual flow capacity in liters per second. The default value is 1050 L/s.

Set the Max Power to match your pump's power consumption in kilowatts. The default value is 1950 kW.

Leave Standby Power at 0 unless your system consumes power when idle.

Set Min Continuous Run to the minimum number of hours the pump should run continuously once started. The default is 4 hours. This prevents frequent start-stop cycles that can damage equipment.

Set Min Daily Run to the minimum number of hours the pump must operate each day. The default is 6 hours.

### Step 2: Fetch Price Data

In the left panel under Data Input, click the "Fetch AEMO Data" button.

The system will connect to the Australian Energy Market Operator (AEMO) website and download the latest electricity price forecasts for NSW.

Wait for the data to load. You will see a success message showing how many records were fetched. The data preview table below will display the fetched price data.

The system automatically fetches approximately 48 hours of data, covering today and tomorrow. Data before the current time is marked as "historical" and data after is marked as "forecast".

### Step 3: Set Daily Pumping Targets

After fetching data, the Daily Pumping Targets section will appear.

First, set the Default Target in megalitres (ML) per day. This applies to all days unless you specify otherwise.

Below the default, you will see input fields for each specific day in the data. You can set different targets for each day. For example, you might need more water on Monday than on Sunday.

### Step 4: Run the Optimization

Click the "Run Optimization" button.

The system will calculate the optimal pump schedule that minimizes electricity costs while meeting all your requirements and constraints.

Wait for the optimization to complete. This usually takes a few seconds.

### Step 5: Review the Results

Once optimization is complete, the right panel will display the results.

At the top, you will see a chart showing electricity prices over time. The blue dashed line represents historical prices (time periods that have already passed). The blue solid line represents forecast prices (future time periods). A red vertical line marks the current time. The orange filled areas show when the pump is scheduled to run.

Below the chart, you will find expandable sections for each day. Click on a day to see its details. Each day shows four key metrics: Run Time (total hours of operation), Volume (total water pumped in ML), Cost (total electricity cost in dollars), and Unit Cost (cost per megalitre in dollars).

Each day also includes a table listing every pump operation period with its start time, stop time, duration, volume, cost, and unit cost.

At the bottom, you will see the Total Summary showing aggregate statistics for all days combined.

### Step 6: Download the Results

Click "Download Excel" to get a spreadsheet containing the complete schedule and detailed data.

Click "Download CSV" to get the raw data including timestamps, prices, and pump status for each time period.

---

## Parameter Settings Explained

### Flow Rate (L/s)

This is the volumetric flow rate of your pump in liters per second. The system uses this to calculate how much water is pumped during each operating period. To find your pump's flow rate, check the pump specifications or measure it using a flow meter.

### Max Power (kW)

This is the electrical power consumption of your pump when running at full capacity, measured in kilowatts. The system uses this to calculate electricity costs. You can find this value on your pump's nameplate or in its specifications.

### Standby Power (kW)

This is the power consumed when the pump is not running but the system is still powered on. Most pump systems have zero standby power, so leave this at 0 unless you know otherwise.

### Min Continuous Run (h)

This is the minimum number of hours the pump must run continuously once it starts. Setting this prevents the pump from frequently turning on and off, which can cause mechanical stress, water hammer, and increased wear. A typical value is 4 hours.

### Min Daily Run (h)

This is the minimum number of hours the pump must operate each day, regardless of electricity prices. This ensures a minimum level of water supply even on expensive days.

### Daily Target (ML/day)

This is the target volume of water to pump each day, measured in megalitres (1 ML = 1,000,000 liters). The optimizer will ensure at least this much water is pumped each day.

---

## Understanding the Results

### The Price and Schedule Chart

The chart has two vertical axes. The left axis shows electricity price in dollars per megawatt-hour. The right axis shows pump status, where ON means the pump is running and OFF means it is stopped.

The horizontal axis shows time, spanning approximately 48 hours from the start of today to the end of tomorrow.

Historical data (before the current time) appears with dashed lines and gray shading. This represents what has already happened and cannot be changed.

Forecast data (after the current time) appears with solid lines and orange shading. This represents the optimized schedule for future operation.

### Daily Summary Metrics

Run Time tells you how many hours the pump will operate that day.

Volume tells you how much water will be pumped that day in megalitres.

Cost tells you the total electricity cost for that day in dollars.

Unit Cost tells you the cost per megalitre of water pumped. This is useful for comparing efficiency across different days.

### Schedule Table

Each row in the schedule table represents one continuous operation period.

Start Time is when the pump turns on.

Stop Time is when the pump turns off.

Duration is how long the pump runs during this period.

Volume is how much water is pumped during this period.

Cost is the electricity cost for this period.

Unit Cost is the cost per megalitre for this period.

---

## System Logic and Constraints

### Optimization Objective

The system minimizes total electricity cost while meeting all operational requirements. It does this by scheduling pump operation during periods when electricity prices are lowest.

### Price Data Source

The system fetches electricity price forecasts from the Australian Energy Market Operator (AEMO). These are the wholesale spot prices for the NSW region, published every 30 minutes. The system downloads the latest available forecast, which typically covers the next 24-36 hours.

### Peak Period Restriction

The pump is not allowed to operate during peak demand periods on weekdays. Specifically, operation is prohibited from 4:00 PM to 8:00 PM (16:00-20:00) Monday through Friday. This is because electricity prices are typically highest during these hours, and avoiding pump operation helps reduce costs significantly.

### Minimum Continuous Run Constraint

Once the pump starts, it must continue running for at least the specified minimum duration. The optimizer will not schedule short operation periods. This constraint also prevents starting the pump near the end of the data period, as there would not be enough time to complete the minimum run.

### Daily Volume Constraint

Each day must meet or exceed the specified pumping target. The optimizer ensures this by scheduling enough operation time during low-price periods.

### Daily Minimum Run Time Constraint

Each day must have at least the specified minimum hours of operation. This ensures basic water supply requirements are met.

### Time Zone Handling

The system automatically uses Australian Eastern Time (Sydney timezone). Even if the server is located in a different country, all times are correctly adjusted to match AEMO data and local operations.

### Cost Calculation

Electricity cost is calculated as: Price (dollars per MWh) multiplied by Power (kW) divided by 1000, multiplied by Time (hours).

For example, if the price is 80 dollars per MWh, the pump power is 1950 kW, and it runs for 4 hours, the cost is: 80 times 1.95 times 4 equals 624 dollars.

Unit cost (dollars per ML) is calculated by dividing the total cost by the total volume pumped.

---

## Troubleshooting

### Problem: Optimization fails with "Infeasible" status

This means the system cannot find a solution that satisfies all constraints.

Possible causes include: The daily target is too high to achieve within the available low-price periods. The minimum continuous run time is too long. There are too few hours available outside the restricted peak periods.

To fix this, try reducing the daily target, reducing the minimum continuous run time, or checking that your data covers enough hours.

### Problem: No data appears after clicking Fetch AEMO Data

Check your internet connection. The system needs to access the AEMO website to download price data.

The AEMO website may be temporarily unavailable. Wait a few minutes and try again.

### Problem: The schedule shows operation during unexpected times

The optimizer chooses the lowest-cost periods that satisfy all constraints. If prices are similar across many hours, the schedule may appear different from what you expect.

Check the price chart to verify that operation is indeed scheduled during lower-price periods.

### Problem: Unit cost seems too high

High unit costs typically result from operating during high-price periods. This happens when the daily target requires more pumping than can be done during low-price hours.

Consider reducing the daily target or accepting higher costs during high-demand days.

---

## Important Notes

This system provides recommended schedules only. Actual pump operation should consider real-time factors such as reservoir levels, pipe network pressure, equipment maintenance needs, and emergency situations.

The electricity prices are forecasts and may differ from actual prices. However, AEMO forecasts are generally reliable for the next 24-48 hours.

All times displayed are in Australian Eastern Time (AEST/AEDT).

---

For technical support, please contact your system administrator.

