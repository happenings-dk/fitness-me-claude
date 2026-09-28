---
name: fitness-me
description: "Read and log the owner's private health data in Fitness Me (fitnessme.org) through its connector tool `fm_command`: today's plan, A/B workouts, weigh-ins, water (counted in Stanley cups) and every other drink (coffee, energy drinks, soda, milk, shakes, beer, wine… with calories and caffeine), meals and food search, the grocery list, check-ins/mood, sleep, cardio, journal, goals, habits and weekly reviews. Use whenever the user asks about or wants to log anything health/fitness related — e.g. 'what's on today', 'log 104.2 kg', 'I drank a Stanley', 'I had a Monster', 'two coffees this morning', 'a beer with dinner', 'how much caffeine today', 'I ate …', 'add milk to my grocery list', 'what's on my shopping list', 'I bought the eggs', 'start workout A', 'how did my week go', 'hvad skal jeg i dag', 'log min vægt', 'jeg har drukket en halv Stanley', 'jeg drak en kaffe', 'en Cola Zero', 'sæt mælk på indkøbslisten', 'me tomé un café', 'añade huevos a la lista de la compra'."
---

# Fitness Me

Fitness Me is the owner's private health log. The **Fitness Me connector**
exposes one tool, `fm_command`, which runs the `fm` command-line client for
you: `{ "command": "<name>", "args": ["…"] }`. Output is always JSON.

## How to use the tool

1. **Start with `help`** once per conversation if you're unsure of a command:
   `{"command":"help"}` lists everything available to you.
2. **Read before you write.** `{"command":"today"}` gives the plan, targets
   and what's done; use its ids instead of guessing.
3. **Writes are real** and change the log immediately. Only log what the user
   asked for; if a value is ambiguous (which meal, which day, grams), ask once.
   Confirm what you logged in one short line.
4. **Plan items** are referenced as `<eventId>@<occurrenceIso>` from the JSON
   (`rows[].eventId` + `rows[].occurrenceStart`). Row numbers don't carry over
   between calls.
5. If a result says the connection is **read-only**, tell the user to reconnect
   Fitness Me and allow "log and change things".
6. Metric units; local dates (Europe/Copenhagen) as `YYYY-MM-DD`.
7. Answer in the user's language (usually Danish). Enum values such as
   `MEAL_TYPE_LUNCH` are internal names — say "frokost", never the raw value.

## Commands

