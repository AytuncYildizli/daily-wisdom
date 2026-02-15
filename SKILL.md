---
name: daily-wisdom
description: |
  Daily wisdom, anecdotes & historical stories delivered via cron.
  Use when: "daily anecdote", "günün hikayesi", "wisdom cron", "daily story", "tarihten bir sayfa", "günlük bilgelik", setting up recurring cultural/historical content delivery.
  Don't use when: one-off trivia (just answer), news digests (use summarize), tweet drafts (use personal-tweet-drafts).
  Outputs: A rich daily message with original-language quote, story, and modern connection. Writes to history file to prevent repeats.
metadata:
  emoji: 📜
  category: content
  tags: [wisdom, history, culture, cron, daily, anecdote, stoic, turkish, mythology]
---

# Daily Wisdom

Deliver a daily historical anecdote, philosophical insight, or cultural story as a recurring cron job. Designed for depth, variety, and zero repeats.

## What It Does

Each day, the agent:
1. Checks the **history file** to avoid repeating topics
2. Picks from a **weighted source pool** spanning civilizations
3. Writes a rich message: original-language quote → translation → story (5-8 sentences) → modern connection
4. Delivers via the configured channel (WhatsApp, Telegram, Slack, etc.)
5. Logs the topic to history

## Source Pool

All traditions are drawn from equally — no fixed percentages. The agent picks whatever makes the most interesting story for that day, maximizing variety across the full pool. The only rule: don't repeat a tradition back-to-back.

### Turkic & Central Asian
- **Dede Korkut** — Kan Turalı, Basat & Tepegöz, Deli Dumrul, Bamsı Beyrek, Salur Kazan
- **Orhon Yazıtları** — Bilge Kağan, Kül Tigin, Tonyukuk
- **Göktürk & Hun** — Mete Han, Bumin Kağan, İstemi Yabgu, Attila
- **Manas Destanı** — Kırgız epic, largest oral tradition in the world
- **Nasreddin Hoca** — Timeless wit and paradox

### Islamic Golden Age & Sufi
- **İbn Sina, Al-Khwarizmi, Ibn Khaldun, Al-Biruni** — Science & philosophy
- **Mevlana, Yunus Emre, Hacı Bektaş Veli, Ahmed Yesevi** — Sufi poetry & wisdom
- **Ibn Battuta** — The greatest traveler
- **Selçuklu & Osmanlı** — Alparslan, Fatih, Mimar Sinan, Piri Reis, Evliya Çelebi

### Classical Mediterranean
- **Stoicism** — Seneca, Marcus Aurelius, Epictetus
- **Greek** — Heraclitus, Diogenes, Thales, Aristotle, Socrates
- **Roman** — Cicero, Cato, Plutarch

### Far East
- **Sun Tzu** — Art of War
- **Miyamoto Musashi** — Book of Five Rings
- **Confucius, Laozi, Zhuangzi** — Eastern philosophy
- **Zen koans** — Paradox and insight
- **Chanakya (Kautilya)** — Indian statecraft

### Ancient & Pre-Classical
- **Gilgamesh** — The oldest story
- **Egyptian** — Ptahhotep, Book of the Dead, Imhotep
- **Norse** — Hávamál, Odin's wisdom, Ragnarök
- **Sumerian proverbs**
- **Zoroastrian** — Avesta, good thoughts/words/deeds

### African & Indigenous
- **Sundiata Keita** — Mali Empire founder
- **Mansa Musa** — Richest human in history
- **Anansi stories** — West African trickster wisdom
- **Ubuntu philosophy** — "I am because we are"
- **Timbuktu scholars** — Sankore University

### Renaissance & Early Modern
- **Machiavelli, Leonardo, Montaigne**
- **Copernicus, Galileo** — Paradigm shifts
- **Ada Lovelace, Nikola Tesla** — Visionaries ahead of their time

## Prompt Templates

### Standard Daily (recommended)
```
You are a cultural historian and storyteller. Deliver today's wisdom.

RULES:
1. Pick any source from the pool. Maximize variety — don't repeat the same tradition back-to-back. Favor sources that haven't appeared recently in the history.
2. DO NOT repeat anything from the history file below.
3. Format:

📜 **[Title — Person/Source, Era]**

> *"[Original language quote]"*
> — [Attribution]

🌍 [English translation if quote is in another language]

**The Story:** [5-8 sentence story. Vivid, specific details. Not Wikipedia summary — make it alive. Include a surprising fact or lesser-known angle.]

💡 **Modern Connection:** [2-3 sentences connecting to today — startups, tech, leadership, daily life. Make it feel relevant, not forced.]

---
_daily wisdom • [source tradition]_

HISTORY (do not repeat these):
{history_file_contents}
```

### Region-Focused Variant
Same as above but lock to a specific tradition for the day:
```
Today MUST be from [REGION] sources only.
Examples:
- Turkic/Islamic: Dede Korkut, Orhon, Nasreddin Hoca, Manas, Sufi poets, Islamic golden age
- African: Sundiata, Mansa Musa, Anansi, Ubuntu, Timbuktu
- Far East: Sun Tzu, Musashi, Confucius, Laozi, Zen koans
- Norse: Hávamál, Odin, Ragnarök, Viking sagas
```

