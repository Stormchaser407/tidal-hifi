# Multi-Scrobbler Local Setup

This folder is the local relay setup for Tidal Hi-Fi scrobbles into multiple services via multi-scrobbler.

## Files

- `config.json`: local source/client config (tracked template, keep real tokens local)
- `.tidal-hifi-token`: local endpoint token used by Tidal Hi-Fi (ignored by git)
- `.tidal-hifi-token.example`: token template
- `docker-compose.yml`: local compose stack
- `data/`: runtime state/cache/auth/session files
- `logs/`: local logs
- `secrets/`: local secret scratch files (ignored by git)

## First Run

1. Set endpoint token:
   - `cp -n .tidal-hifi-token.example .tidal-hifi-token`
   - edit `.tidal-hifi-token`
2. Edit `config.json`:
   - set `sources[0].data.token` to the same token value
   - set ListenBrainz credentials in `clients[].type == "listenbrainz"`
   - set Last.fm `apiKey` + `secret` in `clients[].type == "lastfm"`
   - Libre.fm uses placeholder key/secret (`anything`) with browser auth
3. Start stack:
   - `podman compose up -d`
4. Verify:
   - `curl http://localhost:9078/health`
   - `podman compose logs -f`

## Tidal Hi-Fi Settings

- Enable ListenBrainz: checked
- ListenBrainz API URL: `http://localhost:9078/1/submit-listens`
- ListenBrainz User Token: same value as `sources[0].data.token` in `config.json`
- Scrobble Delay: `5000`

## Service Auth URLs

After stack start, use browser auth for services that require a session key:

- Last.fm: `http://localhost:9078/api/client/auth?name=lastfm&type=lastfm`
- Libre.fm: `http://localhost:9078/api/client/auth?name=librefm&type=librefm`

## Known Good Validation

- In logs, each client should reach `Fully Initialized!`
- Playback should show `Scrobbled (Backlog)` lines for `listenbrainz`, `lastfm`, and `librefm`
