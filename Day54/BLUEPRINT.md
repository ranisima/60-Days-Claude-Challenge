# StudyStreak — Implementation Blueprint

**Days 2 to 10**

*The single source of truth for building and shipping v1.0*

AB Talks 60-Day Claude AI Challenge — 10-Day Capstone

How to Use This Blueprint

Each remaining day of the capstone should begin as a fresh AI
conversation. Paste that day's full section into the new conversation
(or share this document and say “let's do Day N”) so the assistant has
everything needed to guide you without re-planning or re-architecting.

Every day follows the same structure: Objective, What I'll Learn,
Features to Build, Step-by-Step Implementation Plan, Files & Folders,
APIs/Libraries/Tools, Testing Tasks, Common Issues & Debugging Tips,
End-of-Day Checklist, Expected Project State & Screenshots, and Handoff
Notes for the next day.

This blueprint assumes the scope, personas, and requirements defined in
the companion Product Requirements Document (PRD). If any conflict
arises between a daily prompt and the PRD, the PRD's scope boundaries
(Section 6) win — protect the Day 10 deadline over any new idea.

Finalized Technical Architecture

This stack was chosen specifically for this project and your current
skill level (comfortable with HTML/CSS/JS/Python basics). It is
deliberately simple, free, and beginner-friendly — no build tooling, no
paid services, no unnecessary moving parts.

**Backend:** Python 3 + Flask (matches your existing Python basics)

**Database:** SQLite (single file: study_streak.db) via Python's
built-in sqlite3 module

**Frontend:** Jinja2 server-rendered HTML templates + plain CSS + a
small amount of vanilla JS

**Ai:** Anthropic Claude API (Messages API) via the official
`anthropic` Python package

**Email:** Python smtplib + Gmail SMTP with a free App Password

**Scheduling:** PythonAnywhere's free "Scheduled Tasks" feature (runs a
Python script once daily) — this is what powers the proactive missed-day
email without needing a paid cron service

**Hosting:** PythonAnywhere free tier (Flask-friendly, persistent file
storage so SQLite data survives restarts, no credit card required)

**Vcs:** Git + GitHub (for version history and, optionally, to pull code
onto PythonAnywhere)

Project Folder Structure

This exact structure is created on Day 2 and built out across the
following days. Refer back to it any time you're unsure where a piece of
code belongs.

studystreak/  
├── app.py \# Flask app: routes, request handling  
├── models.py \# SQLite schema + data access functions  
├── ai_coach.py \# Claude API wrapper: builds prompt, returns nudge
text  
├── emailer.py \# smtplib wrapper: sends the missed-day email  
├── scheduled_check.py \# Script run daily by PythonAnywhere Scheduled
Task  
├── config.py \# Loads secrets from environment variables  
├── requirements.txt  
├── .env \# Local secrets (NEVER committed to git)  
├── .gitignore  
├── study_streak.db \# SQLite database file (created automatically)  
├── templates/  
│ ├── base.html  
│ ├── dashboard.html \# Main screen: check-in + stats + AI nudge  
│ ├── add_course.html \# Add/edit course form  
│ └── completed.html \# Course-complete celebration screen  
├── static/  
│ ├── css/style.css  
│ └── js/app.js  
└── tests/  
└── test_app.py \# Simple manual/automated test checks

Data Model (SQLite)

Two tables power the entire product:

- course — id, name, total_lessons, lessons_completed, start_date,
  target_end_date, is_active, is_complete, last_nudge_email_date

- checkin — id, course_id, checkin_date, studied, note,
  lessons_completed_at_checkin

Only one course row is ever active at a time (v1.0 scope). This keeps
the schema and all related logic intentionally simple.

The 9-Day Roadmap at a Glance

|         |                |                                                       |
|---------|----------------|-------------------------------------------------------|
| **Day** | **Phase**      | **Focus**                                             |
| 2       | Design & Setup | Architecture, Tech Stack & Environment Setup          |
| 3       | Implementation | Course Management — Add/Edit the Active Course        |
| 4       | Implementation | Daily Check-In, Streak Calculation & Pace Tracking    |
| 5       | Implementation | AI Coach Integration — Personalized Nudges            |
| 6       | Implementation | Proactive Email Nudges & the Scheduled Missed-Day Job |
| 7       | Implementation | UI/UX Polish — Styling, Empty States & Edge Cases     |
| 8       | Testing        | End-to-End Testing & Bug Fixing                       |
| 9       | Deployment     | Deploy to Production on PythonAnywhere                |
| 10      | Maintenance    | Final QA, Documentation & Capstone Demo Prep          |


## DAY 2 — DESIGN & SETUP

### Architecture, Tech Stack & Environment Setup


#### Objective

Finalize the technical architecture for StudyStreak and get a working
local development environment with the project skeleton in place —
nothing user-facing yet, but everything ready to build on.

#### What I'll Learn

- How to translate a PRD into a concrete technical architecture

- How Flask projects are structured (routes, templates, static files)

- How to design a simple relational schema (SQLite) for a real feature
  set

- How to safely manage secrets (API keys, email credentials) using
  environment variables

#### Features to Build

- No user-facing features today — this is Design + Setup day

- Deliverable: a running ‘Hello StudyStreak’ Flask app with the full
  folder skeleton and an empty database with the correct schema created

