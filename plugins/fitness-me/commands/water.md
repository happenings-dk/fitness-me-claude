---
description: Log water in your vessel (default one Stanley) and show today's total
argument-hint: "[count | 330ml | 0.5l]"
---
Log water with the Fitness Me `fm_command` tool: `{"command":"water","args":["$ARGUMENTS"]}` — or `{"command":"water"}` for one of my vessel if nothing was given. A number is a count of my vessel (0.5 = half); a volume like 330ml or 0.5l is logged as is; "glass" or "bottle" means `["1","--vessel","glass"]`. Then tell me in one line how far I am today in vessels and litres against my target (e.g. "1,5 af 2 Stanleys · 1,8 l"). For any other drink (coffee, a Monster, a cola, a beer) use `/fitness-me:drink` — or `{"command":"drink","args":["coffee"]}`.
