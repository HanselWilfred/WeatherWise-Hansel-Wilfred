# 🌧️ WeatherWise — Simple CLI Weather App 🚀

> **An accurate, beginner-friendly weather app for natural-language weather answers, 5-day emoji forecasts, and fun visualizations!**

---

## 🌟 Why WeatherWise?
- **Ask anything!** — _“Will it rain tomorrow in Perth?”_  
- **See the week at a glance!** — 5-day forecasts with ✨emojis✨  
- **Visualize weather trends!** — Gorgeous temperature & rain charts  
- **No API keys needed!** — Powered by [Open-Meteo](https://open-meteo.com/)  
- **Made with ❤️ for beginners, students, and weather fans!**

---

## 🖼️ Features at a Glance

- 💬 **Natural Language Q&A** — Rain, temperature, “feel”, or general queries
- 🗓️ **5-Day Forecast** — Dates in `DD Mon YYYY` format
- 😎 **Weather Emojis**:  
  - 🔥 Hot (≥35°C)  
  - 🙂 Cool (15–34°C)  
  - 🥶 Cold (<15°C)
- 🌧️ **Rain alerts** — Only shown if meaningful (≥30%)
- 📊 **Charts:**  
  - Temperature line chart  
  - Rain “lollipop” chart (with Matplotlib)
- 🆗 **Friendly errors** — No internet? Bad location? Get helpful advice!

---

## 🧠 How It Works

1. **Geocode your input**  
   City or PIN/ZIP → Latitude/Longitude (Open-Meteo geocoding API)
2. **Fetch the forecast**  
   - Current temp  
   - Daily min/max  
   - Rain chance  
   (Open-Meteo forecast API)
3. **Parse your question**  
   - Detect intent (rain/temperature/general)  
   - Day (today/tomorrow/in N days)
4. **Respond smartly!**  
   Concise answers + optional visualizations
5. **No keys needed!**  
   All endpoints are free & public

---

## ⚡ Quick Start

###  Google Colab

- Open the notebook, paste the provided cells (Data Fetching → NLP → UI helpers → Menu).
- Run all cells, top to bottom.
- Use the console prompts to interact!
---

## 🖥️ Usage

When you start the app, you’ll see a simple menu:

```
1) View Forecast   2) Ask a Question   3) Visualize   4) Exit
```

### 1️⃣ **View Forecast**  
Enter a city or PIN/ZIP  
→ Prints 5 days with formatted dates, min–max temps, emoji, and “(rain XX%)” if ≥30%.

### 2️⃣ **Ask a Question**  
Enter your question, then city or PIN/ZIP  
#### Example Questions:
- “Will it rain today in Perth?”
- “Is rain likely in 6000 tomorrow?”
- “Is it cold tomorrow in Melbourne?”
- “Temperature in Adelaide in 3 days?”
- “How is the weather today in Perth?”
- “Weather in Auckland in 4 days?”

### 3️⃣ **Visualize**
Choose:
- Temperature:min max line chart
- Rain: lollipop chart (stem + dot + % label)
- Both!

---

## 📦 Project Structure

```
weatherwise.py
README.md
requirements.txt   # optional
ai-conversations/  # AI conversation logs directory
```
**Key Functions:**  
- `get_weather_data(place)` — fetches `{location, current_temp, forecast[]}`
- `parse_weather_question(text)` — extracts `{location, day, attribute}`
- `generate_weather_response(text, get_weather_data)` — rain-only or general reply
- `viz_temp(res)` / `viz_rain(res)` / `viz_both(res)` — charts
- `run_app()` — console menu

---

## 🧾 AI Conversation Logs

All my AI prompting conversations and interactions are stored in the `ai-conversations/` directory.  
If you're curious about how the AI helped shape WeatherWise, you can browse these logs for important transcripts of the development process, prompts, replies, and ideas.

---

## ✨ Design Choices

- **Open-Meteo:** No API key, fast JSON, simple endpoints — perfect for assignments & demos!
- **Emojis & thresholds:** Tiny helpers (`fmt_date`, `emoji_for_temp`, `RAIN_TH=30`) = readable code + better UX.
- **Intent-based replies:** Smarter answers, simple NLP!
- **Minimal UI:** One `run_app()` loop — easy to maintain & grade.

---

## 🧪 Test It Yourself!

```python
# example: natural language -> data -> answer
text = "Will it rain in Perth tomorrow?"
info = parse_weather_question(text)
data = get_weather_data(info.get("location"), forecast_days=5)
print(generate_weather_response(info, data))
```
You should see:
- ✅ Valid location name  
- 🌡️ Temperature  
- 📆 Forecast length (1–5)  
- 💬 Short sentence answer

---

## 🛠️ Troubleshooting

- **Module not found:** `requests`/`matplotlib`  
  _Activate your environment &_ `pip install requests matplotlib`
- **No charts appear:**  
  _Matplotlib may open a window outside terminal; use_ `plt.savefig("chart.png")` _instead of_ `plt.show()`
- **No internet / bad location:**  
  _Check your network or use a simpler city / known PIN/ZIP._

---

## ⚠️ Limitations

- 5-day summary for simplicity
- PIN/ZIP geocoding is best effort 
- Keyword-based NLP (not a full language model)
- It is not GUI based
- Needs Internet to work on the code

---

## 🗺️ Roadmap (if I continue)

- ✅ Unit tests for the parser & edge cases
- 🧠 More use of language models
- 🖼️ Export charts to PNG for reports
- 🖥️ Maybe a GUI version!
- Use VS Code 

---

## 🙏 Thanks & Credits

- **Open-Meteo** — Free weather & geocoding APIs
- **Matplotlib** — Simple plotting
- **ChatGPT** — Iterative code refinements, explanations, bug-fixes
- **Google Colab** — Early prototyping

---

## 👋 Final Notes

_Kept **concise and clear** so a new user can run it in minutes and understand the flow!_  
**Questions or suggestions? Reach out! Good luck & have fun!** 🎉🌦️

---
