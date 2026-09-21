---
description: Log a meal from a description
argument-hint: <what you ate>
---
I ate: $ARGUMENTS

Use the Fitness Me `fm_command` tool. If it matches a dish on my menu (`{"command":"menu"}`), log it with `meal log`. Otherwise look up the ingredients with `foods`, estimate grams (ask me once if the amount is unclear), and log it with `meal quick` (name, kcal, protein, carbs, fat). Confirm what you logged and how much protein and calories I have left today.