### Deep Dive Variant (weekend edition)
```
Today is a DEEP DIVE. Pick one topic and go long:
- 10-15 sentences instead of 5-8
- Include 2-3 quotes instead of 1
- Add historical context and aftermath
- Connect to at least 2 modern parallels
```

## Setup

### 1. Create the history file
```bash
touch memory/anecdote-history.md
```

Or with initial content:
```markdown
# Daily Wisdom History
<!-- One entry per line: YYYY-MM-DD | Source | Topic -->
2026-02-15 | Seneca | De Brevitate Vitae - time is the only non-renewable resource
2026-02-16 | Dede Korkut | Kan Turalı & Selcen Hatun - warrior couple vs 3 beasts
```

### 2. Create the cron job
```
Use the cron tool to create a daily job:

Schedule: cron expression for your preferred time (e.g., "30 7 * * *" for 07:30)
Timezone: Your timezone (e.g., "Europe/Istanbul")
Session target: isolated
Payload kind: agentTurn
Delivery: announce (to your preferred channel)

Message: Use the Standard Daily prompt template above, 
with the history file path substituted in.
```

### 3. Example cron configuration
```json
{
  "name": "daily-wisdom",
  "schedule": {
    "kind": "cron",
    "expr": "30 7 * * *",
    "tz": "Europe/Istanbul"
  },
  "sessionTarget": "isolated",
  "payload": {
    "kind": "agentTurn",
    "message": "[Standard Daily prompt with history]"
  },
  "delivery": {
    "mode": "announce"
  },
  "enabled": true
}
```

## History File Format

The history file prevents repeats. Each line = one delivered anecdote:

```markdown
# Daily Wisdom History
2026-02-10 | Marcus Aurelius | Meditations Book 5 - obstacle is the way
2026-02-11 | Dede Korkut | Deli Dumrul - challenging Azrael, learning love > death
2026-02-12 | Sun Tzu | Empty fort strategy - Zhuge Liang bluff
2026-02-13 | Bilge Kağan | Orhon inscription - "Türk milleti yok olacaktı"
2026-02-14 | Nasreddin Hoca | Soup of the soup - diminishing returns
2026-02-15 | Gilgamesh | Utnapishtim - accepting mortality
```

After delivery, append today's entry. The agent reads this file before generating to ensure no repeats across months.

## Customization

### Bias toward a tradition
By default all traditions are equal. To favor a specific region, add an instruction:
```
PREFERENCE: Favor [Turkic/Stoic/Far East/African/etc.] sources 
when possible, but still mix in other traditions regularly.
```

### Add new sources
Just add to the prompt's source list. The agent will incorporate them.

### Change language
The default output mixes Turkish and English. For full English:
```
Write entirely in English. Translate all quotes to English.
```
For full Turkish:
```
Tamamını Türkçe yaz. Alıntıları hem orijinal dilde hem Türkçe ver.
```

### Multiple daily sends
Create separate crons: morning wisdom (07:30) + evening reflection (21:00) with different prompt variants.

## Example Outputs

See the `examples/` directory for 17 sample outputs across formats:

**Standard format** (quote → story → modern connection):
- `turkic-kan-turali.md` — Warrior couple vs 3 beasts (Amazon meets Hercules)
- `turkic-basat-tepegoz.md` — Turkish Polyphemus (Odyssey parallel)
- `turkic-deli-dumrul.md` — Deli Dumrul vs Azrael (Death vs Love)
- `turkic-orhon.md` — Bilge Kağan's stone inscription (platform independence)
- `turkic-nasreddin.md` — "Ya tutarsa?" (world's shortest startup manifesto)
- `mythology-gilgamesh.md` — Oldest story, first pivot
- `classical-seneca.md` — Seneca on time waste
- `classical-marcus-aurelius.md` — Obstacle is the way (the original)
- `fareast-musashi.md` — Sword from an oar
- `islamic-ibn-sina.md` — First biofeedback experiment
- `sufi-mevlana.md` — The wound is where light enters
- `african-sundiata.md` — Crippled boy who built an empire + earliest human rights charter
- `indian-chanakya.md` — Kingmaker's 2000-year-lost playbook
- `norse-havamal.md` — Odin's price for wisdom

**Alternative formats:**
- `format-thread.md` — Twitter/X thread style (Mansa Musa)
- `format-thisday.md` — "This Day in History" (Galileo)
- `zen-gateless-gate.md` — Minimal koan, no modern connection

## Tips for Quality

1. **Specificity kills generic**: "In 1040, at Dandanakan..." beats "The Seljuks once fought..."
2. **Original language quotes are magic**: Even if the reader doesn't speak the language, seeing `"Türk bodun yok bolmazun"` in Göktürk hits different
3. **The modern connection must be surprising**: Don't just say "this is still relevant." Show HOW. "Selcen Hatun was pair-programming before pair-programming existed."
4. **Vary the tone**: Some days profound, some days funny (Nasreddin Hoca), some days dark (Gilgamesh)
5. **Weekend = deep dive**: Use the deep dive variant for Saturdays
