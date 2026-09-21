---
name: fitness-me
description: "Read and log the owner's private health data in Fitness Me (fitnessme.org) through its connector tool `fm_command`: today's plan, A/B workouts, weigh-ins, meals and food search, check-ins/mood, sleep, cardio, journal, goals, habits and weekly reviews. Use whenever the user asks about or wants to log anything health/fitness related — e.g. 'what's on today', 'log 104.2 kg', 'I ate …', 'start workout A', 'how did my week go', 'hvad skal jeg i dag', 'log min vægt'."
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
| Weigh-in / trend | `{"command":"weight","args":["104.2"]}` · `{"command":"weight","args":["--days","90"]}` |
| Check-in | `{"command":"checkin","args":["--valence","2","--energy","4","--stress","2"]}` (valence −5…+5, others 1–5) |
| Steps / sleep | `{"command":"steps","args":["9120"]}` · `{"command":"sleep","args":["--bed","23:10","--wake","06:40","--quality","4"]}` |
| Cardio | `{"command":"cardio","args":["elliptical","25m","--intensity","zone2"]}` |
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
- "Jeg vejer 104,2" → `weight 104.2`; mention the 7-day trend and whether the
  weekly rate is on track.
- "Jeg spiste skyr med jordbær" → `foods skyr` + `foods jordbær` for per-100 g
  values, estimate grams (ask if unsure), then `meal quick` with the totals.
- "Hvordan gik ugen?" → `review` (and `mood` if asked about mood).

## Safety

Give practical, conservative guidance — you're not a doctor. Never encourage
pushing through pain or neurological symptoms (e.g. neck stiffness with head,
arm or vision symptoms); suggest stopping and seeing a doctor if symptoms
worsen. The owner's personal plan and targets come from the data itself
(`today`, `settings get`, `goals`) — read them rather than assuming.
