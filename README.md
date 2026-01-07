# 🤖 AI Game Referee — Rock-Paper-Scissors-Plus

![Python](https://img.shields.io/badge/Python-3.9%2B-blue)
![Google ADK](https://img.shields.io/badge/Framework-Google%20ADK-orange)
![Status](https://img.shields.io/badge/Status-Completed-green)

A deterministic, state-aware **AI Game Referee** built using **Google ADK** and **Gemini models**.
This project demonstrates how to design a **product-grade conversational agent** that enforces game rules reliably while avoiding LLM hallucinations through a **hybrid architecture** (LLM interface + deterministic game engine).

---

## 📋 Assignment Overview

**Goal:**
Build a minimal AI chatbot that acts as a referee for **Rock-Paper-Scissors-Plus**, enforcing all rules and managing a *best-of-three* game.

**Key Challenge:**
Maintain strict correctness of game state and outcomes while still supporting a natural conversational interface.

---

## 🏗️ Architecture & Design

This solution follows a **“Blind Referee” design pattern**, where the language model is never trusted with game logic or state mutation.

### 1. State Modeling — Single Source of Truth

To ensure state never lives *only* inside the prompt:

* **State Object:** `GameState` (Python dataclass)
* **Tracks:**

  * `round_count`
  * `user_score`, `bot_score`
  * bomb usage flags (enforces *one bomb per player*)
  * match history for round-by-round feedback
* **Persistence:** State lives entirely in Python memory
* **Serialization:** Converted to JSON only when passed to ADK tools or agent responses

This guarantees that the LLM always operates on **explicit, authoritative state**.

---

### 2. Agent–Tool Separation (Google ADK)

The system is intentionally split into clear responsibilities:

* **Agent (LLM layer):**

  * Interprets user intent (e.g., “I choose rock”)
  * Explains outcomes and prompts the next move
  * **Explicitly forbidden** from performing game logic or calculations

* **Tools (Deterministic Logic):**

  * `validate_move`
  * `resolve_round`
  * `update_game_state`

* **Execution Flow:**

```
User Input
   ↓
ADK Agent (intent parsing)
   ↓
Tool Execution (validation + scoring)
   ↓
Updated GameState (JSON)
   ↓
Agent Response (natural language)
```

This design satisfies the requirement that **state mutation must happen through tools**, not prompts.

---

## 🛠️ Key Features & Design Decisions

### 🟢 Strict “Wasted Round” Enforcement

* Invalid input (e.g., `"lizard"`, second bomb attempt) **consumes the round**
* No retry loops or soft corrections
* Score remains unchanged, but round counter increments

This ensures full compliance with the assignment rules.

---

### 🟢 Deterministic, Explainable Outcomes

* All round resolutions are handled by pure Python logic
* The LLM only *reports* results returned by tools
* Prevents hallucinated scores or inconsistent outcomes

---

### 🟢 Simple but Intentional Bot Strategy

Instead of pure randomness:

* If it is **Round 3**
* The bot is **losing**
* The bot has **not used its bomb**

➡️ The bot will deterministically choose **Bomb**

This satisfies the requirement to “respond intelligently” without introducing unnecessary complexity.

---

## 🚀 How to Run

### Prerequisites

* Python 3.9+
* Google Gemini API Key

---

### Installation

1. **Clone the repository**

```bash
git clone https://github.com/sayandip30882636/AI-Game-Referee-Rock-Paper-Scissors-Plus.git
cd AI-Game-Referee-Rock-Paper-Scissors-Plus
```

2. **Install dependencies**

```bash
pip install google-generativeai
```

3. **Configure API Key**

Open `Rock_Paper_Scissors_Referee_ADK.ipynb` and add:

```python
GEMINI_API_KEY = "YOUR_API_KEY_HERE"
```

4. **Run the game**

Open the notebook in **VS Code, Colab, or Jupyter**, and run all cells sequentially.

---

## 🔮 Future Improvements

* **Persistence:** Add lightweight session storage (e.g., SQLite) for resumable games
* **UI:** Replace CLI interaction with a Streamlit-based interface

---

## 👤 Author

**Sayandip Ghosh**

**Tech Stack:**
Python · Google ADK · Gemini Models · Deterministic Tooling

---
