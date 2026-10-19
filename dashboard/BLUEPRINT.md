# Morning Brief: dashboard blueprint

The design and build plan behind `morning-brief.html`. The page has one job: show Hobie what to do today. Everything else is one click away.

## 1. Layout

```
┌───────────┬──────────────────────────────────────────────┐
│ GTR       │  PREPARED THURSDAY 7:35 AM ET   [Rebuild]     │
│ Morning   │  Thursday, October 15                         │
│ Brief     │  Headline (one or two sentences)              │
│           │  ┌ At a glance: 3/6 priorities · 2 due · 5 ┐  │
│ ▸ Today   │  └ ███████████░░░░░░░ meter                ┘  │
│   Inbox 6 │  Do these first (numbered, check off)         │
│   Tasks17 │  ┌ Schedule / plan ┐ ┌ Also on your plate ┐   │
│   Meetings│  └─────────────────┘ └────────────────────┘   │
│   Progress│                                               │
│   Notes   │                                               │
│ ───────── │                                               │
│ ☾ Dark    │                                               │
│ « Collapse│                                               │
└───────────┴──────────────────────────────────────────────┘
```

- **App shell.** A sidebar on the left (240px, collapses to 72px icons) and a reading column on the right, capped at 1040px.
- **Phones (≤ 860px).** The sidebar becomes a drawer. A sticky top bar holds the menu button and the section name, and a red dot on the menu button means messages need you.
- **Sections.** Today, Inbox, Tasks, Meetings, Progress and Notes. Today always opens first. A link ending in `#email`, `#tasks`, `#meetings`, `#progress` or `#notes` opens that section instead.

## 2. Visual hierarchy

The Today screen reads top to bottom, from summary to detail:

1. **Date and headline.** These set the shape of the day.
2. **At a glance.** Three numbers, each a link: priorities done, items due today, messages that need you. A meter shows how much of today's list is done.
3. **Do these first.** Numbered, because the order is the priority.
4. **Schedule or day plan, beside Also on your plate.** On a phone they stack.

State is shown by shape and color as well as by numbers:

- Coral chips mean overdue or urgent.
- Teal marks the current section and primary actions.
- A strikethrough means checked off.
- A red dot means a section has something waiting on you.

## 3. Chart selection

Every chart answers one question. All four are bar charts, because each compares counts.

| Question | Chart | Why this form |
|---|---|---|
| Am I getting things done? | **Finished per day.** Stacked columns for the last 14 days: Tasks, Recurring and priorities, Messages handled. | Columns over time show your rhythm. The stacks show where the effort went. |
| Where do my open tasks stand? | **Open tasks.** Horizontal bars: Overdue, Due today, This week, Later, No due date, Waiting. | These are ranked categories with long labels. The Overdue bar is drawn in the critical color and labeled. |
| How old are my open loops? | **Inbox open loops.** Stacked columns by age: Needs you vs Waiting on a reply. | Age buckets show which emails have gone stale. |
| How full is my week? | **This week on your calendar.** Columns per day, split Work and Personal when personal events exist. | Shows which days are heavy before you plan. |

Above the charts, four **stat tiles** show the headline numbers: finished in the last 7 days, open tasks, waiting on others, and slipping.

Chart rules:

- **Colors** are the same three categorical colors in each chart, in a fixed order. They are checked for colorblind separation in both themes.
- **Marks** are thin bars with a 4px rounded top, a 2px gap between stacked segments, and faint gridlines.
- **Labels** appear only on the tallest bar and on today's bar.
- **Interaction:**
  - A tooltip appears on hover and on keyboard focus.
  - Every column can be reached with Tab.
  - A **Table** button shows the same numbers as a table.
- **Motion:** bars grow in when the section opens. Data updates redraw without animation.

## 4. Reusable components

