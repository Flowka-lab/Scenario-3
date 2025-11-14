
# Getting Started with the AI Scheduling Assistant Demo

Welcome! This guide will walk you through setting up and running the **AI Scheduling Assistant (Scenario‑3)** demo from scratch. We assume you have basic familiarity with Python and command-line operations, but we’ll keep things beginner-friendly. By the end, you’ll have the system up and processing a sample request.

---

## ⚡ TL;DR (Quick Setup)

1. **Install Prerequisites**  
   Make sure you have **Python 3.9+**, **PostgreSQL**, and optionally **Docker** (for n8n) installed.

2. **Clone the Repo & Install Python Packages**  
   ```bash
   git clone <repo-url>
   cd Scenario-3
   pip install -r requirements.txt
   ```

3. **Configure Environment**  
   Copy `.env.sample` to `.env` and fill in your Postgres DB name, user, password, etc.

4. **Setup the Database**  
   Start PostgreSQL, create a database, and run the SQL script provided in `data/demo_data.sql` to create tables and seed example data.

5. **Start the Backend and UI**  
   - FastAPI:
     ```bash
     uvicorn main:app --host 0.0.0.0 --port 8000 --reload
     ```
   - Streamlit:
     ```bash
     streamlit run app.py
     ```
   Ensure they can connect to your DB (check `.env` settings).

6. **Run n8n Workflow**  
   Use n8n (cloud or local) to import `FlowKa-Lab_Scenario-3.json`.  
   Set up the **OpenAI API key** and **Gmail credentials** in n8n.  
   Execute the workflow with a test email (either by sending an actual email or triggering it manually with sample data).

7. **Verify the Outcome**  
   You should see a new entry in the Streamlit dashboard (or DB) for the request, and if email was configured, an auto-reply in the recipient’s inbox. The system will have parsed the email and planned a response.

> If any of these steps sound unfamiliar, don’t worry – detailed instructions follow!

---

## ✅ Prerequisites

### 1. Python 3 and Virtual Environment

Ensure you have **Python 3.9 or higher** installed on your machine. You can check with:

```bash
python --version
```

It’s recommended to use a **virtual environment** to avoid dependency conflicts:

```bash
python -m venv venv      # create a virtual environment
source venv/bin/activate # activate it (Linux/Mac)
venv\Scripts\activate    # on Windows
```

All subsequent steps assume the `venv` is activated (you’ll see `(venv)` in your shell prompt).

---

### 2. PostgreSQL

The project uses **PostgreSQL** as the database. Install Postgres if you don’t have it:

- **Windows**: use the Postgres installer from the official site.  
- **Mac**: Homebrew users can run:  
  ```bash
  brew install postgresql
  ```
- **Linux (Debian/Ubuntu)**:  
  ```bash
  sudo apt-get install postgresql
  ```

After installation, start the Postgres service (if not already running). For example, on Ubuntu:

```bash
sudo service postgresql start
```

---

### 3. n8n (Workflow Automation Tool)

n8n is used to run the workflow. You have options:

- **Easiest – n8n Cloud**  
  Sign up for n8n Cloud and run the workflow online. You’ll upload the workflow JSON and set credentials there.

- **Local via Docker**  
  ```bash
  docker run -it -p 5678:5678 n8nio/n8n
  ```
  This starts n8n on port `5678`.

- **Local via npm**  
  ```bash
  npm install -g n8n
  n8n start
  ```

We’ll assume either **n8n Cloud** or **Docker** for local use in this guide.  
If you use n8n Cloud, ensure your FastAPI instance is accessible from the internet (or adjust the workflow to use a local tunnel).

---

### 4. OpenAI API Key & Gmail Account

#### OpenAI API Key

- Sign up at OpenAI and get an **API key** with access to **GPT‑4** (or GPT‑3.5 if you plan to modify the workflow).  
- This key will be entered into n8n’s credentials for the **OpenAI** node.

#### Gmail Account Credentials

For the email sending, it’s easiest to use a **Gmail account**.

In n8n:
- When you add a **Gmail node credential**, it will guide you through OAuth consent.
- Alternatively, you can use an **SMTP** node with an app password if your Gmail allows it.

