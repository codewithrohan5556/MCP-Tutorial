# 💸 Expense Tracker MCP Server

A lightweight [Model Context Protocol (MCP)](https://modelcontextprotocol.io/) server that lets Claude (or any MCP-compatible AI client) track, query, and summarize your personal expenses — all stored locally in a SQLite database.

---

## Features

- **Add expenses** with date, amount, category, subcategory, and an optional note
- **List expenses** over any date range
- **Summarize spending** by category (or drill into a specific one)
- **20 built-in categories** with granular subcategories (food, transport, housing, health, travel, investments, and more)
- Zero cloud dependency — everything lives in a single `expenses.db` file on your machine

---

## Quickstart

### Prerequisites

- Python 3.11+
- [`uv`](https://github.com/astral-sh/uv) (recommended) or `pip`

### Install & run

```bash
# Clone the repo
git clone https://github.com/your-username/expense-tracker-mcp.git
cd expense-tracker-mcp

# Install dependencies
uv sync          # or: pip install fastmcp

# Start the MCP server
uv run main.py   # or: python main.py
```

The server starts and exposes its tools over the MCP stdio transport, ready for any compatible client to connect.

---

## Connecting to Claude Desktop

Add the following block to your `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "expense-tracker": {
      "command": "uv",
      "args": ["run", "/absolute/path/to/main.py"]
    }
  }
}
```

Restart Claude Desktop. You should now see the expense tracker tools available in the tool picker.

---

## Tools

### `add_expense`

Add a new expense entry.

| Parameter | Type | Required | Description |
|-----------|------|----------|-------------|
| `date` | `string` | ✅ | Date in `YYYY-MM-DD` format |
| `amount` | `number` | ✅ | Expense amount |
| `category` | `string` | ✅ | Top-level category (see [Categories](#categories)) |
| `subcategory` | `string` | — | Optional subcategory |
| `note` | `string` | — | Optional free-text note |

**Example prompt to Claude:**
> *"Log ₹450 for an Uber ride today under transport > cab_ride_hailing"*

---

### `list_expenses`

List all expenses within an inclusive date range.

| Parameter | Type | Description |
|-----------|------|-------------|
| `start_date` | `string` | Start of range (`YYYY-MM-DD`) |
| `end_date` | `string` | End of range (`YYYY-MM-DD`) |

**Example prompt:**
> *"Show me all my expenses from June 1 to June 30"*

---

### `summarize`

Get a category-level spending summary for a date range. Optionally filter by one category.

| Parameter | Type | Description |
|-----------|------|-------------|
| `start_date` | `string` | Start of range (`YYYY-MM-DD`) |
| `end_date` | `string` | End of range (`YYYY-MM-DD`) |
| `category` | `string` | *(optional)* Filter to a single category |

**Example prompts:**
> *"Summarize my spending for May"*
> *"How much did I spend on food this month?"*

---

## Resources

### `expense://categories`

A read-only MCP resource that returns the full `categories.json` file as `application/json`. The file is read fresh on every request, so you can edit it without restarting the server.

---

## Categories

<details>
<summary>Click to expand all 20 categories</summary>

| Category | Subcategories |
|----------|--------------|
| `food` | groceries, fruits_vegetables, dairy_bakery, dining_out, coffee_tea, snacks, delivery_fees, other |
| `transport` | fuel, public_transport, cab_ride_hailing, parking, tolls, vehicle_service, other |
| `housing` | rent, maintenance_hoa, property_tax, repairs_service, cleaning, furnishing, other |
| `utilities` | electricity, water, gas, internet_broadband, mobile_phone, tv_dth, other |
| `health` | medicines, doctor_consultation, diagnostics_labs, insurance_health, fitness_gym, other |
| `education` | books, courses, online_subscriptions, exam_fees, workshops, other |
| `family_kids` | school_fees, daycare, toys_games, clothes, events_birthdays, other |
| `entertainment` | movies_events, streaming_subscriptions, games_apps, outing, other |
| `shopping` | clothing, footwear, accessories, electronics_gadgets, appliances, home_decor, other |
| `subscriptions` | saas_tools, cloud_ai, newsletters, music_video, storage_backup, other |
| `personal_care` | salon_spa, grooming, cosmetics, hygiene, other |
| `gifts_donations` | gifts_personal, charity_donation, festivals, other |
| `finance_fees` | bank_charges, late_fees, interest, brokerage, other |
| `business` | software_tools, hosting_domains, marketing_ads, contractor_payments, travel_business, office_supplies, other |
| `travel` | flights, hotels, train_bus, visa_passport, local_transport, food_travel, other |
| `home` | household_supplies, cleaning_supplies, kitchenware, small_repairs, pest_control, other |
| `pet` | food, vet, grooming, supplies, other |
| `taxes` | income_tax, gst, professional_tax, filing_fees, other |
| `investments` | mutual_funds, stocks, fd_rd, gold, crypto, brokerage_fees, other |
| `misc` | uncategorized, rounding, other |

</details>

To add or rename categories, edit `categories.json` directly — no restart needed.

---

## Project Structure

```
expense-tracker-mcp/
├── main.py            # MCP server — tools & resource definitions
├── expenses.db        # SQLite database (auto-created on first run)
├── categories.json    # Category taxonomy (editable at runtime)
├── pyproject.toml     # Project metadata & dependencies
├── uv.lock            # Locked dependency versions
└── test.py            # Quick local test / dev sandbox
```

---

## Tech Stack

- **[FastMCP](https://github.com/jlowin/fastmcp)** — ergonomic MCP server framework for Python
- **SQLite** — zero-config local database via Python's built-in `sqlite3`
- **uv** — fast Python package and project manager

---

## License

MIT
