# 📦 SQL Server Schedule Backup and Cleanup Using SQL Server Agent on Windows Server

This guide describes how to **schedule full backups and weekly cleanup** for SQL Server databases using **SQL Server Agent** and **Maintenance Plans** on Windows Server.

---

## ✅ Prerequisites

Make sure the following folders exist:

```plaintext
D:\SQLBackups\SQLDailyBackupsFiles
````

```plaintext
D:\SQLBackups\SQLReportFiles
````

You can create them manually or using PowerShell:

```powershell
New-Item -ItemType Directory -Path "D:\SQLBackups\SQLDailyBackupsFiles" -Force
```

```powershell
New-Item -ItemType Directory -Path "D:\SQLBackups\SQLReportFiles" -Force
```

---

## 🔹 Step-by-Step Guide

### 🟢 Step 1: Start SQL Server Agent

1. Login to **SQL Server Management Studio**.
2. On the left menu bar, find **SQL Server Agent**, right-click and select **Start**.
3. Wait until it starts completely.

---

### 🟢 Step 2: Set SQL Server Agent to Start Automatically

1. Open the **Run** dialog: `Win + R`
2. Type `services.msc` and hit Enter.
3. Find `SQL Server Agent (MSSQLSERVER)`, right-click → **Properties**
4. Set **Startup type** to `Automatic` → Click **Apply** → OK

---

### 🟢 Step 3: Create Scheduled Backup Plan

1. In SSMS, expand the **Management** node at the bottom of the Object Explorer.
2. Right-click on **Maintenance Plans** → **Maintenance Plan Wizard** → Click **Next**
3. Change the name to:

   ```
   ScheduleDailyBackupPlan
   ```
4. Run as: `SQL Server Agent service account`
5. Choose: `Single schedule for the entire plan or no schedule`

---

### 🔧 Backup Schedule

* Click **Change**
* **Frequency**:

  * Occurs: `Daily`
  * Recurs every: `1` day(s)
* **Daily frequency**:

  * Occurs once at: `21:00:00`
* Duration:

  * Select: `No end date`
* Click **OK** → **Next**

---

### 🔧 Backup Task Configuration

1. Tick: `Back Up Database (Full)` → **Next**
2. Confirm `Back Up Database (Full)` it's selected again → **Next**
3. Choose: `These Databases` → select all databases to backup → Click **OK**
4. Under **Destination**:

   * Select: `Create a backup file for every database`
   * Backup location:

     ```
     D:\SQLBackups\SQLDailyBackupsFiles
     ```
   * Backup file extension:

     ```
     bak
     ```
5. Click **Next**
6. Tick: `Write a report to a text file`

   * Report path:

     ```
     D:\SQLBackups\SQLReportFiles
     ```
7. Click **Next** → **Finish**
8. Check for success message.
9. Click **Close**

---

### 🧪 Verify Backup Works

1. Go to **Maintenance Plans**
2. Right-click `ScheduleDailyBackupPlan` → **Execute**
3. Check for success message.
4. Click **Close**
5. Confirm `.bak` files are created in:

   ```
   D:\SQLBackups\SQLDailyBackupsFiles
   ```

---

## 🧹 Weekly Cleanup Task

Now that the backups are working, schedule a weekly cleanup of old `.bak` files.

---

### 🟢 Step 1: Create Cleanup Plan

1. Go to **Maintenance Plans** → Right-click → **Maintenance Plan Wizard**
2. Rename the plan to:

   ```
   CleanUpInWeek
   ```

---

### 🔧 Cleanup Schedule

* Click **Change**
* **Frequency**:

  * Occurs: `Daily`
* **Daily frequency**:

  * Occurs once at: `22:00`
* Click **OK** → **Next**

---

### 🔧 Cleanup Task Configuration

1. Tick: `Maintenance Cleanup Task` → **Next**
2. Confirm `Maintenance Cleanup Task` is selected → **Next**
3. Choose:

   * Task type: `Backup files`
   * Search folder:

     ```
     D:\SQLBackups\SQLDailyBackupsFiles
     ```
   * File extension: `bak` *(without the dot)*
   * Tick: `Delete files based on the age of the file at task run time`
   * Set: `1 week(s)`
4. Click **Next**
5. Tick: `Write a report to a text file`

   * Report path:

     ```
     D:\SQLBackups\SQLReportFiles
     ```
6. Click **Next** → **Finish**

---

## ✅ Final Verification

* Run both maintenance plans manually once to test.
* Make sure:

  * `.bak` files are created daily
  * Files older than 1 week are deleted
  * Reports are saved in:

    ```
    D:\SQLBackups\SQLReportFiles
    ```

---

## 📝 Notes

* Do **not** include `.bak` when specifying the extension for cleanup.
* Adjust the schedule times to your server’s maintenance window.

---
