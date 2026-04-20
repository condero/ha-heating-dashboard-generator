# HA Heating Dashboard Generator

A Home Assistant template that generates a complete heating control dashboard. No manual editing.

1. Install [card-mod](https://github.com/thomasloven/lovelace-card-mod) (optional — see [install guide](docs/install-guide.md))
2. Go to **Developer Tools** > **Template**
3. Paste the content of `scripts/generate-dashboard.jinja` — the output appears automatically
4. Copy the output
5. **Settings** > **Dashboards** > **Add Dashboard** > name it > **Create**
6. Open the dashboard > **Edit** > **three-dot menu** > **Raw configuration editor** > paste > **Save** > close editor > **Done**

Done. See the [install guide](docs/install-guide.md) for card-mod options, troubleshooting, and customization (language, colors, display names).

## Requirements

- Home Assistant 2024.11 or later (for `floor_id()` support)
- card-mod (optional — needed for colored thermostat icons)

## License

MIT

## Disclaimer

This project was created with [Claude Code](https://claude.ai/code) and GLM 5.1.