| Component | Function in the page | Used for |
|---|---|---|
| Empty state | `emptyState({icon, title, body, actions})` | Every list or chart with nothing to show |
| Skeleton | `skeleton(kind, n)` with kinds `row`, `mail`, `plan`, `card`, `chart` | Waiting on the store, Refresh, Plan my day and Plan my week |
| Chart card | `chartCard` + `paintChart` + `drawChart` / `drawTable` | All four charts |
| Stat tile | `stat()` inside `renderStats` | Progress summary |
| Ask Claude panel | `askPanel(key, describe, chips)` | Any opened item |
| Opener | `makeOpener(node, key, label, onToggle)` | Priorities and emails |
| Drag handle | `grip(item, id, label, group, onDrop)` + `saveOrder` | Reordering tasks and emails by pointer, touch or arrow keys; the order is saved in `meta/order` |
| Chips and badges | `.chip`, `.chip.due`, `.badge`, `.badge.hot` | Due dates, tags, counts |
| Buttons | `.btn`, `.btn.primary`, `.mini`, `.mini.ghost` | Actions, from strongest to quietest |

## 5. Design tokens (dark first)

All colors are tokens on `:root`, so the dark and light themes swap in one place.

| Token | Dark | Light | Role |
|---|---|---|---|
| `--bg` | `#0e151d` harbor navy | `#f2f5f8` | Page |
| `--surface` | `#151f2a` | `#ffffff` | Cards and sidebar |
| `--fg` / `--muted` | `#e7edf3` / `#9cadbf` | `#142231` / `#566578` | Text |
| `--accent` | `#62c6bf` sea-glass teal | `#0d7a74` | Current section, primary actions |
| `--due` | `#f2846f` coral | `#b4442c` | Overdue and urgent |
| `--series-1..3` | `#3987e5` `#d95926` `#199e70` | `#2a78d6` `#eb6834` `#1baf7a` | Chart series |

Type: Bricolage Grotesque for headings, Source Sans 3 for body text, and IBM Plex Mono for data and labels.

The theme button cycles Dark → Light → Match Claude. The choice is remembered in each browser.

## 6. Empty and loading states

**Empty states** all follow the same pattern:

- an icon;
- a title saying what's missing;
- one or two sentences on why, and how it fills;
- at most two actions.

They cover:

- priorities;
- today's list;
- schedule and week;
- inbox;
- tasks;
- recurring items;
- meeting prep;
- notes;
- each chart.

Chart empty states can show **example data**, labeled "Example data", so you can see the chart before your history builds up.

**Skeletons** copy the height of the rows they stand in for, so nothing jumps when data arrives:

- Task, recurring and today rows are 45px.
- Mail rows are 78px.
- Charts are 220px.

Content fades in once, the first time the store answers. If the store doesn't answer within 10 seconds, the page falls back to this browser's copy and says so. Shimmer and all motion turn off when reduced motion is on.

## 7. A realistic 30-minute build plan

This is the plan for an experienced developer building this scope on an existing page. In practice, testing across phone, desktop, light and dark took longer than 30 minutes. The order and the dependencies still hold.

| # | Task | Time | Depends on | Done when |
|---|---|---|---|---|
| 1 | Dark-first tokens and theme toggle | 4 min | none | Every color comes from a token. Dark is the default, and Light and Match Claude work. |
| 2 | App shell and collapsible sidebar | 7 min | 1 | Sections switch; the sidebar collapses and stays collapsed after a reload. The phone drawer opens, closes with Esc, and returns focus to the menu button. Arrow keys move between items. |
| 3 | Empty state component, applied to every list | 5 min | 1 | No list or chart is ever blank, and each empty state has a next step. |
| 4 | Loading gate and skeletons | 4 min | 2, 3 | No flash of stale or empty data on load, and no layout jump when data lands. |
| 5 | Charts and stat tiles | 8 min | 1, 3, 4 | Four charts render from real data, with tooltips, a table view, entrance motion and resizing. |
| 6 | Check phone, desktop, light and dark | 2 min | all | No sideways scroll, no console errors, and readable contrast in both themes. |

**Critical path:** 1 → 2 → 4 → 5.

If time runs short, cut in this order:

1. Example data in empty charts.
2. The "Match Claude" theme option.
3. The calendar chart.

Never cut:

- keyboard access;
- the table views (they are the accessible way to read the charts);
- reduced motion.
