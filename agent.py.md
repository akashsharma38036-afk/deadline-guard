from uagents import Agent, Context, Model
from datetime import datetime, timedelta

class TaskRequest(Model):
    task: str
    deadline: str  # format: YYYY-MM-DD

class Reminder(Model):
    message: str

agent = Agent(name="deadline-guard", seed="your-secret-seed-phrase")

# Simple in-memory store for tracked tasks
tasks = {}

@agent.on_event("startup")
async def intro(ctx: Context):
    ctx.logger.info(f"⏰ Deadline-Guard live at {agent.address}")
    ctx.logger.info("Send a TaskRequest to start tracking deadlines!")

@agent.on_message(model=TaskRequest)
async def track_deadline(ctx: Context, sender: str, msg: TaskRequest):
    try:
        due = datetime.strptime(msg.deadline, "%Y-%m-%d")
    except ValueError:
        await ctx.send(sender, Reminder(message="⚠️ Deadline format galat hai. YYYY-MM-DD use karo."))
        return

    days_left = (due - datetime.utcnow()).days
    tasks[sender] = {"task": msg.task, "deadline": msg.deadline, "due": due}

    if days_left < 0:
        await ctx.send(sender, Reminder(message=f"❌ '{msg.task}' ki deadline ({msg.deadline}) nikal chuki hai!"))
    else:
        await ctx.send(sender, Reminder(
            message=f"✅ Tracked: '{msg.task}' — {days_left} din bache hain (due {msg.deadline}). Main yaad dila dunga!"
        ))

@agent.on_interval(period=86400.0)  # daily check
async def daily_check(ctx: Context):
    today = datetime.utcnow()
    for sender, t in tasks.items():
        days_left = (t["due"] - today).days
        if days_left == 0:
            await ctx.send(sender, Reminder(message=f"🚨 Aaj '{t['task']}' ki deadline hai! Jaldi karo!"))
        elif days_left in (1, 3, 7):
            await ctx.send(sender, Reminder(message=f"⏰ Reminder: '{t['task']}' ke {days_left} din bache hain."))

if __name__ == "__main__":
    agent.run()
