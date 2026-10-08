# Winger IQ: privacy notice

**Short version: everything stays on your device. We don't collect anything.**

## For players and parents

Winger IQ doesn't have accounts, logins, or a server. When you create a player, your name, shirt number, kit colour, mode, stats, badges and trophies are saved only in this browser, on this device — using a standard web feature called `localStorage`. Nothing is ever sent to us, to Anthropic, or to anyone else. There's no tracking, no profiling, and no advertising based on who you are.

If more than one person plays on the same device, each player gets their own saved profile, picked from a "Who's playing?" screen. No password is needed — it's just a way to keep each player's progress separate, the same way a shared games console remembers more than one player.

You can, at any time, from the Privacy screen in the game:
- **Export your data** — download a copy of everything saved for that player, as a plain text file.
- **Delete your data** — permanently remove that player's saved progress from this device.

Ads on the stadium boards (when present) are the same for every player — nobody sees a different ad because of who they are, and no ad promotes gambling, alcohol or unhealthy food to children.

**The shared preview link** is behind a simple passphrase screen and marked `noindex` so it doesn't show up in search engines. This is a courtesy gate for friends testing early builds, not real access control — it doesn't change anything about player data, which still never leaves the device either way.

## For anyone reviewing this technically

- **No server-side processing.** The game is a single static HTML file. There is no backend, no API calls, no analytics beacon, and no third-party script that could read player data. Fonts load from Google Fonts over HTTPS; no player data is included in that request.
- **Data model.** Each local profile has a randomly generated `id` (`crypto.randomUUID()` or an equivalent fallback) — it is not derived from the player's name, device, or any other identifier, so it carries no information on its own.
- **Why this matters for GDPR.** GDPR regulates a *controller* **processing** personal data. Because no player data is transmitted anywhere, there is no processing by us to regulate — this is closer to a notebook a child writes in than a service that handles their data. This also sidesteps Article 8 (which, for consent-based processing of a child's data via an "information society service," requires parental consent below an age that different EU member states set between 13 and 16): there is no consent-based processing happening at all.
- **If this ever changes.** The moment any feature sends player data off-device (for example, the coach dashboard sketched in `roadmap.md`), this notice and the underlying design must change *before* that feature ships: explicit, verifiable parental consent for players below the local consent age, a data minimization review, a DPIA for any systematic monitoring of children, and no profiling-based ad targeting (required anyway under the EU Digital Services Act, Art. 28). The local `id` design above is intentionally ready for that: a future sync feature can use it as a pseudonymous identifier without having to retrofit one, and non-syncing players are unaffected.
- **Schema versioning.** Saved profiles carry a `schemaVersion` field so future changes to what's stored can migrate old saves safely rather than silently losing or misreading data.

This notice should be kept in sync with whatever the game actually stores — if you add a new field to a player profile, check whether this file still describes it accurately.
