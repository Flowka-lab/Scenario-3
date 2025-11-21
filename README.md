
# AI Scheduling Assistant (Demo – Scenario 3)

## AI-Powered Scheduling Assistant for Supply Chain Requests  
*An experimental demo by **Salami Hamza**, integrating GPT-4 with automation to schedule product deliveries based on email requests.*

---

##  Project Overview

This project showcases an **AI Scheduling Assistant** that automatically processes incoming email requests and schedules shipments or production to fulfill them.

The scenario is set in a **supply chain context** where Distribution Centers (DCs) email the manufacturing plant asking for additional product (e.g., *“We need 8,000 units of X by next week”*).

The AI assistant:
1. Reads the email  
2. Extracts key details (product, quantity, needed date)  
3. Simulates a production/shipment plan  
4. Sends back a reply email confirming what can be done  

This is a **proof-of-concept**, designed for demo & educational purposes.  
It uses **GPT-4**, **n8n**, **FastAPI**, **Streamlit**, and **PostgreSQL**.

---

##  Demo Use Case

### **Use Case:**  
Automate handling of urgent product requests from DCs to a manufacturing plant.

### **How It Works:**  
When a DC email arrives, the system automatically:

- Parses the email  
  *(e.g., “DC North asks for 12k units of Citrus Shampoo by 2025-11-02”)*
- Logs the request  
- Checks inventory & production capacity  
- Simulates whether the request is feasible  
- Drafts a response email  
  *e.g., “We can supply the full 12k on time”*  
  or  
  *“We can provide 8k by then; remainder later.”*

This demo highlights how **AI + workflow automation** can drastically reduce response time and manual effort in supply chain communication.

While the scenario is specific, the pattern applies to:
- Meeting scheduling  
- Ticket triaging  
- Operational request automation  
- And more  

---

##  Tech Stack

- **Python Backend:** FastAPI (REST API), PostgreSQL  
- **Streamlit Frontend:** Dashboard to monitor requests & responses  
- **n8n Workflow:** Orchestrates the entire flow (email → GPT-4 → API → reply)  
- **OpenAI GPT-4:** Extracts structured data from free-form emails  
- **Email Integration:** Gmail API via n8n  
- **Deployment:** AWS EC2 (Ubuntu VM)  
- **Other Libraries:**  
  - `pandas`, `plotly` – data & graphs  
  - `psycopg2` – DB connector  
  - `python-dotenv` – configuration  

---

##  Quickstart

### 1. **Clone the repo**
```
git clone <repo-url>
```

### 2. **Set up Python environment**
```
pip install -r requirements.txt
```

### 3. **Configure PostgreSQL**
- Install Postgres locally or via Docker  
- Run SQL schema in `data/` directory  
- Fill out `.env` with DB credentials  

### 4. **Run backend and UI**
```
uvicorn main:app --reload --port 8000
streamlit run app.py
```

### 5. **Import workflow into n8n**
- Open n8n (cloud or local Docker)
- Import `FlowKa-Lab_Scenario-3.json`
- Configure OpenAI + Gmail credentials
- Update API URLs if deployed on EC2

### 6. **Run a test**
- Send a sample email  
- Or manually inject test data in n8n  
- Watch the workflow parse → simulate → reply  

### 7. **Monitor results**
- Check Streamlit dashboard  
- Inspect DB tables  
- Validate that reply email is sent  

For full setup, see `get_started.md`.

---

##  Repository Structure

```
main.py                     # FastAPI backend (simulation logic)
app.py                      # Streamlit UI dashboard
requirements.txt            # Python dependencies
FlowKa-Lab_Scenario-3.json  # n8n workflow export
data/                       # SQL schema + seed data
docs/                       # Documentation & diagrams (planned)
n8n/                        # Workflow (n8n automation)
.env.sample                 # Environment variable template
LICENSE                     # MIT License
```

---

##  How to Contribute or Test

### **Contributions welcome!**

You can help by:

### ✔ Testing the demo  
Follow the Quickstart. If you find issues, open a GitHub issue with details.

### ✔ Improving documentation  
If instructions are unclear, feel free to refine them.

### ✔ Extending functionality  
Try new request types, integrate different models, or add simulation logic.

### ✔ Reporting issues  
Use the issue tracker for bugs, parsing failures, or feature suggestions.

Please follow standard best practices:
- Clear commit messages  
- No sensitive data in code  
- Keep everything MIT-licensed  

---

##  License & Credits

This project is released under the **MIT License**.

**Credits:**  
Developed by **Salami Hamza** as part of **FlowKa Lab**.  
Thanks to the communities behind **n8n**, **Streamlit**, **FastAPI**, and others.  
If you use this project, please attribute the original author.

