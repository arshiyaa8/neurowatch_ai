# NeuroWatch AI

BE-FAST bystander screening tool (front end, unchanged from before) now backed
by a small Node/Express API + SQLite database, so completed checks are saved
and show up in a "Saved History" panel.

## Run it locally

```
cd neurowatch-app
npm install
npm start
```

Then open **http://localhost:3000** (not the file directly — the backend
serves the front end now, so there's no separate `python -m http.server`
step anymore).

The first run creates `data/neurowatch.db` automatically. Delete that file
any time to wipe all history.

## What gets saved per check

Timestamp, the five BE-FAST badge results (text like "ok" / "asymmetry
detected"), the speech transcript, and GPS coordinates if location was
allowed. No video, no audio recording, no name — the app never had a login,
so there's no way to tie a saved row back to a specific person unless you
add one (see below).

## Deploying this online later

This runs as-is on any Node host (Render, Railway, Fly.io, a VPS). Two
things to handle before it's public:

1. **The SQLite file needs a persistent disk.** Most free hosting tiers
   wipe the filesystem on redeploy — check your host's docs for a
   "persistent volume" or "disk" add-on, and point `data/` at it via an
   env var if needed.
2. **Add authentication before real users touch it.** Right now anyone
   who can reach the server can read `/api/checks` and see everyone's
   history. For a demo that's fine; for anything real, put a login in
   front of it (even a simple shared password gate) before health-adjacent
   data from strangers starts landing in this database.

If you outgrow SQLite (multiple server instances, more traffic), swap
`db.js` for a `pg` (Postgres) connection pool — `server.js` doesn't need to
change beyond the three query calls, since they're already isolated in
that one file.
