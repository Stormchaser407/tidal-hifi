# Multi-Scrobbler Local Setup

This folder is a local scaffold for routing Tidal Hi-Fi ListenBrainz events through multi-scrobbler.

## Files

- `config.json`: multi-scrobbler source/client config
- `.tidal-hifi-token`: local token used by Tidal Hi-Fi (ignored by git)
- `.tidal-hifi-token.example`: token template
- `docker-compose.yml`: local compose stack
- `data/`: multi-scrobbler runtime config/state
- `logs/`: runtime logs
- `secrets/`: local secret files (ignored by git)

## First Run

1. Set your token:
   - `cp .tidal-hifi-token.example .tidal-hifi-token`
   - edit `.tidal-hifi-token`
2. Edit `config.json`:
   - replace `REPLACE_WITH_VALUE_FROM_DOT_TIDAL_HIFI_TOKEN`
   - replace ListenBrainz placeholders
3. Start stack:
   - `docker compose up -d`
4. Verify health:
   - `curl http://localhost:9078/health`
   - `docker compose logs -f`

## Tidal Hi-Fi Settings

- Enable ListenBrainz: checked
- API URL: `http://localhost:9078/api/listenbrainz/tidal`
- User Token: same value as `sources[0].data.token` in `config.json`
- Delay: `5000`
