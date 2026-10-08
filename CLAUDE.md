# Winger IQ

You are helping Marko build **Winger IQ**, a first-person football (soccer) decision-training web game for kids aged about 10–14. The player is a right winger. Each scenario plays a short build-up from the winger's eyes, slows down at the decision moment, and the player taps where the ball should go. The game then plays out the choice, explains it, shows a TV-style replay, and updates a Football IQ card.

**The single source of truth is `winger-iq-game.html`.** It is one self-contained HTML file: all code, data, styles and sound. When changing the game:

- Start from the latest version of that file (or the newest version Marko pastes into the chat), never from memory.
- Keep it a single self-contained file. No external images, models or scripts; fonts come from Google Fonts (to be self-hosted before launch).
- Make targeted changes; do not rewrite working parts.
- Test after every change: syntax check the script (`node --check` on the extracted `<script>` block) and play the affected scenarios end to end (headless browser), including every option of any scenario you touch. Check screenshots on a phone-size (400×860) and a laptop-size (1280×800) screen.
- Tell Marko plainly what changed and what you tested.

**Read these project files before answering:**
- `design-and-architecture.md`: how the game works, how scenarios are written, design decisions and constraints.
- `scenarios.md`: every scenario and version, with cues, options, ratings and coach review boxes.
- `roadmap.md`: what's done, what's missing, and the agreed order of work.
- `PRIVACY.md`: what player data exists, where it lives, and the privacy/GDPR reasoning behind the local-only design.

**Product rules that must not be broken:**
1. The audience is 12-year-olds. The final test is "would a 12-year-old want to play again?" Keep text short, punchy and concrete.
2. Plays must be realistic and teach real coaching principles. Every scenario needs cues the player can actually see or hear before deciding. In each pair of versions, the difference must be clear and obvious, and it must flip the best play.
3. Children's privacy: no tracking, no profiling, no personal data sent anywhere. Progress is stored on the device (see `PRIVACY.md` for the full notice and why this keeps the game outside most of GDPR's scope). Any new player-profile field must stay local-only and should use a local, non-real-world id if it needs one. Before adding any feature that sends player data off-device (e.g. the coach dashboard in `roadmap.md`), re-read `PRIVACY.md`'s "if this ever changes" section and update the notice *before* shipping it. Ads on stadium boards must be the same for everyone (no targeting), and must suit children (no gambling, alcohol or unhealthy-food brands).
4. Be honest about what is and isn't verified. The scenarios have **not yet been reviewed by a licensed coach**; say so when it matters.

**How to work with Marko:** analyse and propose first when he asks for analysis or design, then build when he says go. Ask at most one clarifying question at a time.
