# Winger IQ: design and architecture

## 1. What the game is
A first-person decision trainer for young wingers (target age 10–14). Each scenario:
1. **Kick-off screen**: short match context (minute, score), how to play, mode switch.
2. **Build-up (about 6–8 s)**: seen through the winger's eyes. The player drags to turn their head and look around (scanning), and hears teammates shout.
3. **Decision**: the game slows down. Learn mode shows tappable targets and a "Coach's eye" hint; Pro mode shows nothing and punishes waiting ("Too slow!").
4. **Play-out**: the chosen option plays from the player's eyes, with short captions explaining what happens.
5. **TV replay**: the camera flies to a broadcast angle. It pauses on the key defender (tackle zone, distance, feet, which way he is showing you), then tags key players green (you looked at them) or red (you never looked).
6. **Result card**: rating (best / okay / poor / too slow), the reason, scan count, decision time, Football IQ changes and new badges. A panel underneath lists the cues (✓ / ✗ for spotted), the rule and the "But if…" line.

## 2. The four teaching mechanics
- **Scanning**: you only see what you look at. Key players count as "spotted" after about 0.2 s in the central 85% of view. Shoulder checks (turning more than 95°) are counted.
- **Listening**: teammates shout real calls ("Over!", "Man on!", "Here!"). In Learn mode, a nudge points you to the right person (or the defender behind you).
- **Reading the defender**: distance, set or moving, side-on stance and which way he shows you, all shown in the replay read-out.
- **Contrast pairs**: every scenario has two versions with the same start. One clear, multi-cue change flips the best play.

## 3. File structure (single HTML file)
Everything lives in `winger-iq-game.html`: CSS, HTML and one script. The main code blocks, in order:
- **Kits**: `KITS`, `OPP_RED`, `OPP_WHITE`, `GK_Y`, `GK_G` (bright, saturated; no grass-green kit). Opponents switch to white if the player's kit is red, orange or pink.
- **Scenarios**: `SCENARIOS` (version 1 of each), then `addPreRoll`, `buildPaths`, `BASE_VERSION`, `TWISTS` (version 2 changes), `makeTwist`, `VARIANT`, `ALL_SCEN`.
- **Motion**: path sampling (smooth Hermite curves), `posOf`, `dirOf`, ball segments (`ballAt`), `yawAt`.
- **Player profile**: localStorage key `wingerIQ.v1`. Holds name, number, kit, mode, stats, badges, results per scenario version, plays per scenario and graphics setting.
- **Sound**: Web Audio for the crowd, kicks, net, roar, groan and heartbeat; the browser's built-in speech for shouts.
- **Rendering**: a custom software 3D renderer on a 2D canvas (no WebGL). It covers the pitch, crowd, LED ad boards, goal with a bulging net, 3D skeleton athletes, floodlight shadows, replay labels and a heads-up display.
- **Game flow**: phases `brief → play (build-up + decision) → outcome → fly → replay → result`.
- **Input**: drag to look (left/right and up/down); tap chips or targets in the picture; keys 1–4, arrow keys and A/D/S.
- **Player card and onboarding**: card markup, edit screen, graphics toggle (Auto / High / Low).

## 4. Writing a scenario
Coordinates are in metres. `x` runs 0→105 towards the goal we attack; `y` is lateral, with positive values towards the right touchline (y = 34). Times are written **without** the 2.5 s pre-roll; `addPreRoll` shifts everything later automatically.

Main fields:
- `id`, `title`, `sub`, `context`, `minute`, `score:[us,them]`
- `freeze`: when the decision moment is reached (slow-motion starts 0.45 s before it).
- `pre`, `preYaw`, `yaw`: pre-roll length and where the winger's eyes look (keyframes `[t, degrees]`, positive = right). The player's own head turn is added on top.
- `ents`: every player, `{team:'us'|'them'|'gk', n:number, p:[[t,x,y],…]}`. `me` is the player.
- `ball`: segments of type `carry` (`who`, optional `face`) or `kick` (`to`: a player id or `[x,y,z]`; `lead`; `h` = arc height; optional `by` to force who kicks; `silent:true` for no sound, e.g. the ball dropping into the net).
- `poses`: body language `[[t0,t1,'jockey'|'sideon'|'point', arg]]`. `sideon` takes a signed angle (negative = showing you outside, positive = showing you inside). `point` takes a target `[x,y,z]`.
- `callouts`: shouts `{t0,t1,who,text,nudge?,look?}`. `look` makes the Learn-mode nudge point at someone other than the speaker.
- `marks`: key players, with labels for the replay tags; they also drive the scan score.
- `cues`: short lines for the panel, each linked to mark ids for ✓ / ✗.
- `stance`: replay read-out `{id, label, note, show?}`; `show` is the point the defender is pushing you towards.
- `coachEye`, `rule`, `counter`, `best` (option key), `late` (option played when too slow), `lateWhy`, `lateBeats`.
- `tv`: broadcast camera position `{pos:[x,y,z], look:[x,y]}`.
- `opts.A–D`: `label`, `aim` (tap target: `{kind, who | at, z?, alt?}`), `verdict`, `result`, `why`, `beats` (captions `[t,text]`), `end`, `resultAt`, `paths` (continuations after the freeze), `ball` (segments after the freeze).

