Initial Prompting Techniques & Before/After Examples

1) Initial prompting techniques demonstrated
restated the problem in my own words
“i need get_weather_data(location, forecast_days=5) that returns {location, current_temp, forecast[...]} and clamps days 1..5.”
identified input/output requirements with targeted prompts
“the forecast list items must have date, max_temp, min_temp, rain_chance. return {} on failure so ui doesn’t crash.”
requested pseudocode before implementation
“give me short pseudocode flow first (clean input → geocode → forecast → clamp days → build dict → handle errors).”
challenged edge cases in ai code
“what happens if precipitation_probability_max is missing? if user types ‘chennai tomorrow’ as location?”
requested modular design improvements
“split parsing into parse_weather_question(), fetching into get_weather_data(), and response into generate_weather_response().”
asked for code explanations to ensure understanding
“explain each block in simple terms so i can present it.”
requested iterative improvements to initial solutions
“make the code shorter and beginner-friendly; reduce branching; keep the same outputs.”
queried design trade-offs
“open-meteo vs openweather—what’s the trade-off for this assignment?”
(≥5 techniques satisfied: yes.)

3) before/after example #1 — fixing generate_weather_response input type bug
context: i hit AttributeError: 'str' object has no attribute 'get' because i passed a string instead of the parsed dict.
initial ai-generated usage (problem)
# wrong: passing a string as parsed_question
print(generate_weather_response("Will it rain tomorrow in Perth?", get_weather_data))
my follow-up prompt
“that error means a string went in where a dict was expected. show the correct call pattern using parse_weather_question() so generate_weather_response() receives the parsed dict.”
improved usage (result)
info = parse_weather_question("Will it rain tomorrow in Perth?")
data = get_weather_data(info.get("location"))
print(generate_weather_response(info, data))
why this prompting worked
i explicitly restated the input/output contract and asked for the minimal correct call sequence. that forced alignment between function interfaces and removed the type mismatch.
3) before/after example #2 — visualise menu indentation & routing
context: my visualisation branch had mis-indented input and called the wrong functions.
initial snippet (problem)
elif c == "3":
p = input("City to visualise: ").strip()  # misindented
res = get_weather_data(p)
print("1) Temperature 2) Rain 3) Both")
v = input("> ").strip().lower()
if v == "1": create_temperature_visualisation(res)
elif v == "2": matplotlib_lollipop_rain(res)  # mismatched helper name
elif v == "3":
    create_temperature_visualisation(res)
    matplotlib_lollipop_rain(res)
my follow-up prompt
“fix indentation so input and options sit inside the ‘visualise’ branch, and call the correct helpers. keep it defensive if res has an error.”
improved snippet (result)
elif c == "3":
    p = input("City to visualise: ").strip()
    res = get_weather_data(p)
    if not res or "error" in res:
        print(f"⚠️ {res.get('error','No data')}")
        continue
    print("1) Temperature  2) Rain  3) Both")
    v = input("> ").strip().lower()
    if v in ("1","temp","temperature"):
        create_temperature_visualisation(res)
    elif v in ("2","rain","lollipop"):
        create_precipitation_visualisation(res)
    elif v in ("3","both","all"):
        create_temperature_visualisation(res)
        create_precipitation_visualisation(res)
    else:
        print("Choose 1, 2, or 3.")
why this prompting worked
i asked for a focused correction: proper block structure plus function name alignment. the ai produced a neatly scoped fix without changing unrelated code.
4) before/after example #3 — hardening get_weather_data (edge cases + clarity)
context: needed clean input handling, clamped forecast days, safe rain handling, and {} on network failure.
initial ai-generated function (simpler, but brittle)
def get_weather_data(location, forecast_days=5):
    # minimal draft
    g = requests.get("https://geocoding-api.open-meteo.com/v1/search",
                     params={"name": location, "count": 1})
    lat, lon = g.json()["results"][0]["latitude"], g.json()["results"][0]["longitude"]

    fc = requests.get("https://api.open-meteo.com/v1/forecast",
                      params={
                          "latitude": lat, "longitude": lon, "current_weather": True,
                          "daily": "temperature_2m_max,temperature_2m_min,precipitation_probability_max",
                          "timezone": "auto",
                      })
    d = fc.json()
    days = min(5, int(forecast_days))
    out = {"location": location, "current_temp": d["current_weather"]["temperature"], "forecast": []}
    for i in range(days):
        out["forecast"].append({
            "date": d["daily"]["time"][i],
            "max_temp": d["daily"]["temperature_2m_max"][i],
            "min_temp": d["daily"]["temperature_2m_min"][i],
            "rain_chance": d["daily"]["precipitation_probability_max"][i],
        })
    return out
my follow-up prompt
“make it beginner-friendly but robust:
– strip time words from location (‘tomorrow’ etc.)
– clamp forecast_days to 1..5
– return {} on network/format errors
– safely handle missing rain array (use None)
– keep exact return shape”
improved function (result)
import requests, re

