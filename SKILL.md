---
name: nocodb
description: Access and manage NocoDB databases via REST APIs. Free plans support bases, tables, fields, records, links, filters, sorts, and attachments. Enterprise plans add workspaces, views, scripts, teams, and collaboration features.
metadata:
  openclaw:
    requires:
      env:
        - NOCODB_TOKEN
        - NOCODB_URL
        - NOCODB_VERBOSE
      bins:
        - curl
        - jq
    primaryEnv: NOCODB_TOKEN
---

# NocoDB CLI

CLI for NocoDB API.

## Platform Support

- **Linux / macOS**: `scripts/nocodb.sh` (Bash, requires `curl` and `jq`)

All examples below invoke `scripts/nocodb.sh` (relative to this skill's directory) — there is no standalone `nc` binary, and `nc` must not be used as it is the netcat utility.

## Plan Requirements

**FREE PLANS:** Base, Table, Field, Record, Link, Attachment APIs, Fiter, Sorts APIs

**ENTERPRISE (self-hosted OR cloud-hosted):** Workspace, Workspace Collaboration APIs, Base Collaboration APIs, View, Script, Team, API Token APIs

## Setup

```bash
export NOCODB_TOKEN="your-api-token"
export NOCODB_URL="https://app.nocodb.com"  # optional, this is default
export NOCODB_VERBOSE=1                      # optional, shows resolved IDs
```

Get your API token: NocoDB → Team & Settings → API Tokens → Add New Token.

## Argument Order

Commands follow a hierarchical pattern. Arguments are always in this order:

```
WORKSPACE → BASE → TABLE → VIEW/FIELD → RECORD
```

You can use **names** (human-readable) or **IDs** (faster, from NocoDB).

**ID Prefixes:** `w`=workspace, `p`=base, `m`=table, `c`=column, `vw`=view

Examples:
- Name: `scripts/nocodb.sh record:list MyBase Users`
- ID: `scripts/nocodb.sh record:list pdef5678uvw mghi9012rst`

Set `NOCODB_VERBOSE=1` to see resolved IDs:
```bash
NOCODB_VERBOSE=1 scripts/nocodb.sh field:list MyBase Users
# → base: MyBase → pdef5678uvw
# → table: Users → mghi9012rst
```

## Quick Reference

```bash
# Workspace APIs (Enterprise only)
scripts/nocodb.sh workspace:list                                   # → wabc1234xyz

# Free plan APIs
scripts/nocodb.sh base:list wabc1234xyz                            # → pdef5678uvw
scripts/nocodb.sh table:list pdef5678uvw                           # → mghi9012rst
scripts/nocodb.sh field:list pdef5678uvw mghi9012rst               # → cjkl3456opq
scripts/nocodb.sh record:list pdef5678uvw mghi9012rst
scripts/nocodb.sh record:get pdef5678uvw mghi9012rst 31
scripts/nocodb.sh filter:list pdef5678uvw mghi9012rst vwmno7890abc

# View APIs (Enterprise only: self-hosted or cloud-hosted)
scripts/nocodb.sh view:list pdef5678uvw mghi9012rst                # → vwmno7890abc

# Filter syntax help
scripts/nocodb.sh where:help
```

## Commands

### Workspaces

**Note:** Workspace APIs and Workspace Collaboration APIs are available only with self-hosted **Enterprise** plans and cloud-hosted **Enterprise** plans.

```bash
scripts/nocodb.sh workspace:list                         # → wabc1234xyz
scripts/nocodb.sh workspace:get wabc1234xyz
scripts/nocodb.sh workspace:create '{"title":"New Workspace"}'
scripts/nocodb.sh workspace:update wabc1234xyz '{"title":"Renamed"}'
scripts/nocodb.sh workspace:delete wabc1234xyz
scripts/nocodb.sh workspace:members wabc1234xyz
scripts/nocodb.sh workspace:members:add wabc1234xyz '{"email":"user@example.com","roles":"workspace-creator"}'
scripts/nocodb.sh workspace:members:update wabc1234xyz '{"email":"user@example.com","roles":"workspace-viewer"}'
scripts/nocodb.sh workspace:members:remove wabc1234xyz '{"email":"user@example.com"}'
```

### Bases

```bash
scripts/nocodb.sh base:list wabc1234xyz                  # → pdef5678uvw
scripts/nocodb.sh base:get pdef5678uvw
scripts/nocodb.sh base:create wabc1234xyz '{"title":"New Base"}'
scripts/nocodb.sh base:update pdef5678uvw '{"title":"Renamed"}'
scripts/nocodb.sh base:delete pdef5678uvw
```

**Base Collaboration (Enterprise plans only)**

```bash
scripts/nocodb.sh base:members pdef5678uvw
scripts/nocodb.sh base:members:add pdef5678uvw '{"email":"user@example.com","roles":"base-editor"}'
scripts/nocodb.sh base:members:update pdef5678uvw '{"email":"user@example.com","roles":"base-viewer"}'
scripts/nocodb.sh base:members:remove pdef5678uvw '{"email":"user@example.com"}'
```

### Tables

```bash
scripts/nocodb.sh table:list pdef5678uvw                 # → mghi9012rst
scripts/nocodb.sh table:get pdef5678uvw mghi9012rst
scripts/nocodb.sh table:create pdef5678uvw '{"title":"NewTable"}'
scripts/nocodb.sh table:update pdef5678uvw mghi9012rst '{"title":"Customers"}'
scripts/nocodb.sh table:delete pdef5678uvw mghi9012rst
```

### Fields

```bash
scripts/nocodb.sh field:list pdef5678uvw mghi9012rst     # → cjkl3456opq
scripts/nocodb.sh field:get pdef5678uvw mghi9012rst cjkl3456opq
scripts/nocodb.sh field:create pdef5678uvw mghi9012rst '{"title":"Phone","type":"PhoneNumber"}'
scripts/nocodb.sh field:update pdef5678uvw mghi9012rst cjkl3456opq '{"title":"Mobile"}'
scripts/nocodb.sh field:delete pdef5678uvw mghi9012rst cjkl3456opq
```

Field types: SingleLineText, LongText, Number, Decimal, Currency, Percent, Email, URL, PhoneNumber, Date, DateTime, Time, SingleSelect, MultiSelect, Checkbox, Rating, Attachment, Links, User, JSON, etc.

### Views

**Note:** View APIs are available only on self-hosted and cloud-hosted **Enterprise** plans.

```bash
scripts/nocodb.sh view:list pdef5678uvw mghi9012rst      # → vwmno7890abc
scripts/nocodb.sh view:get pdef5678uvw mghi9012rst vwmno7890abc
scripts/nocodb.sh view:create pdef5678uvw mghi9012rst '{"title":"Active Users","type":"grid"}'
scripts/nocodb.sh view:update pdef5678uvw mghi9012rst vwmno7890abc '{"title":"Renamed"}'
scripts/nocodb.sh view:delete pdef5678uvw mghi9012rst vwmno7890abc
```

View types: grid, gallery, kanban, calendar

### Records

```bash
scripts/nocodb.sh record:list pdef5678uvw mghi9012rst                # page 1, 25 records
scripts/nocodb.sh record:list pdef5678uvw mghi9012rst 2 50           # page 2, 50 records
scripts/nocodb.sh record:list pdef5678uvw mghi9012rst 1 25 "(status,eq,active)"
scripts/nocodb.sh record:list pdef5678uvw mghi9012rst 1 25 "" '[{"field":"cjkl3456opq","direction":"desc"}]'

scripts/nocodb.sh record:get pdef5678uvw mghi9012rst 31
scripts/nocodb.sh record:get pdef5678uvw mghi9012rst 31 "name,email"

scripts/nocodb.sh record:create pdef5678uvw mghi9012rst '{"fields":{"name":"Alice"}}'
scripts/nocodb.sh record:update pdef5678uvw mghi9012rst 31 '{"status":"active"}'
scripts/nocodb.sh record:update-many pdef5678uvw mghi9012rst '[{"id":31,"fields":{"status":"done"}}]'

scripts/nocodb.sh record:delete pdef5678uvw mghi9012rst 31
scripts/nocodb.sh record:delete pdef5678uvw mghi9012rst '[31,32]'

scripts/nocodb.sh record:count pdef5678uvw mghi9012rst
scripts/nocodb.sh record:count pdef5678uvw mghi9012rst "(status,eq,active)"
```

### Linked Records

```bash
scripts/nocodb.sh link:list pdef5678uvw mghi9012rst cjkl3456opq 31
scripts/nocodb.sh link:add pdef5678uvw mghi9012rst cjkl3456opq 31 '[{"id":42}]'
scripts/nocodb.sh link:remove pdef5678uvw mghi9012rst cjkl3456opq 31 '[{"id":42}]'
```

### Filters & Sorts (View-level)

```bash
scripts/nocodb.sh filter:list pdef5678uvw mghi9012rst vwmno7890abc
scripts/nocodb.sh filter:create pdef5678uvw mghi9012rst vwmno7890abc '{"field_id":"cjkl3456opq","operator":"eq","value":"active"}'
scripts/nocodb.sh sort:list pdef5678uvw mghi9012rst vwmno7890abc
scripts/nocodb.sh sort:create pdef5678uvw mghi9012rst vwmno7890abc '{"field_id":"cjkl3456opq","direction":"desc"}'
```

### Attachments

```bash
scripts/nocodb.sh attachment:upload pdef5678uvw mghi9012rst 31 cjkl3456opq ./report.pdf
```

### Scripts

**Note:** Script APIs are available only on self-hosted and cloud-hosted **Enterprise** plans.

```bash
scripts/nocodb.sh script:list pdef5678uvw
scripts/nocodb.sh script:create pdef5678uvw '{"title":"My Script"}'
```

### Teams

**Note:** Team APIs require Enterprise plans (workspace teams are Enterprise-only).

```bash
scripts/nocodb.sh team:list wabc1234xyz
scripts/nocodb.sh team:create wabc1234xyz '{"title":"Engineering"}'
```

### API Tokens

**Note:** API Token APIs are available only with self-hosted **Enterprise** plans and cloud-hosted **Enterprise** plans.

```bash
scripts/nocodb.sh token:list
scripts/nocodb.sh token:create '{"title":"CI Token"}'
scripts/nocodb.sh token:delete tkn1a2b3c4d5e6f7g
```

## Where Filter Syntax

Run `scripts/nocodb.sh where:help` for full documentation.

### Basic Syntax

```
(field,operator,value)
(field,operator)                    # for null/blank/checked operators
(field,operator,sub_op)             # for date operators
(field,operator,sub_op,value)       # for date with value
```

### Common Operators

| Operator | Description | Example |
|----------|-------------|---------|
| eq | Equal | `(name,eq,John)` |
| neq | Not equal | `(status,neq,archived)` |
| like | Contains (% wildcard) | `(name,like,%john%)` |
| in | In list | `(status,in,active,pending)` |
| gt, lt, gte, lte | Numeric comparison | `(price,gt,100)` |
| blank, notblank | Null or empty | `(notes,blank)` |
| checked, notchecked | Boolean | `(is_active,checked)` |

### Date Operators

```bash
(created_at,eq,today)
(created_at,isWithin,pastWeek)
(created_at,isWithin,pastNumberOfDays,14)
(due_date,lt,today)                          # overdue
(event_date,eq,exactDate,2024-06-15)
```

### Combining Filters

**IMPORTANT:** Use `~and`, `~or`, `~not` (with tilde prefix)

```bash
(name,eq,John)~and(age,gte,18)
(status,eq,active)~or(status,eq,pending)
~not(is_deleted,checked)
(status,in,active,pending)~and(country,eq,USA)
```

### Complex Examples

```bash
# Active users created this month
"(status,eq,active)~and(created_at,isWithin,pastMonth)"

# Overdue high-priority tasks
"(due_date,lt,today)~and(priority,eq,high)~and(completed,notchecked)"

# Orders $100-$500 in pending/processing
"(amount,gte,100)~and(amount,lte,500)~and(status,in,pending,processing)"
```