| Want | Call |
|---|---|
| Today | `{"command":"today"}` |
| Week plan / one day | `{"command":"calendar"}` · `{"command":"calendar","args":["--day","2026-09-22"]}` |
| Mark done / skip | `{"command":"done","args":["<eventId>@<iso>"]}` · `{"command":"skip","args":["<eventId>@<iso>","--reason","sick"]}` |
| Vacation mode / sick days (the plan pauses: no reminders, nothing missed, streaks kept) | `{"command":"vacation","args":["--until","2026-10-12"]}` · `{"command":"sick"}` (until they're better) · `{"command":"sick","args":["--from","yesterday"]}` · back / better: `{"command":"back"}` · `{"command":"timeoff"}` (current and upcoming) · `{"command":"timeoff","args":["delete","<id>"]}` |
| Switch instead of skipping (same time, e.g. cycling instead of incline walk) | `{"command":"switch","args":["<eventId>@<iso>"]}` (ranked, first = recommended) → `{"command":"switch","args":["<eventId>@<iso>","1"]}` · undo: `{"command":"switch","args":["back","<eventId>"]}` |
| Weigh-in / trend | `{"command":"weight","args":["104.2"]}` · `{"command":"weight","args":["--days","90"]}` |
| Check-in | `{"command":"checkin","args":["--valence","2","--energy","4","--stress","2"]}` (valence −5…+5, others 1–5) |
| Water (in the owner's vessel, default a 40 oz Stanley) | `{"command":"water"}` (one) · `{"command":"water","args":["0.5"]}` (half) · `{"command":"water","args":["330ml"]}` · `{"command":"water","args":["2","--vessel","glass"]}` |
| Water today / undo / history | `{"command":"water","args":["today"]}` · `{"command":"water","args":["undo"]}` · `{"command":"water","args":["history","--days","30"]}` |
| Water target / vessel | `{"command":"water","args":["target","2.4l"]}` (`2` = two vessels, `0` clears) · `{"command":"water","args":["vessel","stanley40"]}` |
| Other drinks (a count = servings of the type) | `{"command":"drink","args":["coffee"]}` · `{"command":"drink","args":["monster","1"]}` (500 ml) · `{"command":"drink","args":["beer","50cl"]}` · `{"command":"drink","args":["energy","--sugar-free","--name","Monster Ultra"]}` |
| Drink from the label / again / catalogue | `{"command":"drink","args":["cola","--kcal","139","--sugar","35","--caffeine","32"]}` · `{"command":"drink","args":["recent"]}` → `{"command":"drink","args":["again","2"]}` · `{"command":"drink","args":["types"]}` |
| Grocery list (by aisle, with offers) | `{"command":"grocery"}` |
| Add to the grocery list | `{"command":"grocery","args":["add","mælk","500g hakket oksekød","2 pakker gær"]}` · `{"command":"grocery","args":["add","--recipe","<suggestionId>"]}` (a recipe's missing ingredients) |
| Bought it / undo / remove / clear | `{"command":"grocery","args":["check","mælk"]}` (moves it into the pantry; `--to freezer`) · `{"command":"grocery","args":["uncheck","mælk"]}` · `{"command":"grocery","args":["remove","mælk"]}` · `{"command":"grocery","args":["clear"]}` |
| Steps / sleep | `{"command":"steps","args":["9120"]}` · `{"command":"sleep","args":["--bed","23:10","--wake","06:40","--quality","4"]}` |
| Cardio | `{"command":"cardio","args":["elliptical","25m","--intensity","zone2"]}` |
| Sauna / recovery | `{"command":"recovery","args":["sauna","15m","--temp","85","--rounds","3"]}` |
| Pain & symptoms | `{"command":"pain","args":["neck","4","--type","stiffness","--side","left"]}` |
| Blood pressure | `{"command":"bp","args":["128/82","--pulse","64"]}` |
| 12-minute walk test | `{"command":"walktest","args":["1320m","--hr","118"]}` · `{"command":"walktest","args":["list"]}` (next due every 4 weeks) |
| Desk time & breaks | `{"command":"desk","args":["7.5","--breaks","few"]}` |
| Blood test results | `{"command":"lab","args":["add","ldl","3.1","mmol/l","--ref","0-3"]}` · `{"command":"lab","args":["list"]}` — report numbers and the lab's range only; never interpret them medically |
| Workout | `{"command":"workout","args":["start","A"]}` → `{"command":"workout","args":["status"]}` → `{"command":"workout","args":["done","<setId>","--kg","60","--reps","10"]}` → `{"command":"workout","args":["finish"]}` |
| Food today | `{"command":"food"}` |
| Search foods (per 100 g, DTU database) | `{"command":"foods","args":["jordbær"]}` |
| Log from menu / quick meal | `{"command":"meal","args":["log","Kylling, kartofler"]}` · `{"command":"meal","args":["quick","--name","Skyr med bær","--kcal","320","--protein","32"]}` |
| Plan tomorrow's food | `{"command":"plan-tomorrow","args":["<breakfast>","<lunch>","<dinner>"]}` |
| Journal | `{"command":"journal","args":["new","Tekst …","--tags","gym","--valence","3"]}` · `{"command":"journal"}` |
| Mood · goals · habits | `{"command":"mood"}` · `{"command":"goals"}` · `{"command":"habits","args":["done","walk"]}` |
| Reviews · patterns | `{"command":"review"}` · `{"command":"review","args":["day","2026-09-21"]}` · `{"command":"insights"}` |
| Anything else | `{"command":"rpc","args":["ls","nutrition"]}` → `{"command":"rpc","args":["NutritionService/GetDay","{}"]}` |

Photos (meal photo, profile picture) need the app or the local CLI.

## Good patterns

- "Hvad skal jeg i dag?" → `today`; summarise the next planned item, what's
  done, calories/protein left and steps — short and friendly.
- "Jeg er syg" / "I'm on holiday until Sunday" → `sick` / `vacation --until <date>`
  instead of skipping every planned item; when they're better or home, `back`.
  Never ask what the illness is; after sick days suggest easing back in.
- "Jeg vejer 104,2" → `weight 104.2`; mention the 7-day trend and whether the
  weekly rate is on track.
- "Jeg spiste skyr med jordbær" → `foods skyr` + `foods jordbær` for per-100 g
  values, estimate grams (ask if unsure), then `meal quick` with the totals.
- "Jeg har drukket en halv Stanley" → `water 0.5`; reply with the day in
  vessels and litres ("1,5 af 2 Stanleys · 1,8 l") from `day` in the result.
  Note: bare `water` *logs* a vessel — to just look, use `water today` (it
  lists every drink of the day with caffeine and drink kcal).
- "Jeg drak en kaffe" / "I had a Monster" / "en Cola Zero" / "to øl" →
  `drink kaffe` · `drink monster` · `drink cola-zero` · `drink øl 2`. Types by
  name in Danish, English or Spanish (energy/monster/redbull, soda/cola/sodavand,
  coffee/kaffe/café/latte, tea/te, juice, milk/mælk, shake/protein, sports,
  sparkling/danskvand, beer/øl, wine/vin, spirits, other). A bare count is
  servings of that drink (energy 500 ml, soda 330 ml, coffee 250 ml, beer 330 ml,
  wine 150 ml); give a volume when one is said ("en halv liter" → `0.5l`).
  Zero/Ultra/Max/light → `--sugar-free` (energy, soda and sports drinks only).
  Numbers read off the can go in as whole-drink totals (`--kcal`, `--sugar`,
  `--caffeine`, `--protein`) with `--name`. "Samme igen" → `drink again`.
  Reply with what was logged, then from `day`: water progress, caffeine against
  the limit ("300 / 400 mg" — mention it gently when over) and kcal from drinks.
  Alcohol counts for calories but never toward the water target.
- "Hvordan gik ugen?" → `review` (and `mood` if asked about mood).

## Safety

Give practical, conservative guidance — you're not a doctor. Never encourage
pushing through pain or neurological symptoms (e.g. neck stiffness with head,
arm or vision symptoms); suggest stopping and seeing a doctor if symptoms
worsen. The owner's personal plan and targets come from the data itself
(`today`, `settings get`, `goals`) — read them rather than assuming.
