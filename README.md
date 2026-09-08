# 📧 Gmail Forwarding Bot - Complete Guide for Beginners

Welcome! This is a **Telegram Bot + Web Dashboard** system that helps you manage email access for multiple users. Think of it as a gatekeeper service where admins can approve/deny user access to shared email accounts, set expiry dates, and users can view forwarded emails through Telegram or a web interface.

> **In Simple Terms:** Imagine you have a shared Netflix account email, and you want to give different people access to it but control who can see what and for how long. This bot automates that!

---

## 🎯 What This Bot Does (Features)

### Unified Telegram/Web email management

The Telegram admin email-management workflow supports both Telegram users and
Web-only users. Web targets are represented as `web_<id>` (for example,
`web_4`) while Telegram targets remain numeric. Assignment, duplicate checks,
expiry selection, confirmation/cancellation, renewal, removal, replacement,
and expiry editing use the correct backend automatically. Duplicate detection
checks both Telegram and Web assignments and excludes the selected target.
Web-only users do not need a Telegram chat; notifications are optional and do
not prevent a successful assignment.

### For **Regular Users:**
- Start a conversation with the bot to request access to email accounts
- View emails assigned to them via Telegram or a web dashboard
- See expiry dates for their access
- Request renewal of expired access

### For **Admins:**
- Approve/deny user access requests
- Assign emails to users
- Set expiry dates (after which access automatically expires)
- View all users and their access status
- Create backups of all data
- Broadcast messages to all users
- Manage sub-admins

