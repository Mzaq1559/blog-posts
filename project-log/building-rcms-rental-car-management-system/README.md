---
title: Building RCMS — From PostgreSQL to SQL Server in a Rental Car App
slug: building-rcms-rental-car-management-system
date: 2026-06-09
excerpt: A DBMS lab project (FastAPI, vanilla JS, SQL Server) that I first wrote against PostgreSQL. Most of what I learned came from converting it.
tags: [FastAPI, SQL Server, PostgreSQL, Python, DBMS, Migration]
category: Project Log
cover: ./images/cover.png
---

<!-- COVER IMAGE: ./images/cover.png — The RCMS single-page UI (dark theme) on the vehicles or reservations tab, showing the table and status badges. Used as the hero image. -->

## What it is

The Rental Car Management System is my DBMS lab project: a FastAPI backend, a single `index.html` frontend with vanilla JavaScript, and a SQL Server database. You can see the fleet across branches, register customers, book a car for a date range, get an invoice generated automatically (18% tax on the rental subtotal), cancel a reservation, and mark an invoice as paid.

<!-- IMAGE: ./images/reservations-tab.png — Screenshot of the Reservations tab with a booking form and a list of active reservations. Place it here so the reader can see the workflow the rest of the post talks about. -->

The history is short and easy to follow. On May 30 nearly the whole first version was committed within hours: the file structure, the schemas, `main.py`, `database.py`, the frontend and a seed script. Then, on June 9, came the interesting part.

## The switch to SQL Server

The first version used PostgreSQL through `psycopg2`. On June 9 a commit called "chabges postgre sql to mssql" (typo mine) changed it to SQL Server through `pyodbc`. The commit doesn't say why. The coursework I was doing at the time was on SQL Server, which is what my earlier Docker posts are about, so that's probably it.

It was a bigger change than I expected. The commit is about 300 lines added and 200 removed, and almost none of it was about the app itself. Each of these had to be rewritten:

- **Placeholders:** `%s` became `?`
- **Auto-increment:** `SERIAL` became `INT IDENTITY(1,1)`, and `BOOLEAN`/`TRUE` became `BIT`/`1`
- **Timestamps:** `NOW()` became `GETDATE()`
- **String concatenation:** `v.make || ' ' || v.model` became `v.make + ' ' + v.model`
- **Joins:** `JOIN ... USING (customer_id)` isn't supported, so every join now spells out `ON a.x = b.x`
- **`RETURNING *`:** T-SQL doesn't have it. Every insert or update that used to return the new row now commits and then runs a second `SELECT`, using `SCOPE_IDENTITY()` to find the new row

`RETURNING *` was the one that changed the shape of the code most, because the endpoints returned whatever the query gave back.

The next one was subtler. `psycopg2` was set up with `RealDictCursor`, so every row came back as a dictionary and FastAPI could return it as JSON directly. `pyodbc` returns plain row tuples. I had to write two small helpers (`row_to_dict` and `rows_to_list`) that use `cursor.description` to build the dictionaries myself.

The connection handling changed too. The old code used `with get_conn() as conn`, and the new code uses `try/finally` with an explicit `conn.close()` on every endpoint. It's more repetitive, and I haven't refactored it into a dependency yet.

<!-- IMAGE: ./images/postgres-vs-tsql.png — A side-by-side screenshot or diff of one endpoint (e.g. create_reservation) before and after the migration, showing %s vs ?, RETURNING vs SCOPE_IDENTITY(). Place it after the list of changes; it's the clearest way to show the size of the rewrite. -->

### A mismatch I introduced in the same commit

The migration commit also replaced the schema with a T-SQL one, and that schema had renamed things: `customers.full_name` was split into `first_name` and `last_name`, `invoices.issued_at` became `created_at`, and the `category` column was dropped from `vehicles`. But `main.py` in the same commit still selected `full_name` and `issued_at`. The next three commits (remove the old schema and seed files, add an MSSQL seed, update the schema) look like the cleanup, and the current README points at `sql/schema_mssql.sql`. I haven't checked that final schema line by line, so I'm not going to claim it's fully consistent.

The same commit also contains `backend/.env` and `__pycache__/*.pyc` files. I had committed both. It's a local lab project and the values are for a local database, but it's exactly the habit I don't want to keep.

## Things I noticed reading it back

These aren't verified bugs, just what I see in the code now:

- **Booking rule.** A vehicle must be `available` to be booked, and booking sets it to `rented`. So a car with a reservation next month can't be booked for the week before it, even though the date-overlap check exists. The status flag and the date range are doing overlapping jobs.
- **Double-booking.** The README says the system prevents it, and for one user at a time it does, because the overlap check runs before the insert. There's no locking, so two simultaneous requests could both pass the check.
- **README limits.** No auth (every endpoint is public), no pagination, the API URL hardcoded in the frontend, and date validation that could be better in the UI.

I also remember fighting CORS errors and connecting DBeaver and Azure Data Studio to SQL Server on Ubuntu while getting this to run. The repo doesn't record those, so I've left the details out.

<!-- IMAGE: ./images/erd.png — Entity-relationship diagram of the final SQL Server schema: locations, vehicles, customers, reservations, invoices and maintenance, with the foreign keys. Place it near the end, after the schema discussion. Draw it from the real schema file, not from memory. -->

## What I learned

A database change isn't just a connection string. Placeholders, key generation, joins, returning rows and cursor behaviour are all part of what your application code assumes. If I did it again I'd keep the SQL in one place instead of writing it inline in every endpoint, so the next migration is a single-file change.

## Repo

| Link | Description |
|------|-------------|
| [GitHub: IDBS-Lab_Project](https://github.com/Mzaq1559/IDBS-Lab_Project) | FastAPI backend, SQL Server schema and seed, single-page frontend |
