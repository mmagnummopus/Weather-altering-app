# Weather-altering-app — Desktop Weather Client & Wallpaper Changer

A PyQt5 desktop application that simplifies how weather information is
perceived: it reads live conditions from OpenWeatherMap, shows them in a
single window, and repaints the Windows desktop wallpaper to match what the
sky is actually doing outside.

**Scope:** current conditions and a 15-hour forecast for any city, **28**
weather themes and **99** wallpapers indexed in a local SQLite database.

---

## What's here

| | |
|---|---|
| **Live weather** | Temperature, min/max, wind speed, humidity, sunrise and sunset for any city |
| **Short-range forecast** | The next five three-hour steps — 15 hours ahead — with conditions per step |
| **Adaptive wallpaper** | Desktop background chosen from season, time of day and current conditions |
| **Theme database** | SQLite catalogue of 28 themes mapped to 99 wallpaper files |
| **City input** | Free-text field accepting Russian and English city names, defaulting to Moscow |
| **Interface** | Single 800×500 Qt window — three buttons, no menus, no configuration |

## Repository layout

```
├── main.py            PyQt5 interface, theme selection, wallpaper switching
├── pogoda.py          OpenWeatherMap client — current conditions and forecast
├── data.sqlite3       theme catalogue: themes (28 rows), oboi (99 rows)
├── requirements.txt   PyQt5, PyQt5-Qt5, requests
└── photo_img/         wallpaper files and favicon.ico, referenced by data.sqlite3
```

## Running it

```bash
pip install -r requirements.txt
python main.py
```

Type a city into the input field — or leave it empty for Moscow — and use
the three buttons:

| | |
|---|---|
| **Погода на данный момент** | Fetch and display current conditions |
| **Прогноз на ближайшие 15 часов** | Fetch the next five forecast steps |
| **Поменять обои** | Pick a matching wallpaper and apply it |

Weather has to be fetched before the wallpaper button has anything to work
with — the theme is derived from the conditions currently on screen.

## How the wallpaper is chosen

Theme selection runs on four inputs, in this order:

**Season**, from the current month — winter (December-February), summer
(June-August), or off-season (the remaining six months).

**Time of day**, from the current hour — the evening threshold is 17:00 in
winter and summer, 19:00 off-season.

**Condition**, from the OpenWeatherMap `weather.main` field — `Clear`,
`Clouds`, `Snow` or `Rain`, with temperatures at or below 5.3 °C falling
through to the fog themes.

**Humidity**, used only in summer and off-season, splits `Clouds` into
overcast at 50% and above, partly cloudy below it.

The resulting theme name is looked up in `themes`, one of its wallpapers is
drawn at random from `oboi`, and the file is applied through the Windows
`SystemParametersInfo` API.

---

## Requirements and platform

| | |
|---|---|
| **Python** | 3.6 or newer |
| **Dependencies** | PyQt5 5.15.7, PyQt5-Qt5 5.15.2, requests 2.28.1 |
| **Platform** | Weather display is cross-platform; wallpaper switching is Windows-only |
| **Network** | Requires access to `api.openweathermap.org` |

The wallpaper call goes through `ctypes.windll`, which exists only on
Windows. On Linux and macOS the two weather buttons work normally and the
third one will not.

The `photo_img/` directory is not tracked in this repository. The database
stores filenames only, so the folder has to be present alongside `main.py`
for wallpaper switching to resolve a path.

## Interface language

The application interface, the forecast descriptions returned by the API and
the theme names in the database are all in Russian. City names are accepted
in both Russian and English.

---

## Contact

Written by **Timofey Vasilyev**. Questions and additional explanations —
tima.yastreb07@gmail.com
