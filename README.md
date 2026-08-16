# AI Telegram Finance

`choa` is a Telegram financial assistant built as a multi-agent AI system. Specialized agents process transactions, request missing information, analyze financial data, and prepare structured responses through a shared router.

## Features

- Multi-agent request routing
- Transaction recognition and accounting
- Financial analysis and CFS reporting
- Telegram webhook integration
- HeyGen avatar integration
- Google Sheets synchronization
- Configuration through environment variables
- Dependency management with `uv`

## Architecture

The application separates Telegram transport, routing, financial agents, external integrations, and logging into independent modules.

```text
User → Telegram bot → Router → Specialized agent → Response
```

The main application code is located in `service/`:

- `service/telegram/bot.py` — Telegram webhook server and message handling
- `service/core_and_router.py` — request routing and agent orchestration
- `service/accounting.py` — transaction processing
- `service/analyze.py` — financial analysis
- `service/cfs.py` — cash-flow statement generation
- `service/google_sheets.py` — Google Sheets integration
- `service/avatar.py` — HeyGen integration
- `service/logging_setup.py` — application logging

## Requirements

- Python 3.13 or newer
- [`uv`](https://docs.astral.sh/uv/)
- Telegram bot token
- OpenRouter API key
- HeyGen API key
- Public HTTPS URL for the Telegram webhook
- Google Cloud service-account credentials for Google Sheets synchronization

## Installation

### 1. Clone the repository

```bash
git clone https://github.com/lambda19-auto/choa.git
cd choa
```

### 2. Install dependencies

Create the virtual environment and install the locked dependencies:

```bash
uv sync
```

You can run commands through `uv run` without activating the environment. To activate it manually:

```bash
# Bash or Zsh
source .venv/bin/activate

# Fish
source .venv/bin/activate.fish
```

### 3. Configure environment variables

Copy the example configuration:

```bash
cp .env.example .env
```

Fill in the values in `.env`:

```dotenv
# OpenRouter
OPENROUTER_API_KEY=your_api_key

# Telegram
BOT_TOKEN=your_token
WEBHOOK_BASE_URL=https://your-domain.example
WEBHOOK_PATH=/telegram/webhook
WEBHOOK_SECRET_TOKEN=your_strong_secret
WEB_SERVER_HOST=0.0.0.0
WEB_SERVER_PORT=8080

# HeyGen
HEYGEN_API_KEY=your_api_key

# Google Sheets
GOOGLE_CREDENTIALS_JSON=google_credentials.json
GOOGLE_JOURNAL_SHEET_URL=https://docs.google.com/spreadsheets/d/.../edit#gid=...
GOOGLE_CFS_SHEET_URL=https://docs.google.com/spreadsheets/d/.../edit#gid=...
```

Place the Google service-account JSON file at the path specified by `GOOGLE_CREDENTIALS_JSON`. Do not commit this file or `.env` to the repository. Share the journal and CFS spreadsheets with the service-account email.

`WEBHOOK_BASE_URL` must be a publicly reachable HTTPS address. During development, it can be provided by a secure tunnel or a locally configured reverse proxy.

## Run

Start the bot from the repository root:

```bash
uv run python -m service.telegram.bot
```

The application starts the webhook server using `WEB_SERVER_HOST` and `WEB_SERVER_PORT`, then registers the Telegram webhook from `WEBHOOK_BASE_URL` and `WEBHOOK_PATH`.

## Configuration reference

| Variable | Purpose |
| --- | --- |
| `OPENROUTER_API_KEY` | Access to the AI models through OpenRouter |
| `BOT_TOKEN` | Telegram bot token from BotFather |
| `WEBHOOK_BASE_URL` | Public HTTPS base URL |
| `WEBHOOK_PATH` | Telegram webhook endpoint path |
| `WEBHOOK_SECRET_TOKEN` | Secret used to verify Telegram webhook requests |
| `WEB_SERVER_HOST` | Local interface for the webhook server |
| `WEB_SERVER_PORT` | Local port for the webhook server |
| `HEYGEN_API_KEY` | Access to HeyGen avatar generation |
| `GOOGLE_CREDENTIALS_JSON` | Path to the Google service-account JSON file |
| `GOOGLE_JOURNAL_SHEET_URL` | Operations journal spreadsheet URL |
| `GOOGLE_CFS_SHEET_URL` | CFS report spreadsheet URL |

## Development

When dependencies change, update the environment and lock file with:

```bash
uv sync
```

Keep secrets, generated logs, local data, and service-account credentials outside version control.
