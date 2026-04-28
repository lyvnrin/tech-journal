# Lovable Prototype: Cinema Hours

> 📅 2026-04-28

---

*Dear diary,*

*I wanted a study timer, but I didn't want another minimalist productivity app with a generic ring countdown and a Pomodoro label. I wanted it to feel like going to the pictures. So I made Cinema Hours - a study timer themed around an old movie aesthetic. You fill out a ticket stub, tap "watch movie", and the whole screen becomes a black-and-white countdown running through a film reel. Cream, ink, scallop-edged ticket. Intermissions instead of breaks. A picture log instead of a session history. I built it as a Lovable prototype - prompt-only, iterating my way through it - and the taste work was 90% of the job.*

> Inspo: the ticket stubs in my desk drawer, late-night TCM broadcasts, and the fact that every study app looks like every other study app.

---

## Overview

A study timer disguised as a trip to the cinema. The starting screen is a scallop-edged ticket stub where the user fills in task name, minutes, whether they want intermissions, and whether they want sound. Clicking "watch movie" transitions into a full-screen black-and-white countdown framed like a film reel - grain, vignette, flicker, scrolling sprocket strips. Completed sessions are saved to a "Picture Log" - a movie-log styled history page. The whole thing runs on cream, black, and white with a Midnight mode for late-night study.

Built entirely in Lovable via iterative prompting. No manual code edits, prompt-only workflow.

## What I Built

### 1. The Ticket Stub (Starting Screen)

A centered, horizontal ticket card with scalloped side edges (radial-gradient CSS masks), serif display title "CINEMA hours", and typewriter-styled input fields sitting on dashed cinema rules. Fields:

- **Task name**

- **Time** in minutes

- **Intermissions y/n** (breaks)

- **Sound on/off**

- **Preset chips**: Double Feature (25/5), Matinée (50/10), Epic (90/15)

Clicking "watch movie" validates the input (Zod schema, 1-240 minute range, non-empty task) and hands a `SessionInput` object to the timer.

### 2. The Film Reel Timer

Full-screen countdown. Black background, cream typography, giant Playfair-style digits. Around the timer:

- Scrolling sprocket strips top and bottom (looping background-position animation)

- Film grain overlay (SVG turbulence, stepped animation)

- Vignette and scanlines

- Flicker keyframe on the whole frame

- Mute toggle and exit button styled like projection booth controls

Intermissions flip the label and palette subtly so you know you're on break without leaving the reel.

### 3. Audio Cues

Optional chimes generated live via WebAudio (sine-wave tones, no external assets). Three cues:

- Focus → intermission

- Intermission → focus

- Session end

Toggle on the ticket, live mute button in the reel.

### 4. Picture Log (Session History)

A movie-log styled page at `/log` showing each completed session as a ticket-row with task name, total minutes, intermission flag, and date. Entries persist in `localStorage`. Link lives inside the ticket stub header so it feels like part of the cinema furniture, not a nav bar.

### 5. Midnight Mode (Dark Theme)

A toggle that flips the palette to noir - charcoal background, dark sepia ticket, cream ink. Implemented by swapping the `--cream`, `--ink`, `--sepia`, `--film` tokens inside a `.dark` class so every component re-themes without touching its markup. Preference persists in `localStorage` and respects `prefers-color-scheme`.

### 6. Aesthetic Direction

Old movie, not retro-kitsch. Iterated toward:

- Serif display type (Playfair Display) for titles, typewriter (Special Elite) for body

- Cream and ink only - no accent colours

- Ticket styled as real printed matter: scalloped side cuts, dashed rules, small-caps field labels

- Film reel styled like actual 35mm - sprocket holes, grain, vignette, flicker - not an emoji 🎬 slapped on a card

- Layout pulled away from browser borders so the ticket floats, not fills

## Prompt Iterations (in order)

1. Initial concept - ticket stub starting screen with task/time/intermissions inputs, leading to a full-screen black-and-white film reel countdown

2. Added Picture Log - session history page themed as a movie log, linked from the ticket

3. Ticket refinement - scalloped edges, Picture Log link moved into the ticket itself

4. Added audio cues for phase changes and session end, with a toggle on the ticket

5. Added timer presets (25/5, 50/10, 90/15) plus Zod validation for task name and minutes

6. Layout pass - ticket made horizontal, centered, smaller, pulled away from the browser edges

7. Added Midnight mode - full dark theme toggle persisting in localStorage

## What Worked Well

- The ticket metaphor landed on the first prompt - scallop edges and dashed rules did most of the heavy lifting

- CSS masks for the scalloped cuts were cleaner than SVG and re-theme automatically with the tokens

- WebAudio chimes meant no asset pipeline and no licensing - just sine waves tuned to feel analogue

- Token-swap dark mode: one `.dark` block re-themed every component without touching a single JSX file

- The film reel frame (grain + vignette + flicker + sprockets) composed well - each layer was a small prompt, and they stacked into something that actually feels cinematic

## Issues Encountered

- Early ticket versions filled the viewport edge-to-edge - took an explicit "pull it away from the borders" prompt to get it floating

- First pass at scalloped edges used clip-path and broke on the shadow - moving to CSS `mask` fixed both the cut and the drop shadow

- Validation initially allowed empty task names and 0-minute sessions - had to prompt specifically for a Zod schema with bounds

- "Play / watch movie" button styling drifted toward generic shadcn primary on a few iterations - needed re-grounding in the cinema tokens each time

- Audio autoplay policies meant the first chime sometimes got swallowed - resolved by initialising the AudioContext on the user's click

## Current Limitations

- No cloud sync - Picture Log lives in `localStorage`, so clearing site data wipes your study history

- No pause-and-resume across refreshes - reloading mid-session loses the timer

- No long-term stats view (streaks, total hours, most-watched task) - the Picture Log is chronological only

- Sprocket scroll speed is fixed - doesn't slow in the last minute for dramatic effect (it should)

- Mobile layout works but the film reel digits could be larger on small screens

## Next Steps (if taken further)

- Lovable Cloud for persistent Picture Log across devices

- Stats dashboard - weekly study hours, most-watched tasks, streak counter, all styled as a "box office" page

- Resume-from-refresh so a reload doesn't kill the session

- Actual short film-reel SFX layered under the tick (faint projector hum) as an optional ambient track

- "Double feature" flow - queue two tasks back to back with a real intermission screen between them

- Export Picture Log as a printable ticket strip

## Reflection

This was a taste project more than a technical one. The hard parts weren't the countdown or the state - they were getting the ticket to feel like a ticket, the reel to feel like a reel, and the dark mode to feel like midnight and not just "inverted". Lovable is genuinely good at this when you prompt with references instead of adjectives - "scalloped cinema ticket edges with dashed inner rules" gets you somewhere; "make it look nice" doesn't. Knowing the vocabulary of the thing you're making - sprocket, vignette, stub, intermission - is what turns a prompt into a design.

---
