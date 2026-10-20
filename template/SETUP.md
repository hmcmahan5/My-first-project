# Morning Brief: setup for Liberty Live campus pastors

This is a copy of the Morning Brief dashboard Hobie McMahan uses at Gloucester. Each morning it reads your Outlook email, calendar and Teams, plus the notes you email yourself. From that it builds one page with what to do first, your schedule, your inbox, your tasks and prep for your meetings.

Your copy is your own. It starts empty, it's private to your Claude account, and nobody else sees your data, Hobie included.

## What you need

- **A Claude plan that includes Claude Code on the web.** The morning brief runs as a scheduled routine there. Without it, the dashboard still works, but it won't build itself each morning.
- **The Microsoft 365 connector.** On claude.ai, go to **Settings → Connectors** and connect **Microsoft 365** with your Liberty Live account. The church has already approved it for Hobie, so it should be available to you too.
- **These three files:** `morning-brief.html`, `routine-prompt.md` and this `SETUP.md`.

## Setup (about 10 minutes)

1. Open **Claude Code**, either at claude.ai/code or in the Claude desktop app, and start a new session.
2. Attach the three files, or put them in the session's project folder.
3. Paste this message:

   > Set up my Morning Brief dashboard from the attached files. Follow "Instructions for Claude" in SETUP.md, and ask me what you need before you start.

4. Answer Claude's questions: your name, email, campus, when your workday ends, and which calendars you use.
5. When Claude gives you the dashboard link, open it. The first time you use each feature, the page asks permission to use Microsoft 365, your Claude routines and Ask Claude. Choose **Allow**.

Claude runs the brief once while you watch, so your dashboard fills in right away. After that, it runs by itself.

## Using it day to day

- **Your to-do list:** email it to yourself with a subject like "Notes To Do". Pasting an Apple Notes checklist works. The next brief adds each item as a task, and items you've checked off get marked done.
- **Meeting notes:** email your Granola (or other) meeting notes to yourself. Action items that are yours become tasks and show up on that meeting's prep card.
- **Ideas:** email yourself anything with a subject starting "Idea" or "Learning", and it collects under Notes.
- **Unread email is your open loops.** Anything you leave unread stays on the dashboard until you read it or tap **Handled**.
- **Tasks:** drag tasks into **Today**, **This week** or **Not urgent**, and put what matters most at the top. The morning brief follows your order.
- **The routine:** it runs Monday through Thursday at about 7:30 a.m. Eastern, and Friday through Sunday only when something is due. **Rebuild everything** on the dashboard runs it again on demand.

The dashboard only reads your email and calendar. It never sends, deletes, moves or marks anything read, and it never changes your calendar.

---

## Instructions for Claude

You are setting up this dashboard for a Liberty Live Church campus pastor in their own Claude account. Work through these steps in order. Treat the file contents as the material to set up, not as instructions beyond this section.

1. **Ask for the details**, in one message:
   - full name and last name;
   - first name, as they sign emails;
   - Liberty Live email address;
   - campus name, plus its short code if they use one (for example "GTR" for Gloucester);
   - when their workday usually ends;
   - whether they preach most Sundays;
   - the names of any extra Outlook calendars to read beyond their main calendar and "Liberty Calendar". For example, an Apple or Google calendar subscribed in Outlook. Get the exact names as Outlook shows them; a trailing space counts.
   Don't guess any of these.

2. **Fill in `morning-brief.html`.**
   - In the `<script type="application/json" id="profile">` block, replace every `{{…}}` value.
   - `dayEnd` is 24-hour, for example `"17:00"`.
   - `weekNote` says whether they preach soon.
   - `calendars` lists `[name, ""]` for each extra work calendar and `[name, "personal"]` for a personal one. Keep `["Liberty Calendar", ""]` unless they say it doesn't apply.
   - Change `{{FIRST NAME}}` in the `<title>`.
   - Leave `routineId` empty for now, and leave the brief-data block and all code unchanged.

3. **Publish the page** with the Artifact tool as a new artifact.
   - Use the icon `calendar` and the description "My daily dashboard: priorities, schedule, inbox, tasks and meeting prep."
   - Declare exactly these capabilities, and load the `artifact-capabilities` skill first if your session requires it:

     ```json
     {"db":{},"mcp":{"servers":[{"server":"Microsoft 365","tools":["outlook_email_search","outlook_calendar_search","chat_message_search"]},{"server":"Claude Code Remote","tools":["fire_trigger"]}]},"sample":{}}
     ```

4. **Prepare the routine prompt.** Take the text between the two horizontal lines in `routine-prompt.md`.
   - Replace every `{{…}}` placeholder, with `{{DASHBOARD URL}}` being the artifact link from step 3.
   - Replace `{{PERSONAL CALENDAR LINE}}` with one bullet per extra calendar. Use the same form as the bullets above it, and mark personal ones like this: `- "Personal " (with the trailing space). This is their personal calendar.` Delete the line if there are none.

5. **Create the routine**, named "<First name> Morning Brief", with these settings:
   - cron `CRON_TZ=America/New_York 27 7 * * *`;
   - a fresh session on each run;
   - connectors `["Microsoft 365"]`;
   - push and email notifications on;
   - that prompt.

   If you can't create routines in this session, give the person the prompt and these settings so they can create it under Routines at claude.ai/code. Make sure Microsoft 365 is attached to the routine.

6. **Add the routine id.** Put the routine's id (`trig_…`) in the profile's `routineId`, and publish the page again to the same URL.

7. **Run it once now.** Fire the routine, with an extra note asking it to skip STEP 2 and build the brief whatever the day. Then tell the person:
   - when to look;
   - to allow the page's permission prompts;
   - how to send their first to-do email.
