# InternFlow

InternFlow is an automation pipeline that monitors Telegram channels for internship opportunities and forwards qualifying postings to WhatsApp. It combines a Python-based Telegram forwarder with an n8n workflow that uses AI to parse, filter, and format postings.

## Features

- Continuous monitoring of multiple Telegram channels
- Message forwarding to a Telegram bot for workflow ingestion
- AI-assisted parsing and eligibility filtering (Google Gemini in n8n)
- WhatsApp notifications with formatted details and links
- Optional Docker deployment for the Python forwarder

## Architecture

Telegram Channels -> Python Forwarder -> Telegram Bot -> n8n Workflow -> AI Parsing/Filtering -> WhatsApp API

## Repository Contents

- `telegram Autoforward.py`: Telethon-based Telegram forwarder
- `Internship Informer.json`: n8n workflow definition
- `requirements.txt`: Python dependencies
- `Dockerfile`: Container definition for the forwarder
- `rebuild.sh`: Helper script to rebuild the Docker container (update paths for your environment)

## Prerequisites

- Python 3.11+
- Telegram API credentials (`api_id`, `api_hash`)
- Telegram bot token (configured in n8n)
- n8n instance (self-hosted or cloud)
- Google Gemini API key
- WhatsApp Business API or compatible provider
- Docker (optional)

## Setup

### 1) Clone the repository

```bash
git clone https://github.com/Unknown-Geek/InternFlow.git
cd InternFlow
```

### 2) Install Python dependencies

```bash
pip install -r requirements.txt
```

### 3) Configure the Telegram forwarder

Edit `telegram Autoforward.py` and set:

- `api_id` and `api_hash` with your Telegram API credentials
- `source_channels` to the channels you want to monitor
- `target_chat` to the bot username or destination chat

The forwarder stores a local session file named `forwarder_session.session` in the working directory.

### 4) Run the forwarder

```bash
python "telegram Autoforward.py"
```

On first run, Telethon will prompt you to authenticate with your Telegram account.

### 5) Run with Docker (optional)

```bash
docker build -t internflow-forwarder .
docker run -d --name internflow-forwarder -v "$(pwd):/app" internflow-forwarder
```

## n8n Workflow Setup

1. Open your n8n instance and import `Internship Informer.json`.
2. Configure credentials for:
   - Telegram API (bot token)
   - Google Gemini API
   - WhatsApp API (endpoint and authentication)
3. Update the HTTP Request node to point to your WhatsApp provider and destination number.
4. Activate the workflow.

## Filtering Criteria

The workflow is designed to identify internship opportunities that match specific criteria (for example, the 2027 batch and selected roles). These rules live in the AI prompt within the workflow nodes and can be adjusted to:

- Change the target graduation year
- Add or remove role keywords
- Exclude specific categories (events, webinars, non-internship jobs)

## Configuration Tips

- Keep sensitive values out of source control.
- Consider loading `api_id` and `api_hash` from environment variables if you plan to deploy.
- Update `source_channels` as you join or leave Telegram channels.

## Troubleshooting

### Forwarder

- Session file issues: delete `forwarder_session.session` and re-authenticate.
- Forwarding failures: ensure the target bot has an active chat with your account.
- Rate limits: pause the script if you encounter Telegram flood-wait errors.

### n8n Workflow

- No messages received: verify bot token and webhook configuration.
- Invalid AI output: confirm the structured output parser is enabled and Gemini API quota is available.
- WhatsApp delivery failures: verify endpoint URL, phone format, and authentication.

## Security

- Never commit API credentials or access tokens.
- Store secrets in environment variables or a secure secrets manager.
- Restrict WhatsApp API access to trusted IPs and use HTTPS.

### Recommended `.gitignore` entries

```
*.session
*.session-journal
.env
config.py
__pycache__/
*.pyc
```

## Development

If you use Docker for the forwarder, you can rebuild with the helper script (update the path inside `rebuild.sh` to match your environment):

```bash
./rebuild.sh
```

## Contributing

Contributions are welcome. Please open an issue to discuss changes or submit a pull request.

## License

This repository does not currently include a license file.

## Acknowledgments

- Telethon for the Telegram client library
- n8n for workflow orchestration
- Google Gemini for AI-assisted parsing

## Contact

For questions or support, please open an issue on GitHub.
