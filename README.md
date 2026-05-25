# ExpenseTracker MCP Server

A lightweight, async expense tracking server built with [FastMCP](https://gofastmcp.com), using SQLite for local storage. Designed to be used as an MCP (Model Context Protocol) server with AI assistants like Claude.

---

## Features

- Add expenses with date, amount, category, subcategory, and notes
- List expenses within a date range
- Summarize expenses by category
- Persistent local SQLite database stored in the project directory
- Async support via `aiosqlite`
- Runs over HTTP for remote MCP access

---

## Project Structure

```
test-remote-server/
├── main.py            # Main server file
├── categories.json    # (Optional) Custom expense categories
├── expenses.db        # Auto-created SQLite database
└── README.md
```

---

## Requirements

- Python 3.10+
- [uv](https://docs.astral.sh/uv/) package manager

### Dependencies

```
fastmcp
aiosqlite
```

---

## Setup & Installation

**1. Clone the repository**
```bash
git clone <your-repo-url>
cd test-remote-server
```

**2. Install dependencies using uv**
```bash
uv sync
```

**3. Run the server**
```bash
uv run main.py
```

On successful startup you should see:
```
Database path: /your/project/path/expenses.db
Database initialized successfully with write access
DB exists: True
INFO: Application startup complete.
```

The server runs at: `http://0.0.0.0:3001/mcp`

---

## MCP Tools

### `add_expense`
Add a new expense entry.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `date` | string | ✅ | Date in `YYYY-MM-DD` format |
| `amount` | float | ✅ | Expense amount |
| `category` | string | ✅ | Expense category |
| `subcategory` | string | ❌ | Optional subcategory |
| `note` | string | ❌ | Optional note |

**Example:**
```json
{
  "date": "2025-05-25",
  "amount": 250.00,
  "category": "Food & Dining",
  "subcategory": "Lunch",
  "note": "Team lunch"
}
```

---

### `list_expenses`
List all expenses within a date range.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `start_date` | string | ✅ | Start date `YYYY-MM-DD` |
| `end_date` | string | ✅ | End date `YYYY-MM-DD` |

---

### `summarize`
Summarize total spending by category within a date range.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `start_date` | string | ✅ | Start date `YYYY-MM-DD` |
| `end_date` | string | ✅ | End date `YYYY-MM-DD` |
| `category` | string | ❌ | Filter by a specific category |

---

## MCP Resource

### `expense:///categories`
Returns the list of available expense categories as JSON.

Default categories:
- Food & Dining
- Transportation
- Shopping
- Entertainment
- Bills & Utilities
- Healthcare
- Travel
- Education
- Business
- Other

To customize, create a `categories.json` file in the project directory:
```json
{
  "categories": [
    "Food & Dining",
    "Rent",
    "Freelance",
    "Subscriptions"
  ]
}
```

---

## Connecting to Claude

To use this server with Claude, add it as an MCP server in your Claude settings:

```
URL: http://localhost:3001/mcp
```

Once connected, Claude can add, list, and summarize your expenses through natural conversation.

---

## Troubleshooting

### Port already in use
If you see `[Errno 10048]` on Windows, another process is using port 3001. Kill it with:
```cmd
for /f "tokens=5" %a in ('netstat -ano ^| findstr :3001') do taskkill /PID %a /F
```
Then restart the server.

To use a different port, change this line in `main.py`:
```python
mcp.run(transport="http", host="0.0.0.0", port=3002)
```

### Database not created
The `expenses.db` file is created in the same directory as `main.py`. Make sure you're running the server with `uv run main.py` from the project folder, and that the folder has write permissions.

---

