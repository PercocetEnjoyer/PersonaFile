# persona.json

**A structured personal profile format for AI agents.**

Your agents don't really know you. They get a system prompt, maybe a few lines of context, and then they guess. `persona.json` fixes that.

It's a simple, structured format that captures who someone is — their car, their diet, their career, their communication style, how many miles they drive a week — so any agent can read it and actually be useful.

> *"My 2022 Camry has 34k miles and I drive ~150 mi/week"*
> → Agent knows to remind you about an oil change in 3 weeks.

> *"I'm lactose intolerant and love Thai food"*
> → Agent suggests restaurants and recipes that actually work for you.

> *"I prefer direct communication and TL;DRs"*
> → Agent stops writing you novels.

## The Problem

Right now, personalizing an AI agent means:
- Writing a freeform `.md` file and hoping you covered everything
- Pasting random context into system prompts
- Repeating yourself across every tool, every agent, every conversation

There's no shared format. Every agent builder rolls their own. Users don't know what to include. Important details get missed.

## The Solution

`persona.json` is a structured profile with **10 life domains** and **120+ fields** that an agent can parse instantly:

| Section | What it covers |
|---|---|
| **Identity** | Name, location, timezone, languages |
| **Career & Skills** | Role, skills, tools, work style, aspirations |
| **Vehicles** | Make/model/year, mileage, weekly driving, maintenance history |
| **Health & Fitness** | Exercise, sleep, conditions, wearables |
| **Food & Diet** | Allergies, cuisines, cooking level, preferences |
| **Home & Living** | Home type, household, pets, smart home, climate |
| **Finance & Goals** | Income range, goals, budgeting, investments |
| **Hobbies & Interests** | Games, music, travel, sports, communities |
| **Personality** | MBTI, communication style, humor, values |
| **Aspirations** | Life goals, learning, challenges, causes |

**Fill what you want, skip what you don't.** Every field is optional. Agents should gracefully handle missing sections.

## Quick Start

### Option A: Use the Form (Non-Technical)

1. Download [`persona-form.html`](./persona-form.html)
2. Open it in your browser (just double-click)
3. Fill out the sections that matter to you
4. Click **Export** → Copy JSON or download the file

No installs. No accounts. Runs 100% locally — your data never leaves your browser.

### Option B: Write it Directly (Technical)

Create a `persona.json` file using the schema:

```json
{
  "_meta": {
    "version": "1.0",
    "generated": "2026-03-10T12:00:00Z",
    "format": "agent-persona-profile"
  },
  "identity": {
    "name": "Alex Chen",
    "preferred_name": "Alex",
    "location_city": "Austin",
    "timezone": "US/Central"
  },
  "vehicles": {
    "vehicle_1_make": "Toyota",
    "vehicle_1_model": "Camry",
    "vehicle_1_year": 2022,
    "vehicle_1_mileage": 34000,
    "vehicle_1_weekly_miles": 150,
    "vehicle_1_last_oil_change": "Jan 2025 / 31,000 mi"
  }
}
```

See [`examples/`](./examples/) for complete sample profiles.

## Using with Agents

### System Prompt Injection

The simplest approach — paste the JSON into your agent's system prompt:

```
You are a helpful personal assistant. Here is the user's profile:

<persona>
{contents of persona.json}
</persona>

Use this profile to personalize your responses. Reference specific details
when relevant. If information is missing, ask rather than assume.
```

### File Reference

Point your agent to read the file on startup:

```python
import json

with open("persona.json") as f:
    persona = json.load(f)

system_prompt = f"""You are a personal assistant.
User profile: {json.dumps(persona)}
"""
```

### MCP Tool

Expose sections as a tool so agents can query what they need:

```python
def get_persona_section(section: str) -> dict:
    """Returns a specific section of the user's persona profile."""
    with open("persona.json") as f:
        persona = json.load(f)
    return persona.get(section, {})
```

### Privacy Tiers

Each field in your profile can be tagged with a privacy level in the `_meta` section:

```json
{
  "_meta": {
    "privacy": {
      "finance": "agent-only",
      "health": "agent-only",
      "identity": "public",
      "hobbies": "public"
    }
  }
}
```

| Tier | Meaning |
|---|---|
| `public` | Safe to reference in shared/visible outputs |
| `agent-only` | Use for reasoning but don't surface in responses |
| `private` | Only use when explicitly asked |

## Schema

The full JSON Schema is available at [`schema/persona.schema.json`](./schema/persona.schema.json).

Validate your profile:

```bash
# Using ajv-cli
npx ajv validate -s schema/persona.schema.json -d my-persona.json
```

## Examples

- [`examples/alex-engineer.json`](./examples/alex-engineer.json) — Software engineer, car enthusiast, fitness-focused
- [`examples/maria-creative.json`](./examples/maria-creative.json) — Freelance designer, vegan, traveler
- [`examples/james-parent.json`](./examples/james-parent.json) — Working dad, budget-conscious, home DIY-er

## Contributing

This is v1. The format should evolve based on what agent builders actually need. Some ideas:

- **New sections**: subscriptions/services, tech devices, medical providers, weekly routines
- **Conditional fields**: more vehicle slots, child profiles, pet details
- **Import/export**: converters for other formats (YAML, TOML, Markdown frontmatter)
- **Agent integrations**: plugins for Claude, GPT, Cursor, Continue, etc.
- **Validation tools**: CLI to lint and validate profiles

Open an issue or PR. Keep it simple, keep it useful.

## License

MIT — do whatever you want with it.
