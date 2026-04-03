---
title: How to Run SQL Server in Docker and Connect it with Azure Data Studio
slug: how-to-run-sql-server-in-docker-and-connect-it-with-azure-data-studio
date: 2026-04-03
tags: []
category: tech
---

## Creating a Students Table using Docker SQL Server and Azure Data Studio

In this section, I set up a **Microsoft SQL Server instance using Docker**, connected it to **Azure Data Studio**, and created a simple `Students` table. Running SQL Server in a container makes it easy to manage databases without installing them directly on your system.

---

### 1. Running SQL Server in Docker

First, start a SQL Server container using Docker.

```bash
docker run -e "ACCEPT_EULA=Y" \
-e "SA_PASSWORD=YourStrongPassword123" \
-p 1433:1433 \
--name sqlserver \
-d mcr.microsoft.com/mssql/server:2022-latest
```

**Explanation**

- `ACCEPT_EULA=Y` → Accepts the Microsoft SQL Server license agreement.
- `SA_PASSWORD` → Sets the password for the `sa` (system administrator) account.
- `-p 1433:1433` → Exposes SQL Server's default port to your local machine.
- `--name sqlserver` → Names the container.
- `-d` → Runs the container in detached mode.
- `mcr.microsoft.com/mssql/server:2022-latest` → Official SQL Server Docker image.

After running this command, SQL Server will be available at **localhost:1433**.

---

**Docker SQL Server Container Running**

![image01.png](./images/image01.png)

---

### 2. Connecting to SQL Server using Azure Data Studio

Next, connect to the running SQL Server instance using Azure Data Studio.

Steps:

1. Open **Azure Data Studio**
2. Click **New Connection**
3. Enter the following details:

| Setting | Value |
|-------|------|
| Server | `localhost` |
| Authentication Type | SQL Login |
| Username | `sa` |
| Password | Your SA password |

Once connected, the SQL Server instance will appear in the **Connections panel**.

---

**Azure Data Studio Connection**

![image02.png](./images/image02.png)

---

### 3. Creating the Students Table

After connecting to the database, run the following SQL query to create a table.

```sql
CREATE TABLE Students (
    StudentID INT PRIMARY KEY,
    Name VARCHAR(100),
    Age INT,
    Department VARCHAR(100)
);

**Explanation**

- `StudentID` → Unique identifier for each student.
- `Name` → Stores the student's name.
- `Age` → Stores the student's age.
- `Department` → Stores the department or program the student belongs to.

The `PRIMARY KEY` ensures each student record has a unique 

### 4. Verifying the Table

To confirm the table was created successfully, run:

```sql
SELECT * FROM Students;
```

This query will display all records stored in the `Students` table.

---

**Students Table Created**

![image03.png](./images/image03.png)

---