#### Finalized Tech Stack (reference)

Today you will use this finalized stack (already chosen for you based on
your skills and constraints, so there's no need to re-debate it):

- backend: Python 3 + Flask (matches your existing Python basics)

- database: SQLite (single file: study_streak.db) via Python's built-in
  sqlite3 module

- frontend: Jinja2 server-rendered HTML templates + plain CSS + a small
  amount of vanilla JS

- ai: Anthropic Claude API (Messages API) via the official `anthropic`
  Python package

- email: Python smtplib + Gmail SMTP with a free App Password

- scheduling: PythonAnywhere's free "Scheduled Tasks" feature (runs a
  Python script once daily) — this is what powers the proactive
  missed-day email without needing a paid cron service

- hosting: PythonAnywhere free tier (Flask-friendly, persistent file
  storage so SQLite data survives restarts, no credit card required)

- vcs: Git + GitHub (for version history and, optionally, to pull code
  onto PythonAnywhere)

#### Step-by-Step Implementation Plan

1.  Install Python 3 (if not already installed) and verify with
    `python3 --version` in your terminal.

2.  Create the project folder: `mkdir studystreak && cd studystreak`.

3.  Create a virtual environment: `python3 -m venv venv`, then
    activate it (`source venv/bin/activate` on Mac/Linux,
    `venv\Scripts\activate` on Windows).

4.  Install dependencies: `pip install flask anthropic python-dotenv`
    then freeze them: `pip freeze > requirements.txt`.

5.  Create the folder structure exactly as shown in 'Files & Folders'
    below (empty files are fine for now).

6.  In `models.py`, write a `init_db()` function that creates two
    tables: `course` (id, name, total_lessons, lessons_completed,
    start_date, target_end_date, is_active, is_complete) and `checkin`
    (id, course_id, checkin_date, studied, note,
    lessons_completed_at_checkin).

7.  In `app.py`, create a minimal Flask app with one route (`/`)
    that returns 'StudyStreak is running' as plain text, just to confirm
    Flask works.

8.  Run the app locally: `python app.py`, then open the printed local
    URL (usually http://127.0.0.1:5000) in your browser to confirm you
    see the message.

9.  Create a `.env` file (not committed to git) with placeholders:
    `ANTHROPIC_API_KEY=`, `GMAIL_ADDRESS=`, `GMAIL_APP_PASSWORD=`,
    `NOTIFY_TO_EMAIL=`.

10. Create `.gitignore` with at least: `venv/`, `.env`,
    `\_\_pycache\_\_/`, `study_streak.db`, `*.pyc`.

11. Initialize git (`git init`), make your first commit, and create a
    GitHub repository, then push.

12. Get an Anthropic API key from the Anthropic Console
    (console.anthropic.com) and paste it into `.env`. I will walk you
    through this step by step if needed — confirm when ready.

13. Set up a Gmail App Password (Google Account > Security > 2-Step
    Verification > App Passwords) so the app can send email without
    your main password. I will walk you through this step by step if
    needed — confirm when ready.

#### Files & Folders

- Create: app.py, models.py, config.py, requirements.txt, .env,
  .gitignore

- Create empty folders: templates/, static/css/, static/js/, tests/

- Create (empty stub, filled in later days): ai_coach.py, emailer.py,
  scheduled_check.py

#### APIs, Libraries & Tools

- Anthropic Console (console.anthropic.com) — to generate your Claude
  API key

- Gmail App Passwords — to allow smtplib to send email securely

- GitHub — to host your code repository

#### Testing Tasks

- Confirm `python app.py` runs without errors and the browser shows
  the test message

- Confirm `init_db()` creates study_streak.db with the `course` and
  `checkin` tables (inspect with `sqlite3 study_streak.db` then
  `.tables`)

- Confirm `.env` is listed in `.gitignore` and does NOT appear in
  `git status` as trackable

#### Common Issues & Debugging Tips

- ‘flask: command not found’ — make sure your virtual environment is
  activated before installing/running

- Port 5000 already in use — stop other running Flask processes, or run
  with a different port: `app.run(port=5001)`

- API key accidentally committed to git — immediately regenerate the key
  in the Anthropic Console and re-check .gitignore

#### End-of-Day Checklist

- Flask app runs locally and shows the test message

- study_streak.db exists with course and checkin tables

- Anthropic API key obtained and stored in .env only

- Gmail App Password obtained and stored in .env only

- Project pushed to a GitHub repository

#### Expected Project State & Screenshots to Capture

- Terminal showing `python app.py` running successfully

- Browser showing the 'StudyStreak is running' test message

- Terminal output of `.tables` showing course and checkin tables

#### Handoff Notes for Next Day

*Day 3 assumes: a working Flask app skeleton, an initialized SQLite
database with the course/checkin schema, and API keys safely stored in
.env. Day 3 will build the actual course creation feature and connect it
to a real dashboard template.*


## DAY 3 — IMPLEMENTATION

### Course Management — Add/Edit the Active Course


#### Objective

Build the first real feature: the user can create and edit their single
active course, and see it reflected on a basic (unstyled) dashboard
page.

#### What I'll Learn

- How Flask handles HTML forms (GET vs POST routes)

- How to read and write to SQLite from Python using parameterized
  queries (safe from SQL injection)

- How to use Jinja2 templates to render dynamic data into HTML

#### Features to Build

- Add Course form (name, total lessons, target end date)

- Edit Course (same form, pre-filled, for the active course)

- Basic dashboard page showing the active course's raw details (styling
  comes Day 7)

