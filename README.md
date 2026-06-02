# 💰 Expense Tracker MCP Server

A lightweight **Model Context Protocol (MCP)** server for tracking personal expenses. Built with [FastMCP](https://github.com/jlowin/fastmcp) and SQLite, it exposes tools that any MCP-compatible AI client (like Claude Desktop, Kiro, etc.) can use to add, list, and summarize expenses through natural language.

---

## ✨ Features

- **Add Expenses** — Log expenses with date, amount, category, subcategory, and optional notes
- **List Expenses** — Query expenses within any date range
- **Summarize Expenses** — Get category-wise spending totals for a date range
- **Category Resource** — Exposes a rich, hierarchical category taxonomy (20 categories, 100+ subcategories)
- **SQLite Storage** — Zero-config persistent storage with automatic database initialization
- **MCP Protocol** — Integrates seamlessly with any MCP-compatible AI assistant

---

## 📁 Project Structure

```
ExpenseTrackerMCPServer/
├── main.py              # MCP server with tool definitions
├── categories.json      # Category & subcategory taxonomy
├── expense.db           # SQLite database (auto-created)
├── pyproject.toml       # Project metadata
├── .python-version      # Python version (3.13)
├── .gitignore           # Git ignore rules
└── README.md            # This file
```

---

## 🚀 Getting Started

### Prerequisites

- **Python 3.13+**
- **[uv](https://docs.astral.sh/uv/getting-started/installation/)** (recommended) or pip

### Installation

1. **Clone the repository**

   ```bash
   git clone https://github.com/<your-username>/ExpenseTrackerMCPServer.git
   cd ExpenseTrackerMCPServer
   ```

2. **Create a virtual environment and install dependencies**

   Using `uv` (recommended):
   ```bash
   uv venv
   uv pip install fastmcp
   ```

   Or using pip:
   ```bash
   python -m venv .venv
   # Windows
   .venv\Scripts\activate
   # macOS/Linux
   source .venv/bin/activate
   pip install fastmcp
   ```

3. **Run the server**

   ```bash
   python main.py
   ```

   The MCP server will start and listen for connections via stdio transport.

---

## 🔧 MCP Client Configuration

To connect this server to an MCP-compatible client, add the following to your MCP configuration:

```json
{
  "mcpServers": {
    "expense-tracker": {
      "command": "python",
      "args": ["path/to/ExpenseTrackerMCPServer/main.py"],
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

> **Tip:** Replace `path/to/ExpenseTrackerMCPServer/main.py` with the absolute path to `main.py` on your system.

---

## 🛠️ Available Tools

### `add_expense`

Add a new expense entry to the database.

| Parameter     | Type   | Required | Description                    |
|---------------|--------|----------|--------------------------------|
| `date`        | string | ✅       | Date of expense (YYYY-MM-DD)   |
| `amount`      | number | ✅       | Expense amount                 |
| `category`    | string | ✅       | Category (see categories.json) |
| `subcategory` | string | ❌       | Subcategory for more detail    |
| `note`        | string | ❌       | Additional notes               |

**Example:**
```
Add an expense of ₹500 for groceries on 2025-06-01
```

---

### `list_expense`

List all expense entries within an inclusive date range.

| Parameter    | Type   | Required | Description              |
|--------------|--------|----------|--------------------------|
| `start_date` | string | ✅       | Start date (YYYY-MM-DD)  |
| `end_date`   | string | ✅       | End date (YYYY-MM-DD)    |

**Example:**
```
Show my expenses from 2025-06-01 to 2025-06-30
```

---

### `summarize`

Summarize expenses by category within an inclusive date range.

| Parameter    | Type   | Required | Description                          |
|--------------|--------|----------|--------------------------------------|
| `start_date` | string | ✅       | Start date (YYYY-MM-DD)              |
| `end_date`   | string | ✅       | End date (YYYY-MM-DD)                |
| `category`   | string | ❌       | Filter to a specific category        |

**Example:**
```
Summarize my spending for May 2025
```

---

## 📚 Available Resources

### `expense://categories`

Returns the full category taxonomy as JSON. This resource is automatically available to MCP clients for context when categorizing expenses.

**Categories include:** food, transport, housing, utilities, health, education, family_kids, entertainment, shopping, subscriptions, personal_care, gifts_donations, finance_fees, business, travel, home, pet, taxes, investments, misc

Each category has multiple subcategories for granular tracking.

---

## 📂 Category Taxonomy

The server ships with 20 top-level categories and 100+ subcategories:

| Category         | Example Subcategories                              |
|------------------|----------------------------------------------------|
| food             | groceries, dining_out, coffee_tea, delivery_fees   |
| transport        | fuel, public_transport, cab_ride_hailing, parking  |
| housing          | rent, maintenance_hoa, property_tax, repairs       |
| utilities        | electricity, water, internet_broadband, mobile     |
| health           | medicines, doctor_consultation, fitness_gym        |
| shopping         | clothing, electronics_gadgets, home_decor          |
| subscriptions    | saas_tools, cloud_ai, music_video, storage_backup  |
| investments      | mutual_funds, stocks, fd_rd, gold, crypto          |
| ...              | See `categories.json` for the full list            |

---

## 🗄️ Database Schema

The SQLite database (`expense.db`) is auto-created on first run with the following schema:

```sql
CREATE TABLE IF NOT EXISTS expenses (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    date TEXT NOT NULL,
    amount REAL NOT NULL,
    category TEXT NOT NULL,
    subcategory TEXT DEFAULT '',
    note TEXT DEFAULT ''
);
```

---




