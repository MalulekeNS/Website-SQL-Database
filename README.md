from pathlib import Path

# Define the README content
readme_content = """# 📦 `teksanja_wp667` Database Schema

![MariaDB](https://img.shields.io/badge/MariaDB-10.3.39-blue.svg?logo=mariadb&logoColor=white)
![PHP](https://img.shields.io/badge/PHP-7.2.30-777BB4.svg?logo=php&logoColor=white)
![phpMyAdmin](https://img.shields.io/badge/phpMyAdmin-5.1.1-orange.svg?logo=phpmyadmin&logoColor=white)
![License](https://img.shields.io/badge/license-MIT-green.svg)
![Status](https://img.shields.io/badge/status-stable-brightgreen)

---

## 🧩 Overview

The **`teksanja_wp667`** database represents a **WordPress / WooCommerce Action Scheduler schema** — exported via phpMyAdmin.  
It contains essential structures used by WordPress plugins such as **WooCommerce**, **WPForms**, and **AIOSEO** to manage **background and recurring tasks**.

Examples of tasks managed include:
- Cleaning up draft orders  
- Generating email summaries  
- Pruning SEO caches  
- Refreshing order count caches  

These are automatically triggered via hooks and executed through the WordPress cron system.

---

## 🗄️ Table: `wps2_actionscheduler_actions`

| Column | Type | Description |
|--------|------|-------------|
| `action_id` | BIGINT(20) UNSIGNED | Unique identifier for each scheduled task. |
| `hook` | VARCHAR(191) | The WordPress hook that triggers this action. |
| `status` | VARCHAR(20) | Current status (`pending`, `complete`, `failed`, etc.). |
| `scheduled_date_gmt` | DATETIME | The UTC timestamp for execution. |
| `scheduled_date_local` | DATETIME | The local server timestamp. |
| `args` | VARCHAR(191) | JSON-encoded arguments passed to the hook. |
| `schedule` | LONGTEXT | Serialized PHP object that defines recurrence. |
| `group_id` | BIGINT(20) UNSIGNED | Group reference for related tasks. |
| `attempts` | INT(11) | Number of execution attempts made. |
| `last_attempt_gmt` | DATETIME | UTC timestamp of last attempt. |
| `last_attempt_local` | DATETIME | Local timestamp of last attempt. |
| `claim_id` | BIGINT(20) UNSIGNED | Claim reference for multi-threaded jobs. |
| `extended_args` | VARCHAR(8000) | Extended JSON arguments for additional data. |
| `priority` | TINYINT(3) UNSIGNED | Task execution priority (default = 10). |

---

## 🧭 Entity-Relationship (ER) Diagram

```mermaid
erDiagram
    WPS2_ACTIONSCHEDULER_ACTIONS {
        BIGINT action_id PK "Primary Key"
        VARCHAR hook "Action hook name"
        VARCHAR status "Action status (pending, complete, failed)"
        DATETIME scheduled_date_gmt "Scheduled UTC"
        DATETIME scheduled_date_local "Scheduled Local"
        VARCHAR args "Encoded arguments"
        LONGTEXT schedule "Serialized recurrence"
        BIGINT group_id "Group reference"
        INT attempts "Execution attempts"
        DATETIME last_attempt_gmt "Last UTC attempt"
        DATETIME last_attempt_local "Last Local attempt"
        BIGINT claim_id "Claim reference"
        VARCHAR extended_args "Extended metadata"
        TINYINT priority "Priority order"
    }
    
    WPS2_ACTIONSCHEDULER_GROUPS {
        BIGINT group_id PK "Group Identifier"
        VARCHAR slug "Group slug"
        VARCHAR description "Group description"
    }

    WPS2_ACTIONSCHEDULER_CLAIMS {
        BIGINT claim_id PK "Claim Identifier"
        BIGINT worker_id "Worker reference"
        DATETIME date_created "Claim creation timestamp"
    }

    WPS2_ACTIONSCHEDULER_LOGS {
        BIGINT log_id PK "Log Identifier"
        BIGINT action_id FK "Related action"
        DATETIME log_date_gmt "Log date (UTC)"
        LONGTEXT message "Execution message"
    }

    WPS2_ACTIONSCHEDULER_ACTIONS ||--o{ WPS2_ACTIONSCHEDULER_LOGS : "has many logs"
    WPS2_ACTIONSCHEDULER_GROUPS ||--o{ WPS2_ACTIONSCHEDULER_ACTIONS : "contains actions"
    WPS2_ACTIONSCHEDULER_CLAIMS ||--o{ WPS2_ACTIONSCHEDULER_ACTIONS : "claims actions"
