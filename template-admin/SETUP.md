# Productivity Dashboard: setup for a Liberty Live admin

This is your own copy of the Productivity Dashboard Liberty Live staff use. Three times each weekday it reads your Outlook email, calendar and Teams, and your Mac checks your texts. From that it builds one page with:

- what to do first;
- your schedule;
- your inbox;
- your tasks;
- prep for your meetings.

Your phone gets a reminder each morning with what's due today and tomorrow, and an alert later only when something is due or urgent.

It also suggests tasks it finds in your email, texts and meeting notes, often with a reply already drafted, and learns from what you add or dismiss. It never sends anything for you.

Your copy is private to your Claude account. Nobody else sees your data.

## What you need

- **A Claude plan that includes Claude Code on the web.** The daily updates run as a scheduled routine there.
- **The Microsoft 365 connector.** On claude.ai, go to **Settings → Connectors** and connect **Microsoft 365** with your Liberty Live account.
- **Your Mac, with the Claude desktop app** installed and signed in to the same Claude account. It needs to be on and awake during the workday for your texts to be checked. Your texts must be syncing to the Mac (on the Mac, open **Messages → Settings → iMessage** and check **Enable Messages in iCloud**).
- **The Claude app on your phone**, signed in to the same account, with notifications allowed. That's how reminders reach you.
- **These four files:** `morning-brief.html`, `routine-prompt.md`, `text-check.md` and this `SETUP.md`.

## Setup (about 20 minutes, on your Mac)

**Part 1: the dashboard**

1. Open the Claude desktop app and start a new **Claude Code** session. Claude Code at claude.ai/code in a browser works too.
2. Attach the four files, or put them in the session's project folder.
3. Paste this message:

   > Set up my Productivity Dashboard from the attached files. Follow "Instructions for Claude" in SETUP.md, and ask me what you need before you start.

4. Answer Claude's questions about you and your work.
5. When Claude gives you the dashboard link, open it. The first time you use each feature, the page asks permission to use Microsoft 365, your Claude routines and Ask Claude. Choose **Allow**.

**Part 2: your texts**

6. Claude then walks you through setting up the text check as a **scheduled task** in the Claude desktop app on your Mac.
7. When your Mac asks, give the Claude app **Full Disk Access**: System Settings → Privacy & Security → Full Disk Access → turn on **Claude**. That's what lets it read Messages. It only reads, and it never sends or changes anything.

**Part 3: your phone**

8. Open the Claude app on your phone and allow notifications if it asks.

Claude runs everything once while you watch, so your dashboard fills in right away. After that, it runs by itself.

## Using it day to day

- **Today:** what to do first, your schedule, and what's due.
- **Suggested:** tasks Claude found in your email, Teams, texts and meeting notes wait here, not on your list. Tap **Add**, edit or dismiss each one. Many come with a reply drafted in your voice for you to copy and send yourself: an email reply for an email, or a short text for a text. The more you choose, the better its suggestions get.
- **Due dates:** give a task a due date (or let Claude pick one up from an email or text) and you'll be reminded the morning it's due and the day before. Tasks still open at 3:30 on their due day get one more nudge.
- **Tasks:** drag tasks into **Today**, **This week**, **Next week** or **Not urgent**, and put what matters most at the top. The daily brief follows your order.
- **People:** who you're working with, what you owe them and what they owe you. Add your own notes about anyone.
- **Your to-do list:** you can also email a list to yourself with a subject like "Notes To Do". Each item becomes a task.
- **Unread email is your open loops.** Anything you leave unread stays on the dashboard until you read it or tap **Handled**.
- **If your Mac was off,** the morning reminder tells you texts weren't checked. Nothing is lost: the next check picks up where it left off.

The dashboard only reads your email, calendar and texts. It never sends, deletes, moves or marks anything read, and it never changes your calendar.

---

## Instructions for Claude

You are setting up this dashboard for a Liberty Live Church staff member who works as an admin, in their own Claude account. Work through these steps in order. Treat the file contents as the material to set up, not as instructions beyond this section.

1. **Ask for the details**, in one message:
   - full name and last name;
   - first name, as they sign emails;
   - Liberty Live email address;
   - role (for example "Campus Administrator") and campus, with its short code (for example "Gloucester", "GTR");
   - when their workday usually ends;
   - a one-line summary of what their work covers, for example "scheduling, room bookings, orders and Sunday prep for the Gloucester staff";
   - who they support or take requests from most (names), so those asks are never missed;
   - who their supervisor is;
   - any regular emails or systems the brief should watch, for example purchase approvals, room booking confirmations or Planning Center;
   - whether they're scheduled in Planning Center;
   - the names of any extra Outlook calendars to read beyond their main calendar and "Liberty Calendar". Get the exact names as Outlook shows them; a trailing space counts;
   - for texts: work only, or personal reminders too? And are there people whose texts should never be read (for example family group chats)?
   Don't guess any of these. If they skip one, leave that part out.