**Version 2** (`TWISTS[id]`) is written as differences only: replaced player paths, poses, shouts, marks, cues, stance, hints, rule, counter, best, and per-option overrides. `makeTwist` deep-copies version 1 and applies them.

**Which version is shown:** the first play of a scenario is version 1, the second is version 2, then random. Results are stored per version id (`overlap`, `overlap-b`, …).

## 5. Ratings and the Football IQ card
After every real attempt, each stat moves 40% of the way towards a target:
- **VIS** = 40 + 59 × share of key players spotted
- **DEC** = 93 for best, 70 okay, 45 poor, 35 too slow
- **SPD** = based on decision time, scaled for Learn or Pro
- **AWR** = 45, plus 27 for a shoulder check, plus 27 for spotting the player a shout pointed to

Card tiers: bronze below 65, silver 65–79, gold 80 and up. Badges: Shoulder check, Big brain (best decision), Ice cold (best decision made fast), Eagle eye (all key players spotted).

## 6. Rendering notes
- Athletes are 3D skeletons: running cycle, jockey and side-on stances with a staggered lead foot, pointing, kicks with wind-up, headers, keeper dives, celebrations, hands on head, leaning when accelerating or braking, idle sway.
- Bodies are tapered limbs plus a rounded 8-sided torso and shorts, with kit trim, a number on the back, a chest crest, ears, simple faces and four hairstyles. **Flat/toon shading, not realistic 3D**: both `taper()` (limbs) and `shell()` (torso/shorts) light against the key light (`LDIR`) the same way, but the result is posterized into a few bold bands rather than a continuous gradient, and every limb, the head and the boots get a fixed-width (1.6px, screen-space, not world-scaled) dark outline (`OUTLINE`). This is a deliberate choice over more realistic shading: the audience is 12-year-olds glancing at a phone, often in portrait where the first-person stage is a fairly small strip — bold flat colour and a silhouette outline read in a fraction of a second at that size, where a soft gradient doesn't. It also matches the rest of the UI (chips, cards), which was already bold and flat. Players within `NEAR_DETAIL_R` (7m) of the camera get a denser torso/shorts shell (12-sided instead of 8) when `GFX.hi` is on — the extra vertices are spent only where the camera is close enough to notice, so the cost never scales with how many players are on the pitch. Outline/posterizing apply at every tier (they're cheap, in fact cheaper than the old continuous gradient); only the near-detail ring count and shadow light count are gated by `GFX.hi`.
- In first person you see your own legs, and the camera dips to watch your kicks and first touches. Your arms are hidden because they looked like blobs that close to the camera.
- To make teams readable: shirts keep bright colours under shading, distant players fade only slightly, each player has a soft team-coloured glow underneath, and distant shirts never shrink below a few pixels.
- Graphics Auto drops to Low (one shadow, no highlights, standard resolution) if frames get slow.
- LED boards: `AD_SLOTS` holds placeholders (A1–A3) and house ads ("SCAN BEFORE YOU RECEIVE", "HEAD UP. EYES OPEN.", "WINGER IQ"). They rotate every `ROTATE_S` seconds. Logos must be embedded as data URIs.

