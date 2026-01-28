# AgenticAI-ConversationMailer 🤖📩

A **stateful, multi-agent AI assistant** built with **LangGraph** and **Streamlit**. This application features persistent conversation memory via **SQLite**, a tool-aware agentic architecture, and an automated email delivery system that sends structured, bulleted chat summaries to users when a session ends.

---

## 🚀 Key Features

* **Multi-Agent Orchestration**
  Separates concerns using a primary LLM agent (conversation & reasoning) and a dedicated `tool_agent` (summarization & email delivery).

* **Persistent Conversation Memory**
  Uses `SqliteSaver` to checkpoint and restore conversations by **Employee ID**, allowing users to resume chats across sessions.

* **State-Driven Routing**
  An explicit router node inspects LLM outputs and determines whether to continue the dialogue or invoke tools.

* **Automated Email Summaries**
  On session completion, a `send_email_summary` tool sends a professional, bulleted summary via SMTP (TLS/SSL).

* **Manual Session Termination**
  A **Stop & Email** button in the UI injects a hidden `SIGNAL_END`, forcing immediate summarization and email dispatch.

* **Structured Reporting**
  Final emails clearly summarize key points from both the **User** and the **AI**, optimized for quick review.

---

## 🧠 Architecture Overview

```text
User ──▶ LLM Agent ──▶ Router ──▶ (continue chat)
                    └──▶ Tool Agent ──▶ Email Summary ──▶ END
```

* **LLM Agent**: Maintains conversational context and intent detection.
* **Router**: Decides the next step based on tool calls or termination signals.
* **Tool Agent**: Executes trusted operations (email sending, summarization) using verified user data.

---

## 🗂️ Project Structure

```text
.
├── app.py              # Streamlit app + LangGraph workflow
├── requirements.txt    # Python dependencies
├── .env                # Environment variables (API keys & SMTP creds)
└── chat_history.db     # Auto-generated SQLite checkpoint database
```

---

## ▶️ Setup & Installation

### 1️⃣ Environment Setup

```bash
python -m venv .venv

# Windows
.venv\Scripts\activate

# macOS / Linux
source .venv/bin/activate
```

### 2️⃣ Install Dependencies

```bash
pip install -r requirements.txt
```

### 3️⃣ Configure Environment Variables

Create a `.env` file in the project root:

```bash
# Google Gemini API key
GOOGLE_API_KEY=your_gemini_api_key_here

# Gmail SMTP configuration (use an App Password)
SENDER_EMAIL=your_email@gmail.com
SENDER_PASSWORD=your_16_digit_app_password
```

> ⚠️ **Security Note**: Never commit your `.env` file to version control. Add it to `.gitignore`.

### 4️⃣ Run the Application

```bash
streamlit run app.py
```

---

## 🔧 Technical Details

### Workflow Execution

1. **START**
   Captures initial user input along with Employee ID and Email.

2. **LLM Node**
   Handles dialogue, reasoning, and intent detection while preserving message history.

3. **Router Node**
   Determines whether to continue the conversation or invoke the tool agent.

4. **Tool Agent**
   Injects verified user metadata (Employee ID & Email) into tool calls to avoid hallucinations and sends the final summary.

5. **END**
   Finalizes the workflow and resets the Streamlit session state.

---

### State Persistence

* Backed by **SQLite** using `SqliteSaver(check_same_thread=False)` to support Streamlit’s multi-threaded execution model.
* Each **Employee ID** maps to a unique conversation thread stored in the `checkpoints` table.
* On login, the agent reconstructs the exact prior state and message history.

---