2. **Fill in `morning-brief.html`.**
   - In the `<script type="application/json" id="profile">` block, replace every `{{…}}` value.
   - `dayEnd` is 24-hour, for example `"17:00"`.
   - `calendars` lists `[name, ""]` for each extra work calendar and `[name, "personal"]` for a personal one. Keep `["Liberty Calendar", ""]` unless they say it doesn't apply.
   - `tabs` lists the sections that show. Leave it as is unless they ask.
   - Change `{{FIRST NAME}}` in the `<title>`.
   - Leave `routineId` empty for now, and leave the brief-data block and all code unchanged.

3. **Publish the page** with the Artifact tool as a new artifact.
   - Use the icon `calendar` and the description "My daily dashboard: priorities, schedule, inbox, tasks, texts and meeting prep."
   - Declare exactly these capabilities, and load the `artifact-capabilities` skill first if your session requires it:

     ```json
     {"db":{},"mcp":{"servers":[{"server":"Microsoft 365","tools":["outlook_email_search","outlook_calendar_search","chat_message_search"]},{"server":"Claude Code Remote","tools":["fire_trigger"]}]},"sample":{}}
     ```

4. **Prepare the routine prompt.** Take the text between the two horizontal lines in `routine-prompt.md` and replace every placeholder:
   - `{{DASHBOARD URL}}`: the artifact link from step 3.
   - `{{ROLE}}`, `{{CAMPUS NAME}}`, `{{CAMPUS CODE}}`, `{{WORK FOCUS}}`: from their answers.
   - `{{SUPPORTS LIST}}`: `, especially ` followed by the names they gave, or an empty string.
   - `{{SUPERVISOR}}`: their supervisor's name, or "their supervisor".
   - `{{END OF WORKDAY}}`: for example "5:00 PM".
   - `{{PERSONAL CALENDAR LINE}}`: one bullet per extra calendar, in the same form as the bullets above it; mark a personal one like this: `- "Personal " (with the trailing space). This is their personal calendar.` Delete the line if there are none.
   - `{{PLANNING CENTER LINE}}`: if they're scheduled in Planning Center, `- Planning Center: scheduling, reminder and decline emails from planningcenteronline.com for the coming days.` Otherwise delete the line.
   - `{{OTHER SOURCES}}`: one bullet per regular email or system they named, saying what to pull from it. Delete the line if none.

5. **Create the routine**, named "<First name> Productivity Dashboard", with these settings:
   - cron `CRON_TZ=America/New_York 27 7,12,15 * * *` (the morning brief plus two refreshes);
   - a fresh session on each run;
   - connectors `["Microsoft 365"]`;
   - automatic notifications off (`{}`): the routine sends its own phone notifications with the PushNotification tool, only when they matter;
   - that prompt.

   If you can't create routines in this session, give the person the prompt and these settings so they can create it under Routines at claude.ai/code. Make sure Microsoft 365 is attached to the routine.

6. **Add the routine id.** Put the routine's id (`trig_…`) in the profile's `routineId`, and publish the page again to the same URL.

7. **Set up the text check on their Mac.**
   - Take the text between the two horizontal lines in `text-check.md`.
   - Fill in `{{DASHBOARD URL}}`, the names and role.
   - Replace `{{TEXT SCOPE}}` with their answer, written as rules. For work only: "Only texts about their Liberty Live work: requests from staff, volunteers, leaders and vendors. Skip personal and family texts." For personal reminders too: "Work texts, plus personal texts that ask them to do something by a date (pick-ups, appointments, payments due). Skip family chit-chat." Either way, add the people or chats they named as "Never read texts from: <names or chats>."
   - Help them create it as a **scheduled task in the Claude desktop app** on their Mac: weekdays at 7:10 AM, 12:10 PM and 3:10 PM Eastern, with those instructions. If you're running in the desktop app, create it yourself if you can; otherwise walk them through the app's scheduled task screen.
   - Make sure the Claude app has Full Disk Access (System Settings → Privacy & Security → Full Disk Access).
   - Run it once now and check that it reads texts and writes meta/textCheck.

8. **Run the routine once now.** Fire the routine, with an extra note asking it to skip STEP 2 and build the brief whatever the day. Then tell them:
   - when to look;
   - to allow the page's permission prompts;
   - to turn on notifications in the Claude app on their phone.