### For **The System:**
- Automatically fetches emails from a Gmail account using IMAP (Gmail's secure protocol for accessing emails)
- Extracts important info from emails (like Netflix codes)
- Stores everything safely in a database
- Runs on Telegram (messaging app) AND has a web dashboard

## 🔌 API Keys and External API Usage

Web users can use their assigned emails from another application through the
authenticated API. First configure the public address in `.env`:

```env
PUBLIC_API_URL=https://your-domain.com
```

Do not add `/api` to this value. Log in to the web dashboard, open **Profile**,
create a named API key, and keep the generated key private. It is equivalent
to a password. The complete interactive guide is available at **API Guide**
in the dashboard.

Use the key in the recommended Bearer header:

```bash
curl -H "Authorization: Bearer YOUR_API_KEY" \
  https://your-domain.com/api/emails
```

The alternative header is `X-Api-Key: YOUR_API_KEY`. Available endpoints are:

| Method | Endpoint | Description |
|---|---|---|
| GET | `/api/health` | Public health check |
| GET | `/api/emails` | List the authenticated user's assigned emails |
| GET | `/api/permissions` | List the user's enabled categories |
| POST | `/api/fetch` | Fetch assigned email content |

Fetch requests use JSON. Use `email` for one email or `emails` for a list, and
provide `choice` such as `2FA`, `reset`, `household`, `verify_email`,
`verification`, or `tv_login`:

```bash
curl -X POST \
  -H "Authorization: Bearer YOUR_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"email":"shared@example.com","choice":"2FA"}' \
  https://your-domain.com/api/fetch
```

The account must have both the relevant category permission and an active
email assignment. Requests support up to 50 emails. Never commit keys to Git,
place them in browser JavaScript, or share them publicly. Revoke an exposed key
from Profile and create a replacement. API responses use HTTP `401` for missing
or invalid keys, `403` for permissions/assignment problems, and `400` for bad
request data.

---

## 📋 Prerequisites - What You Need Before Starting

Before you can run this bot, you need:

1. **Python 3.8 or higher** - [Download here](https://www.python.org/downloads/)
   - *What is Python?* It's a programming language that runs this bot
   - When installing, **CHECK "Add Python to PATH"** (important!)

2. **Git** (optional but recommended) - [Download here](https://git-scm.com/)
   - *What is Git?* It helps you manage code and download this project

3. **A Gmail Account** - Any Gmail account that will send/receive forwarded emails
   - This must have **2-factor authentication enabled** (for security)

4. **A Telegram Bot Token** - Get it from [BotFather](https://t.me/botfather)
   - Send `/newbot` to @BotFather on Telegram, follow the steps, and copy the token
   - *What is a token?* It's like a password that lets the bot access Telegram

5. **Your Telegram User ID** - Get it from [IDBot](https://t.me/userinfobot)
   - Send any message to @userinfobot and it will tell you your ID
   - This is needed to make you the admin of the bot

6. **A Hosting Service** (optional, for running 24/7)
   - **Railway** (recommended, free tier available) - [railway.app](https://railway.app)
   - **Heroku** (or any other service)
   - OR run it on your own computer

---

## 🚀 Installation Steps (Step-by-Step for Beginners)

### Step 1: Download the Project

**Using Git (Recommended):**
```bash
git clone <repository-url>
cd "Gmail forwarding bot/web + bot"
```

**OR Using ZIP:**
- Download as ZIP from GitHub
- Extract it to a folder on your computer
- Open PowerShell/Terminal in that folder

### Step 2: Install Python Packages

Python packages are like plugins that add extra features to Python. Run this command:

```bash
pip install -r requirements.txt
```

What does this do? It reads `requirements.txt` and downloads all the necessary packages:
- `pyTelegramBotAPI` - Lets your bot communicate with Telegram
- `Flask` - Creates the web dashboard
- `python-dotenv` - Reads your secret keys safely
- `pytz` - Handles different time zones

### Step 3: Create a `.env` File

The `.env` file is where you store secret information (like passwords). Create a file named `.env` in the main folder:

```env
BOT_TOKEN=123456789:ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghij
EMAIL_USER=your-gmail@gmail.com
EMAIL_PASS=your-app-specific-password
ADMIN_ID=123456789
FLASK_SECRET_KEY=your-secret-key-here
```

**How to get each value:**

- **BOT_TOKEN**: From @BotFather on Telegram (send `/newbot`)
- **EMAIL_USER**: Your Gmail address
- **EMAIL_PASS**: [Create an app-specific password](https://myaccount.google.com/apppasswords) (NOT your regular Gmail password!)
  - Go to myaccount.google.com → Security → App passwords (requires 2FA enabled)
  - Select "Mail" and "Windows Computer"
  - Copy the 16-character password
- **ADMIN_ID**: Your Telegram user ID (from @userinfobot)
- **FLASK_SECRET_KEY**: Random secure string (run: `python -c "import secrets; print(secrets.token_hex(32))"`)

---

## ▶️ How to Run the Bot

### **Option 1: Run Locally (On Your Computer)**

```bash
python app.py
```

This starts both:
- The Telegram bot (polling for messages)
- The Flask web server (usually at `http://localhost:5000`)

**Keep this terminal open** while the bot is running. Close it to stop the bot.

### **Option 2: Run on Railway (24/7 in Cloud)**

Railway is a free hosting service that keeps your bot running all the time:

1. Push your code to GitHub
2. Go to [railway.app](https://railway.app) and sign up
3. Create a new project → Deploy from GitHub
4. Add environment variables (BOT_TOKEN, EMAIL_USER, etc.)
5. Railway will automatically run: `python app.py` (defined in `Procfile`)

---

## 🏗️ Project Structure - Understanding the Files

The code is split into different files by purpose. This makes it easier to fix bugs and add features:

```text
📁 email_bot/  (All bot files)
│
├── 🔧 CORE FILES (Essential for everything)
│   ├── main.py              # Starts the bot, loads all modules in correct order
│   ├── config.py            # Stores all settings (tokens, paths, database setup)
│   └── storage.py           # Saves/loads data from database and JSON files
│
├── 🔐 PERMISSION & ACCESS FILES
│   ├── permissions.py       # Who can access what (user levels: admin, sub-admin, user)
│   └── expiry.py            # Manages access expiry dates & reminders
│
├── 📨 EMAIL FILES
│   ├── email_fetcher.py     # Connects to Gmail and downloads emails
│   ├── extractors.py        # Finds important info in emails (links, codes, etc.)
│   └── broadcast.py         # Sends messages to many users at once
│
├── 💬 TELEGRAM INTERFACE
│   ├── user_handlers.py     # Handles /start, requests from regular users
│   ├── admin_handlers.py    # Handles admin commands and approvals
│   ├── keyboards.py         # Creates buttons for Telegram messages
│   ├── messages.py          # Text templates for bot messages
│   └── emoji.py             # Fancy emojis for nice looking messages
│
├── 📁 SUPPORT FILES
│   ├── utils.py             # Helper functions used everywhere
│   └── backup.py            # Creates data backups
│
└── 🌐 WEB INTERFACE (in app.py)
    └── api.py               # API endpoints for web dashboard
│
📁 templates/  (Web page designs)
│   ├── base.html            # Main layout template
│   ├── login.html           # Login page
│   ├── dashboard.html       # Main user dashboard
│   ├── emails.html          # List of emails
│   ├── users.html           # Admin: manage users
│   └── ... (other pages)
│
📁 static/  (Images, CSS, JavaScript)
│   ├── css/style.css        # Website styling
│   └── js/app.js            # Website interactivity
│
📄 app.py              # Main entry point (starts everything)
📄 requirements.txt    # List of all Python packages needed
📄 Procfile           # Instructions for Railway.app
```

**Simple Explanation:**
- `email_bot/` = The Telegram bot logic
- `app.py` = The entry point (what runs when you type `python app.py`)
- `templates/` = The HTML pages for the web dashboard
- `static/` = CSS/JavaScript for styling the web pages

---

## 💾 Database Files (Automatically Created)

When you run the bot for the first time, it creates these files in the `data/` folder:

- `email_bot.sqlite3` - Main database (stores users, emails, permissions)
- `approved_users.json` - List of approved users
- `user_email_assignments.json` - Which user can access which email
- `admin_expiry_permissions.json` - Expiry dates for access
- `access_log.txt` - Log of who accessed what and when

**Don't delete these!** They contain all your bot's data.

---

## 🤖 How It Works (Workflow)

### **User Registration Flow:**

```
1. User sends /start → Bot welcomes them
2. User requests access to an email → Request stored in database
3. Admin gets notified → Admin reviews and approves/denies
4. If approved: User can now view emails for that account
5. Access automatically expires on set date → User needs to request renewal
```

### **Email Fetching Flow:**

```
1. Bot connects to Gmail every minute (customizable)
2. Downloads new emails using IMAP protocol
3. Extracts important info (links, codes, sender)
4. Stores in database
5. Users can view via Telegram or web dashboard
```

---

## 🔧 Configuration - Customizing the Bot

All settings are in `email_bot/config.py`. Here are the main ones:

| Setting | What it does |
|---------|------------|
| `BOT_TOKEN` | Token for Telegram bot (from @BotFather) |
| `EMAIL_USER` | Gmail address to forward emails from |
| `EMAIL_PASS` | Gmail app-specific password (NOT regular password) |
| `ADMIN_ID` | Your Telegram user ID (admin access) |
| `DATA_DIR` | Where to save database & JSON files |
| `POLLING_INTERVAL` | How often to check for new emails (seconds) |

---

## 📱 Using the Bot - Commands

### **For Regular Users:**

- `/start` - Start the bot, register yourself
- `/accounts` - See which emails you have access to
- `/profile` - View your profile
- `/request` - Request access to a new email

### **For Admins:**

- `/admin` - Open admin panel
- `/approve [user_id]` - Approve a user's request
- `/deny [user_id]` - Deny a user's request
- `/users` - List all users
- `/broadcast` - Send a message to all users
- `/backup` - Create a backup of all data

---

## 🌐 Web Dashboard Access

After running `python app.py`, open your browser and go to:

```
http://localhost:5000
```

**First time?** Create an account to log in. If you're on Railway, use their provided domain instead of localhost.

### Pages Available:

- **Dashboard** - Overview of emails and status
- **My Emails** - View assigned emails
- **Profile** - Your account info
- **Users** (Admin Only) - Manage all users
- **Settings** (Admin Only) - Configure bot settings
- **Logs** (Admin Only) - See activity logs

---

## ❌ Troubleshooting - Common Problems

### **"ModuleNotFoundError" when running `python app.py`**

```bash
# Solution: Install requirements again
pip install -r requirements.txt
```

### **Bot doesn't respond on Telegram**

1. Check `.env` file - is `BOT_TOKEN` correct?
2. Make sure terminal shows "Bot is polling" message
3. Restart the bot: Close terminal and run `python app.py` again

### **"Gmail login failed"**

1. Check `EMAIL_USER` and `EMAIL_PASS` in `.env`
2. Make sure you're using **app-specific password**, not your regular Gmail password
3. Enable 2-factor authentication on your Gmail account
4. Visit [google.com/accounts/security](https://myaccount.google.com/security) to check

### **"Connection refused" error**

This means another program is using port 5000. Either:
- Close other programs
- Change the port in `app.py` (look for `app.run(port=5000)`)

### **Web dashboard won't load**

Make sure the Flask server is running:
- Terminal should show: `Running on http://localhost:5000`
- Try clearing browser cache (Ctrl+Shift+Delete)

---

## 🔐 Security Tips

1. **Never share your `.env` file** - It contains passwords
2. **Use app-specific passwords**, not your main Gmail password
3. **Change `FLASK_SECRET_KEY` for production** - Make it random and strong
4. **Enable 2-factor authentication** on the Gmail account
5. **Keep your bot token secret** - Don't share it publicly
6. **Regularly backup data** - Use `/backup` command

---

## 📝 File Descriptions - What Each Module Does

### **config.py**
Stores all global settings like bot token, database path, timezone, etc. Think of it as the "settings file" for the entire system.

### **storage.py**
Handles saving and loading data. When you approve a user, this module saves that to the database. When you view users, this module loads from the database.

### **email_fetcher.py**
Connects to Gmail and downloads emails. Uses IMAP (a special protocol for reading emails).

### **permissions.py**
Checks if a user is allowed to do something. "Can this user see this email? Is their access expired?"

### **expiry.py**
Manages when access runs out. "User got access on Jan 1, expires on Feb 1 - remind them on Jan 25."

### **user_handlers.py**
Responds to messages from regular users. When a user sends `/start` or requests something, this module handles it.

### **admin_handlers.py**
Responds to admin commands. When you send `/admin`, this module shows the admin panel.

### **messages.py**
Contains all the text the bot sends. Instead of writing messages everywhere in the code, they're all stored here for easy editing.

### **keyboards.py**
Creates buttons for Telegram messages. Instead of typing commands, users can click buttons.

### **api.py** (in app.py)
Connects the web dashboard to the bot's data. When you view emails on the website, this module fetches data from the database.

---

## 🆘 Getting Help

- Check existing error messages in the terminal
- Look at `access_log.txt` for activity history
- Read through `config.py` for available settings
- Check that all environment variables are set correctly in `.env`

---

## 📦 What Are These Python Packages?

Since you're new, here's what each package does:

| Package | What it does |
|---------|------------|
| `pyTelegramBotAPI` | Lets your Python code talk to Telegram |
| `Flask` | Creates a web server so you can access a website |
| `python-dotenv` | Lets you store secrets safely in `.env` file |
| `pytz` | Handles time zones correctly |

---

## 🎓 Next Steps

1. ✅ Install Python and requirements
2. ✅ Get all tokens/credentials (BOT_TOKEN, EMAIL credentials, ADMIN_ID)
3. ✅ Create `.env` file with all values
4. ✅ Run `python app.py`
5. ✅ Message your bot on Telegram with `/start`
6. ✅ Open `http://localhost:5000` in browser
7. ✅ Create account and explore the dashboard

---

## 📞 Support Commands in Bot

Send these to your bot for help:

```
/start      → Begin using the bot
/help       → Show available commands
/admin      → Admin panel (admins only)
/profile    → Your profile info
```

---

**Happy Botting! 🎉** If you have questions, check the troubleshooting section first!
requirements.txt             # Python packages required by Railway
README.md                    # This documentation
FILE_TREE.txt                # Plain file tree reference
email_bot/single_file_backup.py # Rollback copy of latest working single-file bot
```

---

## File-by-File Explanation

### `app.py`

This is the entry point for Railway.

It imports `run_bot()` from `email_bot/main.py` and starts the bot.

You normally do not need to edit this file unless you change the package name.

---

### `email_bot/main.py`

This file controls startup and polling.

Responsibilities:

- Defines the module loading order.
- Loads all clean modules into one shared runtime namespace.
- Runs startup checks.
- Loads saved JSON data.
- Initializes SQLite mirror database.
- Starts background health checks.
- Starts expiry reminder thread.
- Starts Telegram polling with `bot.infinity_polling()`.

Important section:

```python
MODULE_ORDER = [
    "config.py",
    "emoji.py",
    "storage.py",
    "permissions.py",
    "messages.py",
    "utils.py",
    "extractors.py",
    "email_fetcher.py",
    "keyboards.py",
    "backup.py",
    "broadcast.py",
    "admin_handlers.py",
    "user_handlers.py",
    "expiry.py",
]
```

If you add a new module, add it here in the correct order.

Do not randomly change this order because many functions depend on earlier files.

---

### `email_bot/config.py`

This file contains configuration, constants, global state, and the Telegram bot object.

Responsibilities:

- Loads `.env` values.
- Validates required environment variables.
- Creates `bot = TeleBot(BOT_TOKEN)`.
- Sets data directory path.
- Defines global dictionaries used by the bot.
- Defines permissions/categories.
- Defines date settings.
- Defines SQLite path.

Important environment variables:

```env
BOT_TOKEN=your_telegram_bot_token
EMAIL_USER=your_email_login
EMAIL_PASS=your_email_password_or_app_password
ADMIN_ID=your_telegram_user_id
ADMIN_CHANNEL_ID=optional_channel_id_or_username
```

Important constants:

```python
DEFAULT_EXPIRY_DAYS = 30
DATE_FORMAT = "%Y-%m-%d"
EMAILS_PER_MANAGEMENT_PAGE = 50
MAX_EMAILS_PER_MESSAGE = 20          # normal-user safety limit
MAX_ADMIN_EMAILS_PER_MESSAGE = 500   # admin bulk actions
```

Important global data:

```python
APPROVED_USERS
SUB_ADMIN_USERS
SUB_ADMIN_ASSIGNMENTS
USER_EMAIL_ASSIGNMENTS
RENEWAL_DECISIONS
```

Edit this file when you need to change bot-wide constants or environment handling.

---

### `email_bot/emoji.py`

This file handles emoji mode for the bot. By default, the package uses **normal Unicode emojis everywhere** so users without Telegram Premium can see them, including inline buttons.

Responsibilities:

- Stores your custom emoji IDs in `PREMIUM_EMOJI_IDS`.
- Stores friendly names in `PREMIUM_EMOJIS`, like `success`, `email`, `back`, `crown`, `diamond`.
- Reads `USE_PREMIUM_EMOJI` and `USE_PREMIUM_INLINE_BUTTONS` from Railway Variables / `.env`.
- When premium mode is off, it does not patch messages or inline buttons, so normal emojis stay visible.
- When premium mode is on, it converts normal emojis inside message text/captions to Telegram premium emoji HTML.
- When premium inline button mode is on, it adds premium icons to inline keyboard buttons using `icon_custom_emoji_id`.
- Keeps `types.InlineKeyboardButton` as a class, so TeleBot callback parsing does not crash.
- Retries without premium button icons if Telegram or an old library version rejects the new button field.

Default mode after this fix:

```env
USE_PREMIUM_EMOJI=false
USE_PREMIUM_INLINE_BUTTONS=false
```

This shows normal emojis everywhere, including inline buttons.

Premium emoji format used in normal messages/captions when `USE_PREMIUM_EMOJI=true`:

```html
<tg-emoji emoji-id="CUSTOM_EMOJI_ID">✅</tg-emoji>
```

Inline keyboard buttons are different. Telegram does not parse `<tg-emoji>` HTML inside button text, so this package uses Bot API button field:

```python
icon_custom_emoji_id="5325538810275055890"
```

You do not need to manually change every button. Existing buttons like this are auto-detected:

```python
types.InlineKeyboardButton("✅ Confirm Approval", callback_data="confirm")
```

When `USE_PREMIUM_INLINE_BUTTONS=true`, the premium patch turns it into a custom emoji icon plus cleaned text:

```text
[premium ✅ icon] Confirm Approval
```

When `USE_PREMIUM_INLINE_BUTTONS=false`, it stays as the normal visible button text:

```text
✅ Confirm Approval
```

For new buttons, you can also use the helper:

```python
premium_button("Confirm Approval", emoji="success", callback_data="confirm")
```

Useful helpers:

```python
pe("success")      # premium HTML emoji for messages/captions
pe_id("success")   # custom emoji ID for button icons
premium_button(...) # InlineKeyboardButton with icon_custom_emoji_id
```

Edit this file when:

- You add new custom emoji IDs.
- You want to change emoji aliases.
- You want to change the friendly names in `PREMIUM_EMOJIS`.
- You want to disable or modify premium emoji conversion.

Do not replace `types.InlineKeyboardButton` with a normal function. That causes this crash:

```text
'function' object has no attribute 'de_json'
```

This package avoids that crash by patching the existing class safely instead of replacing it.

---

### `email_bot/storage.py`

This file handles saved data.

Responsibilities:

- Save/load approved users.
- Save/load sub-admin users.
- Save/load sub-admin assignments.
- Save/load user email assignments.
- Save/load renewal decisions.
- Migrate old `.pkl` files to `.json`.
- Migrate files to Railway persistent volume.
- Initialize SQLite database.
- Mirror email assignments into SQLite.
- Store renewal history.
- Run data directory health check.

Main persistent files:

```text
approved_users.json
sub_admin_users.json
sub_admin_assignments.json
user_email_assignments.json
renewal_decisions.json
access_log.txt
email_bot.sqlite3
```

Railway persistent data should live in:

```text
/data
```

This file is important because Railway containers can restart. JSON and SQLite keep your bot state alive.

Edit this file only when changing data format or storage behavior.

---

### `email_bot/permissions.py`

This file handles user role logic.

Responsibilities:

- Check if user is super admin.
- Check if user is sub admin.
- Check if user is any admin.
- Get cached Telegram display names.
- Get user permissions.
- Get users manageable by an admin/sub-admin.
- Update Telegram command list per user.

Main roles:

```text
Super Admin  -> full control
Sub Admin    -> manages assigned users only
Normal User  -> uses assigned emails/categories only
```

Edit this file when changing admin/sub-admin rules.

---

### `email_bot/keyboards.py`

This file is reserved for keyboard helpers.

Responsibilities:

- Keep `InlineKeyboardMarkup` building code organized.
- Store reusable button/keyboard functions.
- Make admin panels and user panels easier to maintain.

Current package keeps many older keyboard blocks inside handlers because this was converted from a large single file safely. Future cleanup can move more keyboard code here.

Important rule:

Do not monkey-patch `types.InlineKeyboardButton`. TeleBot needs it as a class internally.

---

### `email_bot/messages.py`

This file contains message design and template text.

Responsibilities:

- `BotDesign` class.
- Header/divider styles.
- Welcome message.
- Admin panel message.
- Monitoring activated message.
- Result found message.
- Email not found message.
- Error message.
- Approval/denial messages.
- Broadcast preview/completion messages.
- Backup success message.
- Email assignment display messages.

Edit this file when you want to change bot wording, layout, style, or emoji placement.

Example areas:

```python
BotDesign.welcome_message()
BotDesign.admin_panel_main(user_id)
BotDesign.result_message(category, email, data, fetch_time)
BotDesign.user_email_assignment_message(...)
```

---

### `email_bot/email_fetcher.py`

This file handles email login and email scanning.

Responsibilities:

- Connect to IMAP.
- Search mailbox emails.
- Decode email subjects/body.
- Find Netflix-related emails.
- Fetch multiple accounts concurrently.

Main functions:

```python
fetch_email_for_account(receiver_email, category)
fetch_emails_concurrently(email_list, category)
```

Edit this file when:

- IMAP server/login behavior changes.
- Email scanning is too slow.
- Netflix email format changes.
- You need to improve body decoding.

---

### `email_bot/extractors.py`

This file extracts useful data from email text.

Responsibilities:

- Extract login code.
- Extract verification code.
- Extract reset link.
- Extract household links.
- Extract verify email link.
- Extract TV login link.

Main functions:

```python
extract_login_code(text)
extract_verification_code(text)
extract_reset_link(text)
extract_household_links(text)
extract_verify_email_link(text)
extract_tv_login_link(text)
```

Edit this file when Netflix changes link/code formats.

---

### `email_bot/expiry.py`

This file handles subscription expiry logic.

Responsibilities:

- Parse expiry dates.
- Normalize date formats.
- Add exact 30-day renewal.
- Parse bulk expiry updates.
- Assign email expiry.
- Remove email assignment.
- Check expired emails.
- Sort emails by expiry date.
- Filter active/expired/tomorrow/next-7 emails.
- Send expiry reminders.
- Handle renew / do-not-renew callbacks.

Important date format:

```text
YYYY-MM-DD
```

Supported user input examples may include normalized forms like:

```text
2026-07-02
02-07-2026
02/07/2026
```

Expiry update behavior:

#### Method 1: Email-only first, then date or day increase

Admin sends only emails:

```text
email1@example.com
email2@example.com
```

If the list is too long for one Telegram message, send the remaining email chunks in the next messages. The bot keeps collecting them in the same session.

Then send either a fixed expiry date:

```text
2026-08-01
```

Or send only a day number to renew/increase each selected email from its current expiry:

```text
30
```

`30` means current expiry + 30 exact days for every selected email.

#### Method 2: Email + date on each line

Admin sends:

```text
email1@example.com 2026-08-01
email2@example.com 2026-08-05
```

Bot updates directly.

The Method 1 fix is included. Email-only lines are no longer treated as invalid bulk-update lines.

Edit this file when changing subscription rules, renewal days, date parsing, or reminder behavior.

---

### `email_bot/admin_handlers.py`

This file contains admin-side Telegram handlers.

Responsibilities:

- `/start`
- `/accounts`
- `/renew`
- `/approve`
- `/admin`
- `/backup`
- Admin panel callbacks.
- Email management callbacks.
- Assign emails to users.
- Edit expiry dates.
- Replace emails.
- Bulk remove emails.
- Bulk renew emails.
- Search users/emails.
- Expiring tomorrow admin panel.

Important flows:

```text
/admin -> admin dashboard
/admin -> 💫 User Management -> Telegram users only
/admin -> 🌐 Web manage -> Web accounts and Web-user email access
/admin -> 📧 Email Management -> Telegram-user email assignments
Telegram email-management callbacks -> assign/remove/replace/edit expiry
Web-manage callbacks -> approve/pause Web accounts, edit permissions, add/remove Web-user emails, edit expiry
Expiring-tomorrow panel -> renewal actions for manageable Telegram users and Web users
```

The Telegram admin panel keeps account types separate. **💫 User Management** lists
Telegram users and does not include Web-only accounts. **🌐 Web manage** is the
dedicated Super Admin flow for Web accounts, including Web-user permissions and
email assignments. **📧 Email Management** is for Telegram-user assignments;
Web-user email operations remain under **🌐 Web manage**.

Edit this file when changing admin features or admin buttons.

---

### `email_bot/user_handlers.py`

This file contains user-facing handlers and remaining approval/permission callbacks.

Responsibilities:

- Handle user email input.
- Show category choices.
- Process selected category.
- Fetch codes/links for user.
- Retry failed email searches.
- Approve/disapprove user callbacks.
- Grant/revoke category permissions.
- Manage sub-admin user assignment callbacks.
- Broadcast creation/confirmation callbacks.
- Promote/demote sub-admin callbacks.

Main user flow:

```text
User sends email(s)
-> bot checks access
-> bot asks category
-> user selects category
-> bot searches mailbox
-> bot sends code/link/result
-> bot logs access to admin/sub-admin
```

Edit this file when changing normal user behavior or approval/permission flows.

---

### `email_bot/broadcast.py`

This file handles sending broadcasts.

Responsibilities:

- Send text broadcast.
- Send photo broadcast.
- Count successful sends.
- Count failed sends.
- Optionally exclude main admin.

Main function:

```python
broadcast_to_users(broadcast_data, exclude_admin=True)
```

Edit this file when improving broadcast speed, retry behavior, or target filtering.

---

### `email_bot/backup.py`

This file creates backups of important bot data.

Responsibilities:

- Create timestamped backup folder.
- Copy JSON files and access log.
- Return backup file list and backup path.

Main function:

```python
create_data_backup()
```

Backup files include:

```text
approved_users.json
sub_admin_users.json
sub_admin_assignments.json
user_email_assignments.json
renewal_decisions.json
access_log.txt
```

Edit this file when adding more persistent files to backup.

---

### `email_bot/utils.py`

This file contains shared helper functions.

Responsibilities:

- Parse emails from pasted text.
- Clean URLs.
- Send safe Telegram messages.
- Notify main admin.
- Send admin channel updates.
- Renewal decision helpers.
- Renewal prompt helpers.
- Admin notification helpers.
- Remove users completely.
- Demote sub-admins.
- Log user access.
- Format admin/channel report lines.

Important functions:

```python
parse_emails(input_text)
safe_send_message(chat_id, text, parse_mode=None, **kwargs)
notify_main_admin(message, parse_mode=None)
send_admin_channel_update(message)
notify_admins(message, ...)
log_user_access(...)
```

Edit this file when changing common behavior used by many handlers.

---

### `email_bot/single_file_backup.py`

This is a rollback copy of the latest crash-fixed single-file bot.

Use it only if the modular package has an issue and you need to quickly go back.

Do not edit this unless you intentionally want to update the rollback copy.

---

## Runtime Flow

Startup order:

```text
python app.py
-> email_bot.main.run_bot()
-> load modules in MODULE_ORDER
-> verify data directory
-> migrate old data if needed
-> load JSON data
-> ensure super admin permissions
-> normalize email assignment data
-> initialize SQLite
-> set Telegram commands
-> start data health monitor
-> start expiry reminder thread
-> start bot.infinity_polling()
```

User request flow:

```text
User sends email
-> user_handlers/admin_handlers receive message
-> permission check
-> category selection
-> email_fetcher scans mailbox
-> extractors parse code/link
-> messages format response
-> emoji upgrades message emojis
-> bot sends result
-> utils logs access
```

Admin email-management flow:

```text
/admin
-> 📧 Email Management
-> select Telegram user
-> assign/remove/replace/edit expiry
-> expiry.py updates memory
-> storage.py saves JSON and SQLite mirror
```

Web-account management flow:

```text
/admin
-> 🌐 Web manage
-> select Web account
-> approve/pause account or change permissions
-> Manage Emails
-> add/remove emails or edit expiry
-> storage.py saves Web-user assignments
```

---

## Required Environment Variables

Set these in Railway Variables or local `.env`:

```env
BOT_TOKEN=123456:telegram_bot_token
EMAIL_USER=your_email@example.com
EMAIL_PASS=your_email_app_password
ADMIN_ID=123456789
ADMIN_CHANNEL_ID=-1001234567890
```

`ADMIN_CHANNEL_ID` is optional.

Where to add them:

- **Railway:** open your service → **Variables** → add `BOT_TOKEN`, `EMAIL_USER`, `EMAIL_PASS`, `ADMIN_ID`, and optional `ADMIN_CHANNEL_ID`.
- **Local PC/VPS:** copy `.env.example` to `.env`, then fill your real values.

Do not commit/share your real `.env` file. `.env.example` is only a template.

If using a channel:

1. Add the bot to the Telegram channel.
2. Make the bot admin in that channel.
3. Use channel ID like `-1001234567890` or username like `@yourchannel`.

---

## Railway Deployment

Recommended Railway settings:

```text
Start Command: python app.py
```

Or use the included Procfile:

```text
worker: python app.py
```

Make sure Railway has a persistent volume mounted at:

```text
/data
```

The bot will use `/data` for JSON, SQLite, backups, and logs.

---

## Local Run

Install dependencies:

```bash
pip install -r requirements.txt
```

Create `.env` from the included template:

```bash
cp .env.example .env
```

Then edit `.env`:

```env
BOT_TOKEN=your_bot_token
EMAIL_USER=your_email
EMAIL_PASS=your_email_password
ADMIN_ID=your_telegram_id
ADMIN_CHANNEL_ID=

# Emoji mode
# Keep false if Telegram Premium ended or you want normal emojis visible for everyone.
USE_PREMIUM_EMOJI=false
USE_PREMIUM_INLINE_BUTTONS=false
```

Run:

```bash
python app.py
```

---

## Emoji / Premium Emoji Notes

Default is normal emoji mode:

```env
USE_PREMIUM_EMOJI=false
USE_PREMIUM_INLINE_BUTTONS=false
```

Use this if your Telegram Premium ended. Normal emojis remain visible in messages and inline buttons.

Premium emoji conversion is applied only when you enable it.

Works in messages/captions through `<tg-emoji>` HTML when `USE_PREMIUM_EMOJI=true`:

```text
bot.send_message(...)
bot.edit_message_text(...)
bot.send_photo(... caption=...)
bot.send_document(... caption=...)
```

Works in inline keyboard buttons through `icon_custom_emoji_id` only when `USE_PREMIUM_INLINE_BUTTONS=true`:

```python
types.InlineKeyboardButton("✅ Confirm", callback_data="confirm")
```

When inline premium mode is enabled, the emoji patch auto-detects the leading emoji and sends it as a premium button icon. It also removes the duplicate Unicode emoji from the button label. When inline premium mode is disabled, buttons keep normal Unicode emojis.

For new button code, you can use:

```python
premium_button("Confirm", emoji="success", callback_data="confirm")
premium_button("Back", emoji="back", callback_data="back_to_admin")
premium_button("Email Management", emoji="email", callback_data="admin_emails")
```

Important compatibility note:

- If your Telegram Premium ended, keep `USE_PREMIUM_EMOJI=false` and `USE_PREMIUM_INLINE_BUTTONS=false`.
- If your Telegram/Bot API stack accepts `icon_custom_emoji_id`, buttons can show premium icons when enabled.
- If it rejects that field, the bot retries automatically with normal Unicode emojis so it does not crash.

If you get new custom emoji IDs, add them in:

```text
email_bot/emoji.py
```

---

## Expiry Update Guide

### Method 1 - Same date or renew-days for multiple emails

Step 1: paste emails only:

```text
abc@example.com
xyz@example.com
```

For very long lists, send split parts one by one:

```text
part1@example.com
part2@example.com
```

Then send the next message with more emails:

```text
part3@example.com
part4@example.com
```

The bot keeps adding them to the same selected list.

Step 2: when all emails are selected, send one of these:

Fixed expiry date:

```text
2026-08-01
```

Or renew/increase by days:

```text
30
```

`30` means every selected email gets renewed by adding 30 exact days to its current expiry.

### Method 2 - Different date per email

Paste email and date together:

```text
abc@example.com 2026-08-01
xyz@example.com 2026-08-05
```

Bot updates directly.

---

## Data Files

The bot stores state in JSON and SQLite.

```text
approved_users.json              # approved users and permissions
sub_admin_users.json             # sub-admin IDs
sub_admin_assignments.json       # which users each sub-admin can manage
user_email_assignments.json      # assigned emails and expiry dates
renewal_decisions.json           # renewal yes/no decisions
access_log.txt                   # user access logs
email_bot.sqlite3                # SQLite mirror and renewal history
```

JSON is the compatibility backup. SQLite is used as a safer searchable mirror for email assignment data.

---

## Backup

Admins can use `/backup` or the admin panel backup button. The bot also creates
an automatic backup every day at **12:00 AM Indian Standard Time
(`Asia/Kolkata`)** while the bot process is running.

Backups are created under:

```text
/data/backups/YYYYMMDD_HHMMSS_IST/
```

Backup currently includes important JSON files and `access_log.txt`.

---

## Troubleshooting

### Bot crashes with missing environment variables

Check Railway Variables:

```text
BOT_TOKEN
EMAIL_USER
EMAIL_PASS
ADMIN_ID
```

All four are required.

---

### Bot cannot save data

Check Railway volume mount.

The logs should say:

```text
Data directory is writable and configured at: /data
```

If not, attach a Railway volume or check permissions.

---


### Telegram Premium ended / inline button emoji not visible

Set these Railway variables:

```env
USE_PREMIUM_EMOJI=false
USE_PREMIUM_INLINE_BUTTONS=false
```

Then redeploy/restart. This disables all premium conversion and keeps normal Unicode emojis in button labels.

Why this happens: premium inline buttons use `icon_custom_emoji_id`, and the old patch removed the leading Unicode emoji to avoid duplicate icons. If you cannot see premium icons anymore, the button can look like it has no emoji. This package avoids that by defaulting to normal emoji mode.

### Premium emoji caused button crash

Do not replace `types.InlineKeyboardButton` with a function.

Bad crash:

```text
'function' object has no attribute 'de_json'
```

The current package fixes this by keeping `types.InlineKeyboardButton` as a class and only patching its constructor/serializer safely when `USE_PREMIUM_INLINE_BUTTONS=true`.

If Telegram rejects premium button icons, the send wrapper logs a warning and retries without `icon_custom_emoji_id`, so the bot keeps running. If you cannot see emojis in buttons, set both emoji variables to false.

---

### Method 1 expiry update says invalid lines

That bug is already fixed in this package.

Correct Method 1 behavior:

```text
email-only input -> bot asks for date
```

Correct Method 2 behavior:

```text
email + date input -> direct update
```

---

### Bot cannot read email inbox

Check:

```text
EMAIL_USER
EMAIL_PASS
IMAP access / app password
Mailbox security settings
```

If Gmail is used, use an app password instead of normal account password.

---

### Admin channel update not delivered

Check:

1. `ADMIN_CHANNEL_ID` is correct.
2. Bot is added to channel.
3. Bot is admin in channel.
4. Channel ID starts with `-100` if numeric.

---

## Safe Editing Rules

Follow these rules to avoid breaking the bot:

1. Edit message text in `messages.py`.
2. Edit premium emoji IDs in `emoji.py`.
3. Edit storage/data behavior in `storage.py`.
4. Edit expiry/date logic in `expiry.py`.
5. Edit email/code extraction regex in `extractors.py`.
6. Edit IMAP/email scanning in `email_fetcher.py`.
7. Edit admin panel behavior in `admin_handlers.py`.
8. Edit user request behavior in `user_handlers.py`.
9. Do not change `MODULE_ORDER` unless you understand dependencies.
10. Do not monkey-patch TeleBot classes like `InlineKeyboardButton`.

---

## Rollback

If the modular version has any problem, use:

```text
email_bot/single_file_backup.py
```

Fast rollback method:

1. Rename `single_file_backup.py` to your old `email_bot.py`.
2. Run it the old way.
3. Keep the same Railway environment variables and `/data` volume.

---

## Quick Maintenance Map

```text
Need to change emoji?              -> emoji.py
Need to change text/design?         -> messages.py
Need to change buttons?             -> keyboards.py or handler file where button is created
Need to change expiry rules?        -> expiry.py
Need to change admin panel?         -> admin_handlers.py
Need to change user flow?           -> user_handlers.py
Need to change email scanning?      -> email_fetcher.py
Need to change Netflix regex?       -> extractors.py
Need to change save/load?           -> storage.py
Need to change role permissions?    -> permissions.py
Need to change backup files?        -> backup.py
Need to change broadcast?           -> broadcast.py
Need to change startup?             -> main.py
Need to change env/config?          -> config.py
```

---

## Current Included Fixes

This package includes:

```text
✅ Method 1 expiry update fix
✅ Split-part bulk expiry selection
✅ Renew selected emails by sending days like `30`
✅ Premium emoji support for messages/captions
✅ Crash fix for InlineKeyboardButton monkey-patch issue
✅ Clean file names
✅ Shared-runtime modular loader
✅ Single-file rollback backup
```

---

## Recommended Workflow

Before changing anything big:

1. Download Railway data backup.
2. Keep `single_file_backup.py` safe.
3. Make one small change at a time.
4. Redeploy.
5. Check Railway logs.
6. Test `/start`, `/admin`, `/accounts`, expiry update, and one email fetch.

This keeps the bot stable and makes bugs easier to find.

## Bulk Result Sending Fix

This build includes a safer bulk-result sender for large pasted email lists.

What changed:

- Bulk email searches now send result messages in the same order the emails were pasted.
- Telegram flood-limit errors are retried instead of silently skipped.
- Completion text now shows:
  - total processed
  - result messages actually sent
  - successful/found count
  - failed-to-send list, if Telegram still rejects any message after retries
- A small delay is added between result messages to prevent Telegram from dropping or rate-limiting large batches.

This fixes the old behavior where the bot could say something like `65 successful` while only 30–35 result messages were actually delivered.
