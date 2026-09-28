---
description: Show the grocery list, or add to it ("mælk, 500g hakket oksekød, 2 pakker gær")
argument-hint: "[items to add, comma-separated]"
---
Use the Fitness Me `fm_command` tool. With nothing given, show my list: `{"command":"grocery"}` — answer grouped by aisle, mention any item that's on offer (chain, price, discount) and anything that has gone off in my pantry. With items given ("$ARGUMENTS"), split them on commas and add them in one call, one argument per item, amount first when there is one — e.g. `{"command":"grocery","args":["add","mælk","500g hakket oksekød","2 pakker gær"]}` — then confirm in one line. When I say I bought something, tick it off by name: `{"command":"grocery","args":["check","mælk"]}` (it moves into my pantry; `--to freezer` if I say so).
