# Setting Up a Custom Scrobbler with Multi-Scrobbler

This guide configures [multi-scrobbler](https://github.com/FoxxMD/multi-scrobbler) as a proxy for Tidal Hi-Fi scrobbles.

## Why Use Multi-Scrobbler?

- Scrobble to multiple services from one Tidal Hi-Fi source
- Keep Tidal Hi-Fi pointed at one local endpoint
- Inspect activity from a local dashboard

## Prerequisites

- Docker + Docker Compose
- ListenBrainz account and user token

## Local Folder Layout

Use this repo-local scaffold:

- `multi-scrobbler-setup/config.json`
- `multi-scrobbler-setup/.tidal-hifi-token`
- `multi-scrobbler-setup/logs/`
- `multi-scrobbler-setup/data/`
- `multi-scrobbler-setup/secrets/`

> `multi-scrobbler-setup/.tidal-hifi-token` and `multi-scrobbler-setup/secrets/*` are local-only and ignored by git.

## Step 1: Configure Files

1. Open `multi-scrobbler-setup/config.json`
2. Replace:
   - `REPLACE_WITH_VALUE_FROM_DOT_TIDAL_HIFI_TOKEN`
   - `REPLACE_WITH_LISTENBRAINZ_USERNAME`
   - `REPLACE_WITH_LISTENBRAINZ_TOKEN`

The Tidal token in `config.json` should match the token value you keep in `.tidal-hifi-token`.

## Step 2: Start Multi-Scrobbler

From repo root:

```bash
cd multi-scrobbler-setup
podman compose up -d
```

Verify:

```bash
curl http://localhost:9078/health
podman compose logs -f
```

Dashboard: <http://localhost:9078>

## Step 3: Configure Tidal Hi-Fi

In Tidal Hi-Fi settings (ListenBrainz section):

- Enable ListenBrainz: checked
- API URL: `http://localhost:9078/1/submit-listens`
- User Token: same token as `sources[0].data.token` in `multi-scrobbler-setup/config.json`
- Delay: `5000`

Save settings and restart Tidal Hi-Fi.

## Step 4: Live Scrobble Test

1. Play one full track in Tidal Hi-Fi.
2. Watch logs:

```bash
cd multi-scrobbler-setup
podman compose logs -f
```

3. Confirm a new listen appears in ListenBrainz.

## Troubleshooting

### Connection refused

- Confirm stack is running: `podman compose ps`
- Recheck health endpoint: `curl http://localhost:9078/health`

### Invalid token

- Confirm Tidal Hi-Fi User Token exactly matches `config.json`
- Check for extra whitespace

### No scrobbles on ListenBrainz

- Recheck ListenBrainz username/token in `config.json`
- Inspect `podman compose logs -f` for client errors

## Additional Clients

You can scrobble to Last.fm and Libre.fm from the same Tidal source.

1. Add `lastfm` and `librefm` clients in `multi-scrobbler-setup/config.json`.
2. Restart stack: `podman compose up -d --force-recreate`
3. Complete auth callbacks:
   - Last.fm: `http://localhost:9078/api/client/auth?name=lastfm&type=lastfm`
   - Libre.fm: `http://localhost:9078/api/client/auth?name=librefm&type=librefm`

## References

- Tidal Hi-Fi Issues: <https://github.com/Mastermindzh/tidal-hifi/issues>
- Multi-Scrobbler Docs: <https://foxxmd.github.io/multi-scrobbler/>
- Multi-Scrobbler Issues: <https://github.com/FoxxMD/multi-scrobbler/issues>
