# Winger IQ: roadmap
 
## Done
- 5 scenarios × 2 versions (wide 1v1 with overlap, in the box, back to goal, early cross, 2v1 counter). Each has 4 options with their own outcomes, captions, reasons, rules and "But if…" lines.
- Head control (scanning), shouts with Learn-mode nudges, tap-to-play with named chips, slow-motion decisions, Learn and Pro modes, "Too slow!" in Pro.
- TV replay with green/red spotted tags and a defender read-out (tackle zone, distance, feet, which way he is showing you).
- Football IQ card (VIS, DEC, SPD, AWR; bronze/silver/gold), 4 badges, player creation (name, number, kit), progress saved on the device.
- 3D skeleton athletes with body language, first-person legs, floodlight shadows, net bulge, camera shake, scoreboard, celebrations.
- Bright kits, team glows, minimum shirt size for distant players.
- LED ad boards with placeholder slots and house ads.

## Must do before a public launch
1. **Coach review** of all 10 versions (use `scenarios.md` as the review sheet). It needs a licensed youth coach (UEFA B or higher).
2. **Hosting-ready version**: fonts bundled with the game, a privacy notice and an imprint page. Host on a free static service (Netlify, Vercel, Cloudflare Pages or GitHub Pages) under its own domain.
3. **Real device testing**: older Android phones, iPhone and iPad (sound, speech, performance).
4. **Slovenian translation** (plus English), including Slovenian voices where the device has them.
5. **Colour-blind safety** for the green/red replay tags: add a second difference such as shape or pattern.

## Next for engagement
6. **Speed ladder** (designed, not built): build-up at 0.5× / 0.75× / 1× / 1.25×. Ratings are capped by speed (70 / 80 / 92 / 99), so gold needs match speed. One star per speed per scenario; Turbo unlocks after the best decision at 1×. The decision window stays the same; the replay always runs at 1×.
7. **20-second tutorial** play that teaches dragging to look and tapping.
8. **More content**: 20–30 scenarios in levels, then other positions (full-back, number 10, striker).

## Business
9. **Accounts and a coach dashboard**: team codes, each player's VIS/DEC/SPD/AWR, homework sets. This is the main revenue idea (clubs pay). It needs a small server and parental consent for under-16s.
10. **Privacy-friendly usage numbers**: completion, return visits, most-replayed scenarios.
11. **Ads**: sponsor boards that are the same for everyone, with child-appropriate brands only. The EU Digital Services Act bans profiling-based ads to minors.

## Graphics options (decided: polish first, done)
Next step up, if kids want more: a WebGL engine with rigged, motion-captured players (one shared body model and about 10 shared animations). It needs licensed character assets and its own hosting. Keep the current renderer as a fallback for weak devices.
 
## Distribution plan
1. Pilot with 1–2 youth teams through a coach for one week, and watch whether kids ask to play again.
2. Short first-person + replay clips on TikTok, YouTube Shorts and Instagram.
3. Coach communities (e.g. Reddit r/bootroom, coaching groups), then clubs, academies and the football association (NZS) grassroots programmes.
4. Later: itch.io, and possibly kids' web game sites (check their ad rules first).
 