#### Step-by-Step Implementation Plan

14. In models.py, add functions: `create_course(name, total_lessons,
    target_end_date)`, `get_active_course()`,
    `update_course(course_id, name, total_lessons, target_end_date)`.
    Enforce only one active course by setting `is_active=1` on the new
    one and `is_active=0` on any previous course when a new course is
    created.

15. In app.py, add route `GET /` — dashboard: fetch the active course
    with `get_active_course()`. If none exists, render
    `dashboard.html` in an empty state; if one exists, pass its data
    to the template.

16. Add route `GET /add-course` and `POST /add-course` — GET renders
    `add_course.html` with an empty form; POST reads form fields
    (`request.form`), validates them (name not empty, total_lessons is
    a positive integer, target_end_date is a valid future date), and
    calls `create_course(...)`, then redirects to `/`.

17. Add route `GET /edit-course` and `POST /edit-course` — same
    pattern as add, but pre-fills the form with the active course's
    current values and calls `update_course(...)` on submit.

18. Build `templates/base.html` with a simple shared layout (title, a
    `\<link>` to static/css/style.css, and a `{% block content
    %}`).

19. Build `templates/add_course.html` extending base.html with a plain
    HTML form (name text input, total_lessons number input,
    target_end_date date input, submit button).

20. Build `templates/dashboard.html` extending base.html: for now,
    just print the course name, total lessons, and target end date as
    plain text if a course exists, or a message + 'Add a course' link if
    not.

21. Wire up basic navigation: a link/button from the dashboard to 'Add
    Course' (only shown in empty state) and to 'Edit Course' (only shown
    when a course exists).

#### Files & Folders

- Modify: models.py (add course functions), app.py (add 3 routes)

- Create: templates/base.html, templates/add_course.html,
  templates/dashboard.html

#### APIs, Libraries & Tools

- None new today — pure Flask + SQLite + Jinja2

#### Testing Tasks

- Add a course through the form and confirm it appears on the dashboard

- Edit the course and confirm changes persist after a page refresh

- Try submitting the add-course form with an empty name or a past date
  and confirm validation prevents it (or at minimum doesn't crash the
  app)

- Restart the Flask server and confirm the course data is still there
  (proves SQLite persistence)

#### Common Issues & Debugging Tips

- Form data not arriving in Flask — confirm the HTML form has
  `method="POST"` and each input has a `name` attribute matching
  what `request.form` expects