## 7. Design decisions (and why)
- **Own software renderer instead of a 3D engine**: runs on cheap tablets, loads instantly, and can be tested headless. Its ceiling is a "clean stylised mobile game" look; for realistic motion-captured players, see the roadmap.
- **Tap instead of swipe** to play the ball, because dragging is already used for looking. The yellow chips are real buttons and name the target ("PASS #2"). Tapping anywhere on a teammate's body counts as passing to him.
- **Slow-motion instead of a full freeze**: keeps the pressure real. Learn gives about 7 s, then holds and waits; Pro gives about 2.2 s, then "Too slow!".
- **Contrast versions instead of more one-off scenarios**: stops kids memorising answers.
- **Bright kits and team glows**: kids need to tell teams apart instantly on small screens.
- **The replay always runs at real speed**, so kids keep a true sense of match timing.

## 8. Testing approach used so far
- `node --check` on the extracted script after every change.
- Playwright (headless Chromium) scripts that start each scenario, wait for the decision, tap each chip or option, and wait for the result. Expected option, result and rating are checked for all 40 options (5 scenarios × 2 versions × 4 options).
- Test hooks are added only in a temporary copy (`window.__chosen`, `window.__chips`, `window.__result`), never in the shipped file.
- Screenshots on phone and laptop sizes for every visual change, and a frame-rate check (about 60 fps headless).
- **Not yet done:** real phones and tablets, iOS sound and speech behaviour, and testing with actual kids.

## 9. Constraints when published as a Claude artifact
- Maximum 16 MB, one self-contained file.
- Scripts may only load from cdnjs, jsdelivr, the Tailwind CDN or jQuery's CDN; fonts only from Google Fonts. Nothing else can load: no remote images, models or API calls.
- localStorage works but stays on that device only.

## 10. Player profiles, progression and privacy

No login or accounts yet — see `PRIVACY.md` for the full privacy notice and the GDPR reasoning behind this design.

**Storage.** Profiles live in `localStorage` under `wingerIQ.profiles.v1` (an array of player objects) plus `wingerIQ.activeId.v1` (which one is active). A profile created before this system existed is migrated automatically from the old single-profile key (`wingerIQ.v1`) the first time the game loads. Each profile carries a `schemaVersion` so future saves can be migrated safely, and a randomly generated `id` (`genId()`) that is not derived from the name or anything real-world — kept pseudonymous in case a future opt-in sync feature needs one, without retrofitting it onto existing data.

**Remembering players without login.** If there's exactly one saved profile, the game boots straight into it — zero extra friction, same as before this system existed. If there are two or more, a "Who's playing?" picker (`openPicker()`) appears at boot, and again from "Switch player" in the card modal. Picking a tile makes it the active `player`; a "+" tile creates a new one through the existing onboarding flow. No password: this solves "remember multiple players on a shared device," not authentication.

**Progression**, added to the existing `stats`/`badges`/`results`/`plays` fields:
- `trophies`: whole-number score that only ever goes up — awarded per real attempt in `applyStats()`: 15 for `best` (+5 more if it was the harder "twist"/version‑2 variant, since spotting the changed cue is the actual skill being taught), 5 for `okay`, 0 for `poor`/`too slow`. It never decreases: there's no matchmaking here to balance, unlike the games (e.g. Brawl Stars) this was modelled on, so a push/pull mechanic would only ever discourage retrying.
- `rank` (derived, not stored): a football-themed name from `RANKS`/`rankOf()` — Grassroots → Academy → Club Prospect → First Team → International — based on lifetime `trophies`. Deliberately different wording from the bronze/silver/gold card tier (`tierOf()`), which is a skill *snapshot* (rolling average of VIS/DEC/SPD/AWR), so the two numbers can't be confused for each other.
- `xp`/`level` (derived via `levelOf()`): +1 `xp` per real attempt regardless of verdict; rewards practice on its own axis, separate from skill or trophies.
- `streak`: consecutive `best` decisions; resets on anything else. Crossing 3 and 6 earns the `streak3`/`streak6` badges (lifetime, like the existing badges — earned once, never removed).
- `unlocks` + `TROPHY_ROAD`: trophy milestones (50/150/300/600/1000) unlock a card-trim cosmetic (`trim-copper`/`-slate`/`-amber`/`-emerald`/`-diamond`, a CSS `filter:drop-shadow` glow on the FUT card) rather than re-gating the kit colours, which stay free at character creation as before. The highest unlocked trim is applied automatically; there's no picker for it yet (see `roadmap.md`).

**Privacy controls**, reachable from onboarding, the card modal, and the player picker: "Export my data" downloads the active profile as JSON; "Delete my data" removes it from this device permanently (with a confirmation) and reloads. No new field here is ever sent anywhere — these controls only read and write `localStorage`.