def get_weather_data(location, forecast_days=5):
    # clamp days
    days = max(1, min(5, int(forecast_days or 5)))

    # clean location: remove time words & extra spaces
    loc = re.sub(r"\b(tomorrow|today|day after|in\s+\d+\s+days)\b", "", str(location or ""), flags=re.I)
    loc = re.sub(r"\s+", " ", loc).strip()
    if not loc:
        return {}

    try:
        # geocode
        g = requests.get("https://geocoding-api.open-meteo.com/v1/search",
                         params={"name": loc, "count": 1, "language": "en"}, timeout=10)
        g.raise_for_status()
        res = (g.json().get("results") or [])
        if not res:
            return {}

        lat, lon = res[0]["latitude"], res[0]["longitude"]
        city = res[0].get("name") or loc

        # forecast
        fc = requests.get("https://api.open-meteo.com/v1/forecast",
                          params={
                              "latitude": lat, "longitude": lon, "current_weather": True,
                              "daily": "temperature_2m_max,temperature_2m_min,precipitation_probability_max",
                              "timezone": "auto",
                          }, timeout=10)
        fc.raise_for_status()
        data = fc.json()

        daily = data.get("daily") or {}
        times = daily.get("time") or []
        if not times:
            return {}

        out = {
            "location": city,
            "current_temp": (data.get("current_weather") or {}).get("temperature"),
            "forecast": []
        }
        for i in range(min(days, len(times))):
            out["forecast"].append({
                "date": times[i],
                "max_temp": (daily.get("temperature_2m_max") or [None]*len(times))[i],
                "min_temp": (daily.get("temperature_2m_min") or [None]*len(times))[i],
                "rain_chance": (daily.get("precipitation_probability_max") or [None]*len(times))[i],
            })
        return out

    except requests.RequestException:
        return {}
why this prompting worked
i specified exact behaviors and edge cases while keeping the return shape fixed. the ai refactored to handle network errors, missing fields, and input cleaning without growing the function too much—matching the assignment constraints.
5) before/after example #4 — clearer, shorter nlp response generation
context: i wanted temperature/rain/general replies in one short function, with formatted dates and safe numeric handling.
initial response (too generic)
def generate_weather_response(q, get_fn):
    info = parse_weather_question(q)
    data = get_fn(info["location"])
    r = data["forecast"][info["day"]]
    return f"{data['location']} {r['date']}: rain {r['rain_chance']}%"
my follow-up prompt
“keep it simple but smarter:
– format date as DD Mon YYYY
– intent-aware replies (rain-only vs temperature vs general)
– show rain only if ≥30%
– guard for missing data
– write helpers to coerce numbers safely.”
improved response (result)
from datetime import datetime

def format_date(d):
    try: return datetime.strptime(d, "%Y-%m-%d").strftime("%d %b %Y")
    except: return d

def _as_float(x):
    try: return float(x)
    except (TypeError, ValueError): return None

def generate_weather_response(user_question, fetch_fn):
    info = parse_weather_question(user_question)
    place, day, attr = info["location"], info["day"], info["attribute"]
    if not place:
        return "Please tell me a city or PIN/ZIP."
    data = fetch_fn(place)
    if not data or "error" in data:
        return data.get("error", "Sorry, I couldn't get the weather right now.")
    fc = data.get("forecast") or []
    if not fc: return "No forecast available right now."

    i = min(day, len(fc)-1)
    row = fc[i]
    city = data.get("location", place)
    date = format_date(row.get("date",""))
    mx, mn = row.get("max_temp"), row.get("min_temp")
    rc = _as_float(row.get("rain_chance"))
    t = mx if mx is not None else mn

    def temp_phrase(x):
        if x is None: return "temperature unavailable"
        return "hot and sunny" if x >= 35 else ("cool and pleasant" if x >= 15 else "cold")

    def rain_only(v):
        if v is None: return "No rain info."
        return "Yes, likely — rain %d%%." % int(v) if v >= 50 else \
               "Possible — rain %d%%." % int(v) if v >= 20 else \
               "Unlikely — rain %d%%." % int(v)

    if attr == "rain":
        return f"In {city} on {date}: {rain_only(rc)}"

    msg = f"In {city} on {date}: {temp_phrase(t)}"
    if t is not None:
        msg += f" (around {round(t)}°C)"
    if rc is not None and rc >= 30:
        msg += f", rain {int(rc)}%."
    else:
        msg += "."
    return msg
why this prompting worked
i asked for specific intent handling, simple thresholds, and safe formatting. the ai produced a concise response generator that reads well and avoids fragile assumptions.
6) design trade-offs i discussed
open-meteo vs openweather: i chose open-meteo (no api key, simple json) to keep setup minimal.
charts vs gui: stuck to notebook charts + simple html panels in colab (no streamlit/tkinter overhead for grading).
keyword nlp vs llm: chosen for transparency and speed—easy to explain and test.
7) quick takeaway
by stating the problem clearly, asking for pseudocode first, pushing on edge cases, and iterating toward shorter code, i kept the solution simple, robust, and easy to present.
Note: These were just my inital prompts to start with the assignmnet later i adjusted and changed my codes