- Date parsing errors — use Python's `datetime.strptime(value,
  '%Y-%m-%d')` to match the HTML date input's format

- Old course still shows as active after adding a new one — double check
  the SQL UPDATE that sets `is_active=0` runs before the INSERT of the
  new course

#### End-of-Day Checklist

- Can add a new course via the form

- Can edit the active course and see changes persist

- Dashboard correctly shows empty state vs. active-course state

- Data survives a server restart

#### Expected Project State & Screenshots to Capture

- The Add Course form filled in with sample data

- The dashboard showing the newly created course's raw details

- The empty-state dashboard (before any course exists)

#### Handoff Notes for Next Day

*Day 4 assumes: a working add/edit course flow and a dashboard route
that already fetches the active course. Day 4 will add the daily
check-in feature and start calculating streak, progress %, and pace —
displayed on the same dashboard.*


## DAY 4 — IMPLEMENTATION

### Daily Check-In, Streak Calculation & Pace Tracking


#### Objective

Build the daily check-in flow and the logic that turns raw check-in data
into the meaningful stats users actually care about: streak, progress
percentage, and pace vs. deadline.

#### What I'll Learn

- How to model and query date-based data in SQLite

- How to implement streak-counting logic (a very common real-world
  pattern)

- How to compare actual vs. expected progress over time (basic pace
  math)

#### Features to Build

- 'I studied today' check-in button with optional note and optional
  lessons-completed update

- Duplicate-check-in prevention (one check-in per calendar day)

- Dashboard stats: current streak, progress %, days remaining, pace
  status (on track / behind / ahead)

#### Step-by-Step Implementation Plan

22. In models.py, add `create_checkin(course_id, checkin_date, studied,
    note, lessons_completed)` and `get_checkin_for_date(course_id,
    date)` (to block duplicates) and `get_all_checkins(course_id)`.

23. In models.py, add `calculate_streak(course_id)`: fetch all
    check-in dates with studied=True, sort descending, and count
    consecutive days back from today (or yesterday, if today's check-in
    hasn't happened yet) until a gap is found.

24. In models.py, add `calculate_pace(course)`: compute expected
    lessons-by-today using `(days_elapsed / total_days) *
    total_lessons`, compare to `lessons_completed`, and return
    'ahead', 'on_track', or 'behind' (use a small tolerance band, e.g.
    ±1 lesson = on_track).

25. In app.py, add route `POST /checkin`: read 'studied' (checkbox),
    'note' (text), and 'lessons_completed' (number, optional) from the
    form. Check `get_checkin_for_date` first — if a check-in already
    exists for today, update it instead of inserting a new one. Then
    redirect to `/`.

26. Update the `GET /` dashboard route: after fetching the active
    course, call `calculate_streak()` and `calculate_pace()` and
    pass all stats into the template.

27. Update `templates/dashboard.html`: add the check-in form (checkbox
    or button for 'I studied today', a text input for note, a number
    input for lessons completed, pre-filled if today's check-in already
    exists) and display the streak, progress %, days remaining, and pace
    status as plain text (styling comes Day 7).

28. Add a guard so the check-in form is hidden/disabled once the course
    is already complete (lessons_completed >= total_lessons) — route to
    a distinct 'complete' message instead.

#### Files & Folders

- Modify: models.py (checkin + streak + pace functions), app.py (checkin
  route, updated dashboard route)

- Modify: templates/dashboard.html (check-in form + stats display)

#### APIs, Libraries & Tools

- None new today — pure Python date/SQLite logic

#### Testing Tasks

- Check in today, confirm streak becomes 1 and progress % updates

- Check in on multiple simulated days (you can temporarily hardcode a
  fake 'today' date to test streak logic without waiting real days) and
  confirm streak increments and resets correctly across a gap

- Submit a second check-in for the same day and confirm it updates
  rather than duplicates

- Manually set lessons_completed behind expected pace and confirm the
  pace indicator shows 'behind'

#### Common Issues & Debugging Tips

- Streak counts wrong across a missed day — double check your loop
  breaks on the first gap and doesn't just count total check-ins

- Timezone mismatches causing 'today' to be calculated differently
  between check-in and later scheduled email logic — pick one timezone
  convention now (e.g. server local time) and use it consistently
  everywhere; document this choice

- Pace math dividing by zero when total_days is 0 (target date = start
  date) — add a guard clause

#### End-of-Day Checklist

- Can check in once per day with optional note and lessons update

- Streak correctly counts consecutive days and resets after a gap

- Progress % and days remaining display correctly

- Pace indicator correctly shows on track / behind / ahead

#### Expected Project State & Screenshots to Capture

- The check-in form filled out for today

- The dashboard showing an active streak (e.g. 3+ days) with progress
  stats

- The dashboard showing a 'behind pace' state

#### Handoff Notes for Next Day

*Day 5 assumes: a fully working check-in flow with streak, progress, and
pace already calculated and available as variables on the dashboard
route. Day 5 will feed exactly this data into the Claude API to generate
the personalized AI nudge shown after each check-in.*


## DAY 5 — IMPLEMENTATION

### AI Coach Integration — Personalized Nudges


#### Objective

Bring the product's core differentiator to life: after every check-in,
call the Claude API with the learner's real stats and display a warm,
specific, personalized nudge on the dashboard.

#### What I'll Learn

- How to call the Anthropic Messages API from Python

- How to design a system prompt that consistently produces a specific
  tone and format

- How to build resilient AI integrations with graceful fallbacks

#### Features to Build

- AI-generated nudge message displayed after each check-in, referencing
  real stats (streak, pace, note)

- Fallback static message if the AI call fails or times out

#### Step-by-Step Implementation Plan

29. In config.py, load `ANTHROPIC_API_KEY` from the environment using
    `python-dotenv` (`load_dotenv()` + `os.getenv(...)`).

30. In ai_coach.py, write `generate_nudge(streak, progress_pct,
    pace_status, days_remaining, note, course_name)`: build a system
    prompt establishing the persona from PRD Section 8 (warm,
    encouraging, 1–3 sentences, must reference a concrete fact, never
    guilt-trip), and a user message containing the learner's current
    stats as plain text.

31. Call `client.messages.create(model=..., max_tokens=150,
    system=system_prompt, messages=\[{role: 'user', content:
    stats_text}\])` using the `anthropic` Python SDK, and extract the
    text from the response.

32. Wrap the API call in a try/except: on any exception (timeout, rate
    limit, network error), return one of 3–4 pre-written fallback
    messages (e.g. ‘Nice work checking in — keep the momentum going!’)
    chosen based on pace_status so it still feels relevant.

33. In app.py, after saving the check-in in the `POST /checkin` route,
    call `generate_nudge(...)` with the freshly calculated stats and
    store the returned text (in the database as a new `last_nudge`
    field on the course row, or in a simple session variable) so it can
    be displayed on redirect.

34. Update `templates/dashboard.html` to prominently display the
    latest nudge message near the top of the page.

35. Manually test at least 3 different scenarios (on track, behind pace,
    just hit a milestone) and read the actual AI output to confirm tone
    matches the PRD's persona — adjust the system prompt wording if it
    sounds generic or robotic.

#### Files & Folders

- Modify: config.py (load API key), ai_coach.py (full nudge generation
  logic), app.py (call ai_coach after checkin), models.py (add
  last_nudge storage if using DB approach)

- Modify: templates/dashboard.html (display nudge)

#### APIs, Libraries & Tools

- Anthropic Claude API (Messages API) via the `anthropic` Python
  package — requires ANTHROPIC_API_KEY from Day 2

#### Testing Tasks

- Check in while on-track and confirm the nudge references the real
  streak/progress numbers

- Check in while behind pace and confirm the nudge acknowledges this
  without shaming

- Temporarily break the API key (wrong value) and confirm the fallback
  message displays instead of a crash

- Confirm the check-in flow never hangs indefinitely — add a reasonable
  timeout to the API call

#### Common Issues & Debugging Tips

- AI response too long or off-tone — tighten the system prompt with
  explicit constraints (max sentences, required references, forbidden
  phrases like generic 'You can do it!')

- API key not found error — confirm `.env` is loaded before
  `os.getenv` is called, and that `.env` is in the project root

- Slow response times — set `max_tokens` low (100–150) since nudges
  are short, which also speeds up generation

#### End-of-Day Checklist

- AI nudge appears on the dashboard after every check-in

- Nudge content changes meaningfully based on real stats (not the same
  message every time)

- Fallback message works when the API call fails

- Tone matches the PRD's warm, encouraging persona in at least 3 test
  scenarios

#### Expected Project State & Screenshots to Capture

- Dashboard showing an on-track nudge with visible streak/progress
  numbers

- Dashboard showing a behind-pace nudge

- Terminal/log showing a simulated API failure correctly falling back to
  the static message

#### Handoff Notes for Next Day

*Day 6 assumes: a working `generate_nudge()` function in ai_coach.py
that can be reused with different input stats. Day 6 will call this same
function (or a close variant) to generate the proactive missed-day
email, and will build the actual emailer and the scheduled daily check.*


## DAY 6 — IMPLEMENTATION

### Proactive Email Nudges & the Scheduled Missed-Day Job


#### Objective

Implement the feature that makes StudyStreak proactive rather than
passive: a daily scheduled check that emails the user a warm
AI-generated nudge if they missed today's check-in.

#### What I'll Learn

- How to send email from Python using smtplib and Gmail's SMTP server

- How to write a standalone script separate from the web app for
  scheduled/background jobs

- How free scheduled tasks work on PythonAnywhere (cron-like, no server
  management)

#### Features to Build

- emailer.py: sends a formatted email via Gmail SMTP

- scheduled_check.py: standalone script that checks for a missed day and
  triggers the email

- Local test of the full flow (will go live with real scheduling on Day
  9 during deployment)

#### Step-by-Step Implementation Plan

36. In config.py, load `GMAIL_ADDRESS`, `GMAIL_APP_PASSWORD`, and
    `NOTIFY_TO_EMAIL` from the environment.

37. In emailer.py, write `send_email(subject, body)` using
    `smtplib.SMTP('smtp.gmail.com', 587)`, `starttls()`,
    `login(GMAIL_ADDRESS, GMAIL_APP_PASSWORD)`, and `sendmail(...)`.
    Wrap in try/except and log failures clearly.

38. In ai_coach.py, add `generate_missed_day_nudge(streak_before_miss,
    course_name, days_remaining)` — reuse the same persona/system
    prompt pattern from Day 5, but tailored to PRD Section 8.4's
    missed-day example tone. Include the same fallback-message safety
    net.

39. In scheduled_check.py, write the standalone logic: import models,
    get the active course, check `get_checkin_for_date(course_id,
    today)`. If no check-in exists AND the course is active AND not
    complete AND the deadline hasn't passed, call
    `generate_missed_day_nudge(...)` then `send_email(...)`. If a
    check-in already exists, do nothing and exit quietly.

40. Add a simple safeguard so the script never sends more than one email
    per day even if run twice accidentally (e.g. check a
    `last_nudge_email_date` field on the course row and skip if it
    matches today).

41. Test scheduled_check.py by running it manually from the terminal
    (`python scheduled_check.py`) on a day where you have NOT checked
    in, and confirm you receive the real email.

42. Test the 'no email' path by running it again after checking in for
    the day, and confirming no email is sent.

#### Files & Folders

- Create: emailer.py (full implementation)

- Modify: ai_coach.py (add missed-day nudge function), models.py (add
  last_nudge_email_date tracking if not already present)

- Create: scheduled_check.py (full standalone script)

#### APIs, Libraries & Tools

- Gmail SMTP (smtp.gmail.com:587) via smtplib, using the App Password
  from Day 2

- Anthropic Claude API (reused from Day 5, new prompt variant)

#### Testing Tasks

- Run scheduled_check.py manually with no check-in logged today —
  confirm a real email arrives in your inbox with a personalized message

- Run scheduled_check.py again the same day — confirm it does NOT send a
  second email

- Check in for today, then run scheduled_check.py — confirm no email is
  sent

- Temporarily break the Gmail credentials and confirm the script fails
  gracefully (logs an error) rather than crashing the whole process

#### Common Issues & Debugging Tips

- Gmail blocks the login ('less secure app' style errors) — confirm
  you're using an App Password (not your real Gmail password) and that
  2-Step Verification is enabled on the Google account

- Email lands in spam — this is common for scripted email in testing;
  not a blocker for the capstone, but worth noting as a known limitation

- Script sends duplicate emails — confirm the last_nudge_email_date
  guard is being set AND checked correctly

#### End-of-Day Checklist

- emailer.py successfully sends a real email via Gmail SMTP

- scheduled_check.py correctly detects a missed day and sends exactly
  one email

- scheduled_check.py correctly does nothing when a check-in already
  exists

- Missed-day email content is warm and references the real
  streak-before-miss

#### Expected Project State & Screenshots to Capture

- The actual received email in your inbox, showing subject +
  personalized body

- Terminal output of scheduled_check.py running successfully (both the
  'sent' and 'skipped' cases)

#### Handoff Notes for Next Day

*Day 7 assumes: the full core loop (add course → check in → AI nudge →
missed-day email) is functionally complete but visually plain. Day 7
focuses entirely on UI/UX polish — no new backend logic should be
needed. Day 9 will wire scheduled_check.py into PythonAnywhere's real
Scheduled Tasks feature so it runs automatically every day in
production.*


## DAY 7 — IMPLEMENTATION

### UI/UX Polish — Styling, Empty States & Edge Cases


#### Objective

Turn the functionally-complete but plain app into something that looks
and feels like a real, polished product — without touching core logic.

#### What I'll Learn

- How to structure CSS for a clean, consistent dashboard layout

- How to design good empty states and edge-case UI (a hallmark of
  thoughtful product design)

- Basic responsive design so the app is usable on both desktop and
  mobile browsers

#### Features to Build

- Styled dashboard: clear visual hierarchy for streak, progress, pace,
  and the AI nudge

- Styled add/edit course form

- Polished empty state (no course yet) and completion state (course
  finished)

- Basic responsive layout for mobile screens

#### Step-by-Step Implementation Plan

43. Choose a simple, cohesive color palette and a Google-Fonts-free
    system font stack (e.g. `-apple-system, Segoe UI, sans-serif`) to
    keep things fast and dependency-free.

44. In static/css/style.css, define shared layout rules in base.html: a
    centered content container (max-width ~600–700px), consistent
    spacing scale, and a card-style container for the dashboard stats.

45. Style the streak, progress %, and days-remaining as prominent 'stat
    blocks' (large number + small label), and the pace status as a
    colored badge (e.g. green = on track, amber = behind, blue = ahead).

46. Visually separate the AI nudge into its own highlighted card (e.g. a
    soft background tint) so it reads as a message 'from the coach,'
    distinct from raw stats.

47. Style the check-in form as a clear primary action (a prominent
    button for 'I studied today'), with the optional note field visually
    secondary.

48. Style add_course.html as a clean, centered form with clear labels
    and a prominent submit button.

49. Build (or refine) the empty-state screen: a friendly message plus a
    clear call-to-action button to add the first course.

50. Build (or refine) the completed-course screen: a celebratory
    message, final stats summary, and a button to start a new course.

51. Add basic responsive rules (a `@media (max-width: 600px)` block)
    so the layout doesn't break on a phone browser.

52. Do a full visual pass on all screens (empty, active/on-track,
    active/behind, completed) and fix any spacing, alignment, or
    contrast issues.

#### Files & Folders

- Modify: static/css/style.css (full styling pass)

- Modify: templates/base.html, templates/dashboard.html,
  templates/add_course.html

- Create (if not already split out): templates/completed.html

#### APIs, Libraries & Tools

- None — CSS/HTML only, no new services

#### Testing Tasks

- View the dashboard in both a desktop-width and a narrow
  (mobile-simulated) browser window and confirm no overlapping or
  cut-off elements

- Click through every state (empty → active → completed) and confirm
  each looks intentional, not broken or placeholder-ish

- Check color contrast is readable (dark text on light backgrounds, no
  low-contrast pairings)

#### Common Issues & Debugging Tips

- Layout breaks on narrow screens — use relative units (%, rem) instead
  of fixed pixel widths for containers

- Inconsistent spacing — pick one spacing scale (e.g. 8px increments)
  and apply it everywhere rather than eyeballing each element

- Badge colors clash or are unreadable — test text-on-background
  contrast directly in the browser, adjust as needed

#### End-of-Day Checklist

- Dashboard has clear visual hierarchy and no unstyled/raw-text sections
  remain

- Empty state and completed state both look intentional and polished

- Layout holds up on a narrow/mobile-width browser window

- AI nudge is visually distinct as a 'coach message'

#### Expected Project State & Screenshots to Capture

- Final styled dashboard in an on-track state

- Final styled dashboard in a behind-pace state

- Final styled empty state

- Final styled completed-course state

- Dashboard viewed at mobile width

#### Handoff Notes for Next Day

*Day 8 assumes: all screens are visually complete and the full core loop
works end-to-end locally. Day 8 shifts focus entirely to structured
testing and bug-fixing — no new features or styling should be introduced
unless a bug requires it.*


## DAY 8 — TESTING

### End-to-End Testing & Bug Fixing


#### Objective

Systematically test every flow defined in the PRD, log any bugs found,
and fix them — so Day 9's deployment starts from a stable, verified
codebase.

#### What I'll Learn

- How to write and execute a manual test plan against a PRD's functional
  requirements

- How to reproduce and isolate bugs methodically instead of guessing

- Basic automated testing in Python (a simple smoke test) as a bonus if
  time allows

#### Features to Build

- No new features today — purely testing and fixing existing ones

- Optional: a minimal automated smoke test script

#### Step-by-Step Implementation Plan

53. Re-read PRD Sections 5 (User Flows) and 7 (Functional Requirements)
    and turn each requirement into a one-line test case (e.g. 'FR-7:
    duplicate check-in updates instead of creating a new row —
    PASS/FAIL').

54. Execute Flow A (first-time setup) on a fresh database (delete
    study_streak.db and let it recreate) and confirm it works exactly as
    described.

55. Execute Flow B (daily check-in) at least 3 times across simulated
    different days (use a temporarily hardcoded date override if needed)
    to verify streak/progress/pace all update correctly together.

56. Execute Flow C (missed day) by manually running scheduled_check.py
    on a day with no check-in, confirming the real email arrives with
    correct content.

57. Execute Flow D (course completion) by setting lessons_completed
    equal to total_lessons and confirming the completion screen and AI
    message appear correctly.

58. Deliberately try to break things: empty form submissions, negative
    numbers, a target date in the past, extremely long notes, special
    characters in the course name — note anything that crashes vs.
    degrades gracefully.

59. Fix every bug found, prioritizing anything that crashes the app or
    corrupts data over minor cosmetic issues.

60. (Optional, if time allows) Write tests/test_app.py with a few basic
    assertions using Python's `assert` or the `unittest` module —
    e.g. that `calculate_streak` returns the correct number for a
    known set of check-in dates.

61. Do one final full click-through of the entire app pretending to be a
    brand-new user seeing it for the first time.

#### Files & Folders

- Modify: any file where a bug is found and fixed (likely app.py,
  models.py, ai_coach.py)

- Create (optional): tests/test_app.py

#### APIs, Libraries & Tools

- None new — testing existing integrations (Claude API, Gmail SMTP)
  under real conditions

#### Testing Tasks

- All functional requirements FR-1 through FR-19 from the PRD pass
  manual verification

- No crashes occur across the deliberate 'try to break it' pass

- Fresh-database first-run experience works correctly (Flow A)

#### Common Issues & Debugging Tips

- Bug only appears after multiple check-ins — test with at least 4–5
  consecutive simulated days, not just one

- Hard-to-reproduce timing bugs around 'today' — isolate by temporarily
  printing the calculated date/streak values to the terminal at each
  step

- Fixing one bug introduces another — re-run the full flow list after
  each fix, not just the specific case you patched

#### End-of-Day Checklist

- Every functional requirement from the PRD has been manually tested

- All discovered bugs are fixed and re-verified

- A full first-time-user walkthrough completes with no errors

- Codebase is committed to git as a stable checkpoint before deployment

#### Expected Project State & Screenshots to Capture

- Your test case list/checklist with pass marks (a simple text file or
  notes screenshot is fine)

- Any before/after of a notable bug fix (optional but good
  documentation)

#### Handoff Notes for Next Day

*Day 9 assumes: a fully tested, stable local codebase committed to git
with no known crashing bugs. Day 9 will deploy this exact codebase to
PythonAnywhere and configure the real scheduled task — no further
feature changes should happen during deployment unless something breaks
specifically because of the hosting environment.*


## DAY 9 — DEPLOYMENT

### Deploy to Production on PythonAnywhere


#### Objective

Get StudyStreak live on the public internet with a real URL, working
secrets, and the missed-day email running automatically on a real daily
schedule.

#### What I'll Learn

- How to deploy a Flask app to a real hosting platform

- How to configure environment variables/secrets safely in production

- How free scheduled tasks (cron-like jobs) work in a hosting platform

#### Features to Build

- Live, publicly accessible StudyStreak URL

- Production environment variables configured (API key, email
  credentials)

- Real daily Scheduled Task running scheduled_check.py automatically

#### Step-by-Step Implementation Plan

62. Create a free PythonAnywhere account (no credit card required for
    the free tier). I will walk you through this step by step — confirm
    when ready.

63. Open a Bash console on PythonAnywhere and clone your GitHub
    repository (`git clone \<your-repo-url>`).

64. Create a virtual environment on PythonAnywhere matching your local
    one and install dependencies from requirements.txt.

65. Set up a new Web App in the PythonAnywhere dashboard, choosing Flask
    and pointing it to your app.py (the dashboard walks you through the
    WSGI config file — follow its instructions to point it at your Flask
    `app` object).

66. Set your environment variables in production: PythonAnywhere doesn't
    read local .env files automatically for web apps, so add
    `os.environ\['ANTHROPIC_API_KEY'\] = '...'` style lines (or load
    via python-dotenv pointing at an uploaded .env file kept OUT of git)
    directly in the WSGI configuration file, OR upload a `.env` file
    directly to the server via the Files tab (never commit it to git).

67. Reload the web app from the PythonAnywhere dashboard and visit your
    new public URL to confirm the empty-state dashboard loads correctly.

68. Walk through the full first-time flow on the live URL: add a course,
    check in, confirm the AI nudge appears (this proves the Anthropic
    API key works in production).

69. Go to the 'Tasks' tab in PythonAnywhere and create a free Scheduled
    Task that runs `python3.x
    /home/yourusername/studystreak/scheduled_check.py` once daily at a
    fixed time (choose a time close to end-of-day in your timezone).

70. Test the live scheduled task path: either wait for its scheduled run
    or trigger scheduled_check.py manually via the Bash console, and
    confirm you receive a real email referencing your live production
    data.

71. Double-check the production database file (study_streak.db) is in a
    persistent location on PythonAnywhere's filesystem, not a temp
    directory that could be wiped.

#### Files & Folders

- No new application files — this day is purely deployment/configuration

- Possibly modify: WSGI configuration file (provided/edited via
  PythonAnywhere's dashboard, not part of your repo)

#### APIs, Libraries & Tools

- PythonAnywhere (hosting + free Scheduled Tasks)

- Production Anthropic API key and Gmail credentials (same services as
  local, now configured for the live server)

#### Testing Tasks

- Visit the live URL from a different device (e.g. your phone) to
  confirm true public accessibility

- Full click-through of add course → check in → AI nudge on the live
  site

- Confirm the Scheduled Task runs and a real email is received
  referencing live production data

- Restart/reload the web app and confirm course data persists (proves
  the database file is in a persistent location)

#### Common Issues & Debugging Tips

- 500 error on the live site — check the PythonAnywhere error log
  (available from the Web tab) for the actual Python traceback

- Environment variables not found in production — confirm they're set in
  the WSGI file or loaded correctly, since PythonAnywhere web apps don't
  automatically read a local-style .env the same way your dev machine
  does

- Scheduled Task doesn't run — confirm the exact file path and Python
  version in the task command match your actual project location on the
  server

#### End-of-Day Checklist

- StudyStreak is live at a public URL

- Full core loop (add course, check-in, AI nudge) works on the live site

- Scheduled Task is configured and has successfully sent at least one
  real production email

- Data persists across a web app reload

#### Expected Project State & Screenshots to Capture

- The live public URL in a browser address bar showing the working
  dashboard

- PythonAnywhere Scheduled Tasks tab showing the configured daily task

- The real production email received from the live scheduled task

#### Handoff Notes for Next Day

*Day 10 assumes: StudyStreak is fully live and functioning in
production. Day 10 is the final QA, documentation, and demo-preparation
day — treat any issues found as final polish, not new development.*


## DAY 10 — MAINTENANCE

### Final QA, Documentation & Capstone Demo Prep


#### Objective

Confirm the live product meets every Day 10 success metric from the PRD,
write clear documentation, and prepare a confident demo for the capstone
submission.

#### What I'll Learn

- How to write a README that lets someone else understand and run your
  project

- How to prepare and rehearse a technical product demo

- How to think about a basic maintenance/roadmap plan for a shipped v1.0

#### Features to Build

- No new features — final verification, documentation, and presentation
  only

#### Step-by-Step Implementation Plan

72. Re-check every row of the PRD's Section 4.2 (Day 10 Definition of
    Done table) directly against the live product and confirm each one
    is genuinely true.

73. Do one more full click-through on the live URL as a first-time user
    would, on both desktop and mobile browser widths.

74. Write a README.md in the project repo covering: what the project is,
    the problem it solves, the tech stack, how to run it locally, and a
    link to the live deployed URL.

75. Add a short 'Known Limitations & Future Roadmap' section to the
    README, pulling directly from PRD Section 6.2 (out of scope items) —
    this shows intentional scoping, not unfinished work.

76. Prepare your demo script: a 3–5 minute walkthrough hitting (1) the
    problem in one sentence, (2) adding a course live, (3) checking in
    and showing the AI nudge, (4) explaining/showing the missed-day
    email (can reference the screenshot from Day 9 if a live miss isn't
    demo-able in the moment), (5) one sentence on future roadmap.

77. Do a dry run of the demo out loud, timing it, and adjust if it runs
    long.

78. Commit and push the final README and any last small fixes to GitHub.

79. Write a short personal retrospective (a few bullet points is
    enough): what went well, what was hardest, and what you'd do
    differently — useful both for the challenge reflection and for your
    own growth as a builder.

#### Files & Folders

- Create: README.md

- Modify: any file needing a final small fix discovered during this last
  QA pass

#### APIs, Libraries & Tools

- None new — final verification of everything already integrated

#### Testing Tasks

- Every item in the PRD's Day 10 Definition of Done table is verified
  true on the live product

- Demo script has been rehearsed at least once within the intended time
  limit

#### Common Issues & Debugging Tips

- Running out of time for polish — prioritize fixing anything that would
  visibly break during the live demo over invisible code cleanup

- Demo relies on a real missed-day email arriving live — have the Day 9
  email screenshot ready as a backup so the demo doesn't depend on
  timing

#### End-of-Day Checklist

- All PRD Day 10 success metrics verified on the live product

- README.md complete with setup instructions, tech stack, live URL, and
  roadmap

- Demo script written and rehearsed

- Final code pushed to GitHub

- Short personal retrospective written

#### Expected Project State & Screenshots to Capture

- Final live dashboard, fully polished

- The completed README.md file

- Any final confirmation of the live URL working end-to-end

#### Handoff Notes for Next Day

*This is the final day — StudyStreak v1.0 is complete, deployed, tested,
and documented. Future roadmap items (multi-course support, quizzes,
predictive analytics, etc.) are captured in the README and PRD Section
6.2 for any post-capstone continuation.*

Final Note

Completing every day's checklist in order results in a polished,
deployed StudyStreak v1.0 by Day 10 — live, tested, documented, and
demo-ready. If you fall behind on any single day, prioritize finishing
that day's End-of-Day Checklist over starting the next day's steps; the
days are sequential and each one's Handoff Notes assume the prior day is
genuinely complete.
