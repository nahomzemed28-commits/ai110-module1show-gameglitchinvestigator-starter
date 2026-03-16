# 💭 Reflection: Game Glitch Investigator

Answer each question in 3 to 5 sentences. Be specific and honest about what actually happened while you worked. This is about your process, not trying to sound perfect.

## 1. What was broken when you started?

When I first ran the game, the secret number kept changing every time I clicked "Submit Guess," making it impossible to win consistently. I also noticed the hints were backwards — if I guessed too high, the game told me to go higher, and if I guessed too low, it told me to go lower. There were two root causes: the text input used a dynamic key (`guess_input_{difficulty}`) that caused Streamlit to reset state on each render, and the `check_guess` call was intentionally passing the secret as a string every other attempt, which caused incorrect comparisons. Additionally, the hint messages in the original `check_guess` function were simply swapped.

---

## 2. How did you use AI as a teammate?

I used Claude (Claude Code) as my primary AI tool throughout this project. Claude correctly identified that the dynamic `key=f"guess_input_{difficulty}"` on the text input was causing the secret to regenerate, and suggested changing it to the static key `"guess_input"` — I verified this fix by running the game and confirming the secret stayed constant across guesses. Claude also correctly identified the backwards hint messages and the string-conversion bug on line 158–161 of the original app.py. One thing to watch: AI confidently explained the bugs but I still had to trace through the code myself to fully understand *why* the string comparison in the `TypeError` branch of `check_guess` could silently return wrong results for multi-digit numbers.

---

## 3. Debugging and testing your fixes

I decided a bug was fixed when both the manual game behavior matched expectations and the pytest suite passed. I ran `pytest tests/test_game_logic.py` after implementing `logic_utils.py` and all three tests passed immediately, confirming that `check_guess` correctly returns `"Win"`, `"Too High"`, and `"Too Low"` as plain strings. The tests also revealed that the original `check_guess` in `app.py` returned tuples, not strings — the tests expected strings, so refactoring to `logic_utils` required changing the return type. Claude helped me understand that the test file imports from `logic_utils`, not `app.py`, which meant the refactor was necessary for the tests to even run.

---

## 4. What did you learn about Streamlit and state?

The secret number kept changing because Streamlit re-runs the entire script from top to bottom on every user interaction (button click, input change, etc.). When the text input key was `f"guess_input_{difficulty}"`, changing difficulty produced a new widget key, which Streamlit treated as a brand-new widget, resetting related state and triggering the `random.randint` call again. `st.session_state` is Streamlit's way of preserving values across these reruns — values stored in it survive the script restart. The fix was to use a static key `"guess_input"` so the widget identity never changes, keeping the secret stable.

---

## 5. Looking ahead: your developer habits

One habit I want to carry forward is reading the test file *before* writing any code — the tests in `test_game_logic.py` told me exactly what the fixed function should return, which made the implementation straightforward. Next time I work with AI on a coding task, I would give it the test file upfront so it can verify its suggestions against expected behavior rather than guessing. This project changed how I think about AI-generated code: it can produce plausible-looking logic that is subtly wrong in ways that only appear at runtime, so treating AI output as a first draft that needs human review and testing is essential.
