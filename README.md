# ⏰ Deadline-Guard

An agentic AI that tracks your deadlines and sends you reminders — built with the Fetch.ai **uAgents** SDK.

## What it does
- Accepts a task + deadline (`YYYY-MM-DD`)
- Confirms tracking and tells you how many days are left
- Sends automatic reminders at 7 days, 3 days, 1 day, and on the due date

## Setup

```bash
pip install -r requirements.txt
python agent.py
```

> Tip: change `seed="your-secret-seed-phrase"` in `agent.py` to your own secret phrase to get a stable agent address.

## Usage
Send the agent a `TaskRequest` message:

```python
TaskRequest(task="Submit project report", deadline="2026-10-30")
```

## Roadmap
- [ ] Persistent task storage (SQLite)
- [ ] Email / chat notifications
- [ ] Multi-user web interface

## License
MIT