> If this is too complex for now, you can **skip actual email sending** by disabling the Gmail node in the workflow and simply inspect the draft output. That way, you don’t risk sending emails during testing.

For help, see n8n’s docs or search **“n8n Gmail credential setup”** – it’s a one-time setup.

---

## 🔧 Installation & Setup

### Step 1: Download the Project Code

Clone the repository from GitHub:

```bash
git clone https://github.com/FlowKa-Lab/Scenario-3.git  # use the actual repo URL
cd Scenario-3
```

This creates a directory `Scenario-3` containing `main.py`, `app.py`, etc.

---

### Step 2: Install Python Dependencies

Inside the project directory (with your `venv` active), run:

```bash
pip install -r requirements.txt
```

This installs **Streamlit**, **pandas**, **plotly**, **psycopg2**, **python-dotenv**, and other required packages.

If `fastapi` and `uvicorn` are not listed in `requirements.txt`, also run:

```bash
pip install fastapi uvicorn[standard]
```

---

### Step 3: Configure Environment Variables

There is a file named `.env.sample` in the repo. Copy it to `.env`:

```bash
cp .env.sample .env
```

Open `.env` in a text editor. You should see placeholders like:

```env
DB_HOST=localhost
DB_NAME=your_db_name
DB_USER=your_db_user
DB_PASSWORD=your_db_password
```

Edit these values to match your Postgres setup:

- `DB_HOST` is usually `localhost` if running locally.
- `DB_NAME` is the database name (e.g., `flowkalab`).
- `DB_USER` and `DB_PASSWORD` are your Postgres user credentials.

Save the file. The apps will use this to connect to Postgres.

---

### Step 4: Setup the Database Schema and Data

Create the database and import the schema/data.

#### 4.1 Create Database

Using `psql` or pgAdmin, run something like:

```sql
CREATE DATABASE flowkalab;
CREATE USER flowka_user WITH PASSWORD 'somepassword';
GRANT ALL PRIVILEGES ON DATABASE flowkalab TO flowka_user;
```

(Replace names/passwords as you like, but ensure they match `.env`.)  
If using the default `postgres` user, you can skip creating a new user and just use that in `.env`.

#### 4.2 Import Schema and Data

In the `Scenario-3/data/` folder, there should be an SQL file (e.g., `demo_data.sql`). Run:

```bash
psql -U flowka_user -d flowkalab -f data/demo_data.sql
```

Or if using the `postgres` user:

```bash
psql -U postgres -d flowkalab -f data/demo_data.sql
```

This will create tables like `materials`, `products`, `dc_requests`, `dc_request_scenarios`, etc., and seed sample data.

#### 4.3 Verify DB Setup (Optional)

Connect to the DB and run:

```sql
SELECT sku_id, description FROM products;
SELECT * FROM dc_requests LIMIT 5;
```

You should see product entries and sample DC requests.

---

### Step 5: Run the FastAPI Backend

Start the backend service:

```bash
uvicorn main:app --host 0.0.0.0 --port 8000 --reload
```

- `--reload` is useful during development.  
- `--host 0.0.0.0` makes it accessible externally (e.g., on a cloud VM).

Open a browser at:

- http://localhost:8000/docs

You should see the **FastAPI docs** with the `simulate_request` endpoint.

If you encounter errors:

- **Import errors** → ensure FastAPI/Uvicorn are installed in the venv.  
- **DB errors** → check `.env`, Postgres status, and credentials.  
- Make sure `load_dotenv()` is called in the code before using `os.getenv(...)`.

---

### Step 6: Run the Streamlit UI

In a new terminal (with the `venv` activated again), run:

```bash
streamlit run app.py --server.port 8501 --server.address 0.0.0.0
```

If you’re local, you can simply do:

```bash
streamlit run app.py
```

Then open:

- http://localhost:8501

You should see the dashboard, likely showing:

- A table of existing DC requests and statuses  
- Possibly charts summarizing requests or scenarios  

If you see errors about DB connection, double-check `.env` and that Streamlit is reading it from the correct working directory.

---

### Step 7: Setting up the n8n Workflow

If you’re using **n8n Cloud**, log in.  
If you’re using **Docker/local**, ensure n8n is running:

