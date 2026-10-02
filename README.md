# mindweave-news

News, and update notes for the mwcode app, the core and the CLI, shown in the app's **What's new** tab. A CLI update posted here reaches app users as a notification too. mwcode downloads `feed.json` and
`feed.sig` from this repo, checks the signature against the public key built into the app, and
shows only what checks out.

## Publishing

**What's new is a history.** Every release gets its own plain news item (kind `news`) and stays there, oldest at the bottom, newest on top. Never delete an old item and never hide one with `minVersion`/`maxVersion`: removing an item removes it from everyone's app. Use `update` items only for something the app itself can install, and `expires` only for a notice that really goes stale.

1. Edit `source.json` (the only file you write by hand). **The newest item goes first**: the
   latest one is always on top in the app. Items are shown by date, newest first, and items from
   the same day keep the order of this file. App 1.0.0 orders same-day items by id instead, so
   while it is in use, give same-day items ids that sort the same way (`a-...` for the newest,
   then `b-...`). Put pictures in `images/`
   (png, jpeg, webp or gif, up to 600 KB each) and refer to them as
   `{ "file": "images/name.png", "alt": "...", "caption": "..." }`.
2. From the mwcode-desktop folder: `node news/tools/publish.mjs ../mindweave-news`
   This checks every item the way the app will, signs the result with your private key, and writes
   `feed.json` and `feed.sig`.
3. Commit and push. Users see it the next time their app checks (at startup, then every ~6 hours).

## An item

```json
{
  "id": "unique-lowercase-id",
  "kind": "news",                 // or "update"
  "area": "app",                  // updates only: app | core | cli
  "version": "3.1.0",             // updates only, optional
  "command": "npm install -g mindweave@latest", // cli updates only, optional (this exact shape)
  "date": "2026-09-29",
  "title": "Up to 120 characters",
  "summary": "Up to 400 characters, shown on the card.",
  "points": ["a few short lines for the summary screen"],
  "sections": [{ "h": "Heading", "text": "or", "list": ["items"] }],
  "images": [{ "file": "images/x.png", "alt": "...", "caption": "..." }],
  "links": [{ "label": "GitHub", "url": "https://github.com/..." }, { "label": "X", "url": "https://x.com/..." }], // up to 4 buttons
  "link": { "label": "Full release on GitHub", "url": "https://github.com/..." }, // the release (the "Get X" button)
  "minVersion": "3.0.0",          // optional: only show to this version or newer
  "maxVersion": "3.2.0",          // optional: only show to this version or older
  "expires": "2026-12-01"         // optional: stop showing after this date
}
```

Text only. Links must be https and on github.com, x.com or the mwcode site. Without `links`, core and CLI updates get a GitHub button and news gets Website, GitHub and X. To take an item down, delete
it from `source.json` and publish again.

## Keys

The private key lives at `~/.mwcode-news/private.pem` on the publisher's machine and nowhere else.
Anyone holding it can put anything in front of every user, so it is never committed, never pasted,
and it is backed up offline. The public key is `mwcode-desktop/news/feedKey.js`.
