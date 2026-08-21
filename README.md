# Flights SQL Analysis

A SQL project that loads flight and aircraft data into a SQLite database and answers a series of analytical queries using joins, filtering, sorting, and aggregation.

## Overview

This notebook demonstrates core SQL skills by:
1. Setting up a SQLite database from raw CSV data
2. Writing queries to answer specific analytical questions about flight schedules and aircraft usage

## Setup

**Dependencies:**
- `ipython-sql`
- `sqlalchemy`
- `pandas`
- `sqlite3` (via SQLAlchemy raw connection)

**Data files required (place in the working directory):**
- `flights-2.csv`
- `aircrafts.csv`

**Environment:** Built and run in Google Colab (Python 3.12).

## How it works

1. Installs `ipython-sql` and creates a SQLite database (`new.db`) via `sqlalchemy.create_engine`
2. Opens a raw DBAPI2 connection and cursor for executing SQL directly
3. Loads `flights-2.csv` → `flights` table (893 rows)
4. Loads `aircrafts.csv` → `aircrafts` table (34 rows)
5. Runs a series of SQL queries against the two tables

## Table schemas (inferred)

**`flights`**

| Column | Description |
|---|---|
| Depart_Time | Departure time (stored as text, e.g. `'12:10'`) |
| Origin | Origin airport code |
| Arrival_Time | Arrival time |
| Destination | Destination airport code (SFO in sampled rows) |
| Flight | Flight number/carrier |
| Aircraft | Aircraft type code (joins to `aircrafts.Aircraft`) |
| Stops | Stop type (e.g. `Non-Stop`) |
| (numeric/duration columns) | Additional flight duration/distance fields |

**`aircrafts`**

| Column | Description |
|---|---|
| Aircraft | Aircraft type code |
| Seats | Seat capacity |

## Queries answered

| Query | Description |
|---|---|
| `q_2a` | Top 5 earliest-departing flights from LAS, sorted by `Depart_Time` ascending |
| `q_2b` | Top 5 latest-departing flights from LAS, sorted by `Depart_Time` descending |
| `q_3` | Distinct origin airports served by aircraft with exactly 150 seats (via `LEFT JOIN` on `flights`/`aircrafts`) |
| `q_4` | For JFK departures on wide-body aircraft (type codes containing `'7'`), flight count and total seat capacity per aircraft type |

## Known limitations

- **Time sorting is lexicographic, not chronological.** `Depart_Time` is stored as text (e.g. `'9:44'`, `'8:25'`), so `ORDER BY Depart_Time` sorts as strings rather than actual time values. This happens to look reasonable in some cases but isn't reliable for accurate chronological ordering.
- **LEFT JOIN nulls.** The joins in `q_3` and `q_4` use `LEFT JOIN` on aircraft type; any aircraft code in `flights` with no match in `aircrafts` would produce nulls for `Seats`, which could silently affect aggregate results depending on the data.

## Possible extensions

- Cast `Depart_Time`/`Arrival_Time` to proper time types for accurate chronological sorting
- Validate join coverage between `flights.Aircraft` and `aircrafts.Aircraft` to catch unmatched codes
- Add additional queries (e.g. average flight duration by route, busiest origin airports)