```bash
docker run -it --rm -p 5678:5678 -v ~/.n8n:/home/node/.n8n n8nio/n8n
```

Then go to:

- http://localhost:5678

#### 7.1 Import the Workflow

In n8n:

- Go to **Import**  
- Select `FlowKa-Lab_Scenario-3.json` from the repo  

#### 7.2 Configure Credentials

Set up credentials for:

- **OpenAI** – use your API key (GPT‑4 or GPT‑3.5).  
- **Postgres** – use host, DB name, user, and password matching `.env`.  
- **Gmail** – via OAuth or use an SMTP node with an app password.

> If Gmail setup is too complex, you can disable the Gmail trigger and send nodes for your first tests.

#### 7.3 Simplify for Manual Testing (Optional)

For a first test, you can:

- Replace the Gmail trigger with a **Manual Trigger**.  
- Hard-code sample subject/body in a Function node or directly in the OpenAI prompt inputs.  

Then **execute the workflow** manually in n8n:
- GPT will parse the “email”  
- Data will be inserted into Postgres  
- FastAPI will be called via HTTP  
- (Optionally) a reply email is drafted/sent

Check each node for green status and inspect outputs as needed.

---

### Step 8: End-to-End Verification

Now verify through the UI/DB:

- Check the **Streamlit dashboard** for a new request.  
- Or query the DB directly, for example:

```sql
SELECT * FROM dc_requests ORDER BY created_at DESC LIMIT 5;
SELECT * FROM dc_request_scenarios ORDER BY created_at DESC LIMIT 5;
```

If email sending was enabled, verify that a reply reached the inbox.

If nothing appears, double-check:

- The Postgres node in n8n actually ran and committed.  
- FastAPI logs (for exceptions or data issues).  
- Any filters in the Streamlit UI that might hide your test data.

If everything looks good: 🎉 **You have the full system running!**

---

## 🧩 Troubleshooting & Tips

Common issues and fixes:

### n8n Credential Issues

- **OpenAI errors** → confirm API key and model access. Try `gpt-3.5-turbo` if GPT‑4 is restricted.  
- **Gmail errors** → check OAuth setup, or use SMTP + app password.  
- You can always **disable email nodes** and just inspect generated JSON/drafts.

### Database Connection Errors

Errors like:

- `could not connect to server`  
- `password authentication failed`  

Check that:

- Postgres is running.  
- `.env` values are correct.  
- Host/port match your environment (especially if using Docker).  

### FastAPI Endpoint Not Responding

If n8n times out:

- Ensure FastAPI is running at the URL/port configured in the HTTP node.  
- If n8n Cloud is used and FastAPI is local, you’ll need a public URL (e.g., via tunneling or deploying FastAPI to a cloud VM).  

### GPT Formatting Issues

If GPT doesn’t return valid JSON for the parsing step:

- Check the prompt in n8n – it should strictly request **“ONLY valid JSON”**.  
- If needed, add a small JS Function node to clean the output (strip extra text, etc.).  

### Streamlit Not Updating

- Hit **refresh** in the browser.  
- Confirm that the SQL queries in `app.py` aren’t filtering out your test entries.  

If you get stuck, you can also test components independently (e.g., call FastAPI directly with a test JSON in a REST client).

---

## 🚀 Next Steps & Experiments

Once the base demo works, try:

- Writing your **own sample email** and see how parsing behaves.  
- Modifying **inventory/capacity** in the DB to force partial fulfillment scenarios.  
- Tweaking the **reply drafting logic** in `main.py` to change email tone or add details.  
- Replacing Gmail with a **webhook trigger** or Slack/Teams integration.  

---

## 📚 Additional Resources

- **Design Document** – See the PDF in the `docs/` folder (if provided) for architecture & data model details.  
- **n8n Docs** – For extending or modifying the workflow.  
- **FastAPI Docs** – For adding new endpoints or logic.  
- **OpenAI API Reference** – For tuning prompts or changing models.

---

Feel free to reach out via the project’s issue tracker for help, feedback, or to share what you’ve built on top of this demo.  
**Good luck, and happy automating! 🤖📦**
