WeatherWise — Simple CLI Weather App:
An accurate,beginner-friendly weather app that answers natural-language questions (“Will it rain tomorrow in Perth?”), prints a 5-day forecast with emojis, and can visualize temperature and rain.

🌤 Overview:
WeatherWise fetches data from Open-Meteo (no API key needed) and presents it in a clean user interface. 
You can View a 5-day forecast for a city or PIN/ZIP.
Ask questions like “Will it rain tomorrow in 6000?” or “How’s the weather today in Melbourne?”
Visualise temperature ranges and rain chances.
This project was built for my university assessment and refined with AI assistance to keep the code short, readable, and easy to explain.

✨ Features:
Natural language Q&A (rain-only, temperature-only, or general feel)
5-day forecast with date formatting (DD Mon YYYY)
Emojis for temperature: 🔥 hot (≥35 °C), 🙂 cool (15–34 °C), 🥶 cold (<15 °C)
Rain note shown only when meaningful (≥30%)
Charts: Temperature band + Rain “lollipop” chart (Matplotlib)
Friendly errors for no internet / bad location

🧠 How It Works (short):
Geocode your input (city or PIN/ZIP) to lat/lon using Open-Meteo’s geocoding API.
Fetch forecast (current temp, daily min/max, rain chance) from Open-Meteo’s forecast API.
Parse questions to detect intent (rain / temperature / general) and day (today/tomorrow/in N days).
Respond with a concise sentence and optional visualisations.
No API keys. All endpoints are free and public.

🔧 Quick Start:
Option A — Google Colab
Open the notebook and paste the provided cells (Data Fetching → NLP → UI helpers → Menu).
Run all cells top to bottom.
Use the console prompts to interact.

Option B — VS Code (recommended) but i have used Google Colab

🖥 Usage:
When you start the app you’ll see a simple menu:
1) View Forecast  2) Ask a Question  3) Visualise  4) Exit
1) View Forecast → enter a city or PIN/ZIP
Prints 5 days with formatted dates, min–max temps, emoji, and “(rain XX%)” only if ≥30%.
2) Ask a Question → enter your question, then city or PIN/ZIP
Example questions:
“Will it rain today in Perth?”
“Is rain likely in 6000 tomorrow?”
“Is it cold tomorrow in Melbourne?”
“Temperature in Adelaide in 3 days?”
“How is the weather today in Perth?”
“Weather in Auckland in 4 days?”
3) Visualise → choose Temperature, Rain, or Both
Temperature: shaded min–max band with lines
Rain: lollipop (stem + dot + % label)

📂 Project Structure:
weatherwise.py
README.md
requirements.txt   # optional
Key functions:
get_weather_data(place) — fetches & returns {location, current_temp, forecast[]}
parse_weather_question(text) — extracts {location, day, attribute}
generate_weather_response(text, get_weather_data) — rain-only or general reply
viz_temp(res) / viz_rain(res) / viz_both(res) — charts
run_app() — console menu

🪄 Design Choices (reflective):
Open-Meteo: no API key, fast JSON, simple endpoints → perfect for an assignment and demos.
Emojis & thresholds: tiny helpers (fmt_date, emoji_for_temp, RAIN_TH=30) keep code readable while improving UX.
Intent-based replies: splitting rain vs temperature vs general makes answers feel smarter without heavy NLP.
Minimal UI: single run_app() loop is easier to maintain and grade.

🧪 Quick Sanity Test:
# Minimal checks (paste in a Python shell with the app loaded)
res = get_weather_data("6000")
print(res.get("location"), res.get("current_temp"))
print(len(res.get("forecast", [])))
print(generate_weather_response("Will it rain tomorrow in Perth?", get_weather_data))
You should see a valid location name, a temperature, a forecast length 1–5, and a short sentence answer.

🛠 Troubleshooting:
“Module not found: requests/matplotlib”
Activate your virtual environment and pip install requests matplotlib.
No charts appear
On some systems, Matplotlib opens a window outside the terminal. If needed, replace plt.show() with plt.savefig("chart.png").
“No internet connection” / “Could not find 'X'”
Check your network or try a simpler city name or a known PIN/ZIP.

⚠️ Limitations:
Forecast is limited to 5 days and summarized to keep the UI simple.
PIN/ZIP geocoding is best-effort via Open-Meteo; some codes may not resolve perfectly.
NLP is keyword-based, not a full language model.

🗺 Roadmap (if I continue):
Unit tests for the parser and edge cases
More use of language model
Export charts to PNG automatically for reports
Maybe trying it GUI based

🙏 Acknowledgements:
Open-Meteo for free weather & geocoding APIs
Matplotlib for simple plotting
ChatGPT for iterative code refinements, explanation help, and bug-fix guidance
Google Colab for early prototyping

👋 Final Notes:
This app was intentionally kept small and clear so a new reader can run it in minutes and understand the flow. If you have questions or suggestions, feel free to reach out.Good luck and have fun.









