
# 🤖 AI Game Referee: Rock-Paper-Scissors-Plus

![Python](https://img.shields.io/badge/Python-3.9%2B-blue)
![Gemini API](https://img.shields.io/badge/AI-Google%20Gemini%20Flash-orange)
![Status](https://img.shields.io/badge/Status-Completed-green)

A deterministic, state-aware Game Referee built with the **Google Generative AI SDK**.
This project demonstrates how to build a reliable "Product-Grade" agent that strictly enforces game rules without hallucinating scores, using a **Hybrid Architecture** (LLM Interface + Deterministic Logic Engine).

---

## 📋 Assignment Overview
**Goal:** Build a minimal AI Game Referee chatbot that enforces rules for *Rock-Paper-Scissors-Plus* (Best of 3, with a special "Bomb" move).
**Core Challenge:** Prevent the LLM from hallucinating the game state while maintaining a conversational interface.

---

## 🏗️ Architecture & Design

My solution follows the **"Blind Referee" Pattern**. I strictly separated the *Intent Understanding* (LLM) from the *Game Logic* (Python).

### 1. State Modeling (The Source of Truth)
To ensure the game state never lives "only in the prompt," I implemented a rigid Pydantic-style data model:
* **Storage:** `GameState` class (persists in memory, outside the context window).
* **Tracking:** Explicitly tracks `round_count`, `user_score`, `bot_score`, and boolean flags for `bomb_used` (enforcing the 1-bomb-per-game rule).
* **Serialization:** The state is converted to JSON only when communicating with the Agent, ensuring the LLM always sees the undeniable truth.

### 2. Agent-Tool Interface
The Gemini Agent acts as a **UI Layer**, not a Judge.
* **The Agent:** Configured with a system prompt that forbids calculation. Its only job is to parse user intent (e.g., "I throw a rock") and call the tool.
* **The Tool (`resolve_round`):** A deterministic Python function that executes the "Laws of Physics" for the game (Move comparison, Scoring, Validations).
* **Flow:** `User Input` → `Agent Intent` → `Tool Execution` → `JSON Result` → `Agent Response`.

---

## 🛠️ Key Features & Tradeoffs

### 🟢 Strict "Wasted Round" Handling
[cite_start]The requirements stated: *"Invalid input wastes the round"*[cite: 25].
* **Design Decision:** Instead of a soft "Please try again" loop, I implemented strict compliance.
* **Implementation:** If a user inputs "Lizard" or tries to use a Bomb twice, the engine captures this as `Move.INVALID`, increments the round counter, but awards **zero points**.

### 🟢 Rate Limit Failover (Reliability)
* **The Problem:** The Google Gemini Free Tier has a limit of ~15 Requests Per Minute (RPM).
* **The Solution:** I implemented a **Hybrid Failover Mechanism**.
    1. **Throttling:** A `time.sleep(2)` delay is added to the loop.
    2. **Fallback Mode:** If the API returns a `429 Quota Error`, the game **does not crash**. It automatically switches to a "Offline Referee" mode, printing the raw result from the logic engine to keep the user's session alive.

### 🟢 "Intelligent" Bot Strategy
Instead of pure randomness, the bot uses a heuristic:
* *Logic:* If it is the Final Round (3), the Bot is losing, and it still has a Bomb → **It will use the Bomb.**
* [cite_start]This satisfies the requirement to "respond intelligently" rather than randomly[cite: 5].

---

## 🚀 How to Run

### Prerequisites
* Python 3.9+
* A Google Cloud API Key (for Gemini)

### Installation
1. **Clone the repository:**
   ```bash
   git clone [https://github.com/yourusername/rps-ai-referee.git](https://github.com/yourusername/rps-ai-referee.git)
   cd rps-ai-referee

```

2. **Install dependencies:**
```bash
pip install google-generativeai

```


3. **Configure API Key:**
Open `Rock_Paper_Scissors_Referee_ADK.ipynb` and paste your key at the bottom:
```python
MY_API_KEY = "YOUR_GOOGLE_API_KEY_HERE"

```


4. **Run the Game:**
```bash
python main.py

```



---

## 🔮 Future Improvements

* **Persistence:** Implement SQLite storage to allow game sessions to survive server restarts.
* **GUI:** Upgrade from CLI to a **Streamlit** web interface for better visualization of the round history.
* **Dockerization:** Containerize the application for easier deployment.

---

**Author:** Sayandip
**Tech Stack:** Python, Google GenAI SDK (ADK)

```

```
