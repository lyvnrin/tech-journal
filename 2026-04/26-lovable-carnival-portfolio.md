# Lovable Prototype: Carnival Portfolio
> 📅 2026-04-26

---

*Dear diary,*

*I had an idea that I was slightly embarrassed about for about thirty seconds, and then I just went for it. A portfolio website where the navigation is a carnival shooting gallery. Your mouse is the gun. About, Skills, Projects, Contact are the moving targets. You have to shoot all four to "win", and the end screen is a grand prize reveal. I built it as a Lovable prototype - just prompting my way through it iteratively - and honestly it came out better than it had any right to. The aesthetic started comic-book chaotic and I dragged it into something more editorial and chic. Serif fonts. Moody palette. Carnival yes, but make it Vogue.*

---

## Overview

A portfolio site where the user doesn't navigate — they play. The interface is a shooting gallery: four moving targets labelled with portfolio sections. Each hit drops a poster-style panel revealing the content. All four posters collected triggers an end screen with a grand prize message, a progress meter, and click-to-scroll access to every section.

Built entirely in Lovable via iterative prompting. No manual code edits, prompt-only workflow.

## What I Built

### 1. The Core Mechanic

Mouse-as-gun shooting gallery. Four targets move horizontally across the screen:

- **About**
- **Skills**
- **Projects**
- **Contact**

Clicking a target triggers a hit animation and drops a poster-style panel with the section content. The game continues until all four are collected.

### 2. Welcome / Intro Screen

On first load, a full-screen welcome state appears before any gameplay. Styled like a vintage carnival entrance board - serif headline, atmospheric background, a "Press to Play" prompt. Sets the tone immediately so the interaction model isn't confusing.

### 3. Poster Drop Mechanic

Each successfully hit target reveals its section as a poster dropdown - content unfurls into view rather than snapping open. Kept intentionally theatrical. Users can read the content and then return to the game to collect the remaining targets.

### 4. End Screen - Grand Prize

Once all four posters are collected, an end screen appears with:

- A grand prize message (kept sweet, not ironic)
- A **progress meter** showing which posters were collected, with remaining ones highlighted
- **Click-to-scroll navigation** so users can tap any section from the end screen and jump directly to it, with the first interactive element in that section focused automatically
- A **"Play Again"** option that resets the full game state - back to the welcome screen, targets moving again
- A **full portfolio view** - all four sections visible simultaneously for users who want to actually read everything properly without replaying

### 5. Aesthetic Direction

Started too comic-book. Iterated toward:

- Serif typography throughout (no sans-serif, everything felt too modern and broke the mood)
- Muted, moody palette: deep backgrounds, warm accent tones, nothing neon
- Carnival but editorial: like a 1920s fairground poster, not a theme park app
- Poster panels styled as real printed matter: borders, structured layouts, considered spacing

The shift from "playful" to "chic" was the most important prompt iteration of the session.

## Prompt Iterations (in order)

1. Initial concept - shooting gallery portfolio with four targets and poster drops
2. Added welcome/intro screen
3. Added end screen with grand prize message and play again option
4. Added progress meter to end screen showing collected vs remaining posters
5. Added click-to-scroll navigation from end screen to individual sections
6. Aesthetic overhaul - serif, muted palette, less comic-book more editorial carnival
7. Added full "view all sections" mode on end screen for non-game browsing

## What Worked Well

- The core mechanic landed immediately - intuitive without explanation
- Lovable handled the hit detection and state tracking cleanly from a single prompt
- The poster-drop animation added a lot without much prompting effort
- The aesthetic pivot was surprisingly effective - one well-directed prompt changed the whole feel

## Issues Encountered

- Early versions leaned too hard into bright, playful carnival colours - needed explicit direction to go muted and serif
- Progress meter took a couple of iterations to show *remaining* posters clearly, not just completed ones
- Click-to-scroll + first-element focus required a specific prompt - Lovable defaulted to scroll-only without the focus behaviour
- "Play Again" initially reset to the game state, skipping the welcome screen - had to explicitly prompt for a full reset

## Current Limitations

- No mobile touch support: mouse-as-gun doesn't translate to tap without reworking the mechanic
- No persistent state: closing the tab loses all collected posters
- The "view all sections" end screen is functional but not as polished as the game screens
- Target movement speed is fixed, no difficulty setting or variation

## Next Steps (if taken further)

- Mobile version - possibly a tap-to-aim mechanic instead of continuous mouse tracking
- Deploy as actual portfolio (replace placeholder content with real About/Skills/Projects/Contact)
- Animate the poster panels more - currently drop, could unfurl or stamp
- Sound design - a subtle click or carnival ambient layer would complete the aesthetic
- Add a "high score" / speed-run mode for returning visitors

## Reflection

This is exactly what Lovable is useful for: ideas that are slightly ridiculous but technically non-trivial to scaffold from scratch. The shooting gallery mechanic would have taken a full day to build properly in React. In Lovable, the hard part was taste, not code. Getting the aesthetic right through prompts alone is its own skill, and it's one worth practising. Knowing what to ask for - and how specifically to ask for it - is half the work.
