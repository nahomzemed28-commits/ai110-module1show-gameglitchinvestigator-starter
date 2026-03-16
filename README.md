# 🎮 Game Glitch Investigator: The Impossible Guesser

## 🚨 The Situation

You asked an AI to build a simple "Number Guessing Game" using Streamlit.
It wrote the code, ran away, and now the game is unplayable. 

- You can't win.
- The hints lie to you.
- The secret number seems to have commitment issues.

## 🛠️ Setup

1. Install dependencies: `pip install -r requirements.txt`
2. Run the broken app: `python -m streamlit run app.py`

## 🕵️‍♂️ Your Mission

1. **Play the game.** Open the "Developer Debug Info" tab in the app to see the secret number. Try to win.
2. **Find the State Bug.** Why does the secret number change every time you click "Submit"? Ask ChatGPT: *"How do I keep a variable from resetting in Streamlit when I click a button?"*
3. **Fix the Logic.** The hints ("Higher/Lower") are wrong. Fix them.
4. **Refactor & Test.** - Move the logic into `logic_utils.py`.
   - Run `pytest` in your terminal.
   - Keep fixing until all tests pass!

## 📝 Document Your Experience

**Game Purpose:**
Glitchy Guesser is a number guessing game built with Streamlit. The player selects a difficulty (Easy: 1–20, Normal: 1–100, Hard: 1–50), then tries to guess a randomly chosen secret number within a limited number of attempts. Each guess returns a hint and the player's score updates based on how quickly they find the answer.

**Bugs Found:**

1. **State Bug — secret number kept changing:** The text input widget used a dynamic key (`key=f"guess_input_{difficulty}"`). Every time Streamlit re-ran the script, this key changed, which caused the widget to be treated as new and triggered a fresh `random.randint` call, replacing the secret number on every click.

2. **Logic Bug — secret passed as a string:** On every even-numbered attempt, the code intentionally converted the secret to a string (`secret = str(st.session_state.secret)`) before passing it to `check_guess`. This caused incorrect comparisons between an `int` guess and a `str` secret, producing wrong hints.

3. **Logic Bug — hints were backwards:** Inside `check_guess`, when `guess > secret` the message said "📈 Go HIGHER!" and when `guess < secret` it said "📉 Go LOWER!" — the opposite of correct.

**Fixes Applied:**

1. Changed the text input key to a static string `key="guess_input"` so the widget identity never changes across reruns.
2. Removed the even/odd string conversion — the secret is now always passed as an integer.
3. Corrected the hint messages: "Too High" → "📉 Go LOWER!", "Too Low" → "📈 Go HIGHER!".
4. Refactored all game logic (`get_range_for_difficulty`, `parse_guess`, `check_guess`, `update_score`) out of `app.py` and into `logic_utils.py`. All 3 pytest tests now pass.

## 📸 Demo
<img width="867" height="681" alt="Screenshot 2026-03-16 at 3 42 33 PM" src="https://github.com/user-attachments/assets/3b46e22b-b385-4c88-9f5c-7f7c065e7bb2" />

- [ ] 

## 🚀 Stretch Features

- [ ] [If you choose to complete Challenge 4, insert a screenshot of your Enhanced Game UI here]
