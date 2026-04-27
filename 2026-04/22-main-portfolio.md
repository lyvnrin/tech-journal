# React: Personal Portfolio with Dark Mode, Animations & Contact Form
> 📅 2026-04-22

> View the [live page](https://lavanyakamble.vercel.app/) and [repository](https://github.com/lyvnrin/main-portfolio)

---

*Dear diary,*

*I built my portfolio today, again. That sentence feels stranger to write than I expected. It's one thing to make tools for yourself, but it's another to make something that's explicitly about presenting yourself. I kept having to resist the urge to just keep tweaking. At some point I had to decide it was done. It is done. It has a dark mode that actually works, transitions that don't feel cheap, and a contact form that submits correctly. I'm putting this down before I go back and change anything.*

---

## Overview

Two goals going in. First: get a portfolio actually built and deployed - something that could represent the work I've been doing. Second: use it as a deliberate excuse to work through things in React I'd been avoiding: theme context, transition libraries, controlled form state. All three showed up and all three pushed back a little.

## Part One: Project Structure & Setup

### Stack decisions

React via Vite for the build setup, faster than Create React App, cleaner output. No UI component library; everything hand-rolled. Framer Motion for animations. CSS custom properties for theming, toggled via a class on the document root.

### Folder structure

```
src/
  components/
    Navbar.jsx
    Hero.jsx
    Projects.jsx
    Contact.jsx
    ThemeToggle.jsx
  context/
    ThemeContext.jsx
  hooks/
    useForm.js
  App.jsx
  main.jsx
```

Kept it flat. One context, one custom hook, components that do one thing each.

### Theme context

Dark mode lives in a React context so any component can read or toggle it without prop-drilling:

```jsx
// context/ThemeContext.jsx
import { createContext, useContext, useState, useEffect } from 'react';

const ThemeContext = createContext();

export function ThemeProvider({ children }) {
  const [dark, setDark] = useState(() => {
    return localStorage.getItem('theme') === 'dark';
  });

  useEffect(() => {
    document.documentElement.classList.toggle('dark', dark);
    localStorage.setItem('theme', dark ? 'dark' : 'light');
  }, [dark]);

  return (
    <ThemeContext.Provider value={{ dark, toggle: () => setDark(d => !d) }}>
      {children}
    </ThemeContext.Provider>
  );
}

export const useTheme = () => useContext(ThemeContext);
```

CSS variables on `:root` and `:root.dark` handle the actual colour switching — the React side just manages the class.

```css
:root {
  --bg: #f5f5f0;
  --text: #1a1a1a;
  --accent: #2a5cff;
  --muted: #888;
}

:root.dark {
  --bg: #111;
  --text: #f0f0eb;
  --accent: #4d7cff;
  --muted: #666;
}
```

---

## Part Two - Animations with Framer Motion

### Approach

Two animation patterns used throughout: fade-and-slide-up on page section entry, and a staggered reveal for the projects grid. Both use `motion.div` with `initial`, `animate`, and `transition` props.

### Section entry animation

```jsx
import { motion } from 'framer-motion';

const fadeUp = {
  initial: { opacity: 0, y: 32 },
  animate: { opacity: 1, y: 0 },
  transition: { duration: 0.5, ease: 'easeOut' }
};

function Hero() {
  return (
    <motion.section {...fadeUp}>
      <h1>Hi, I'm [name].</h1>
      <p>I build things and write about it.</p>
    </motion.section>
  );
}
```

### Staggered project cards

```jsx
const container = {
  animate: { transition: { staggerChildren: 0.1 } }
};

const card = {
  initial: { opacity: 0, y: 24 },
  animate: { opacity: 1, y: 0 }
};

function Projects() {
  return (
    <motion.div variants={container} initial="initial" animate="animate">
      {projects.map(p => (
        <motion.div key={p.id} variants={card}>
          <ProjectCard {...p} />
        </motion.div>
      ))}
    </motion.div>
  );
}
```

The stagger means each card enters slightly after the previous one — takes about three lines of config and makes the grid feel considerably more considered than it otherwise would.

---

## Part Three - Contact Form

### Custom hook for form state

Extracted form logic into a hook to keep the component clean:

```js
// hooks/useForm.js
import { useState } from 'react';

export function useForm(initial) {
  const [values, setValues] = useState(initial);
  const [status, setStatus] = useState('idle'); // idle | sending | sent | error

  const handleChange = e => {
    setValues(v => ({ ...v, [e.target.name]: e.target.value }));
  };

  const handleSubmit = async (e, endpoint) => {
    e.preventDefault();
    setStatus('sending');
    try {
      const res = await fetch(endpoint, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(values)
      });
      setStatus(res.ok ? 'sent' : 'error');
    } catch {
      setStatus('error');
    }
  };

  return { values, status, handleChange, handleSubmit };
}
```

The component then just consumes the hook and renders based on `status`:

```jsx
function Contact() {
  const { values, status, handleChange, handleSubmit } = useForm({
    name: '', email: '', message: ''
  });

  return (
    <form onSubmit={e => handleSubmit(e, '/api/contact')}>
      <input name="name" value={values.name} onChange={handleChange} placeholder="Name" />
      <input name="email" value={values.email} onChange={handleChange} placeholder="Email" />
      <textarea name="message" value={values.message} onChange={handleChange} />
      <button type="submit" disabled={status === 'sending'}>
        {status === 'sending' ? 'Sending…' : 'Send'}
      </button>
      {status === 'sent' && <p>Message sent.</p>}
      {status === 'error' && <p>Something went wrong. Try again.</p>}
    </form>
  );
}
```

---

## Issues Encountered

- **Flash of wrong theme on load**: on first render, the page briefly appeared in light mode even when dark was saved in localStorage. Fixed by initialising the `useState` with a lazy function that reads from `localStorage` immediately rather than defaulting to `false` and correcting in an effect.

- **Framer Motion variants not staggering**: spent longer than I'd like admitting on this. The issue was that `initial` and `animate` were being set directly on the child `motion.div` elements rather than inherited via `variants`. Once the parent had `initial="initial" animate="animate"` and children used `variants={card}` without overriding `animate`, the stagger worked exactly as documented.

- **Contact form submitting on Enter in input fields**: the form was firing `handleSubmit` whenever the user pressed Enter mid-field, even before filling everything out. Adding `type="button"` to any non-submit buttons in the form and ensuring only one `type="submit"` existed resolved it. Obvious in hindsight, genuinely confusing in the moment.

- **CSS variables not applying to Framer Motion elements**: a specificity issue. `motion.div` renders a standard `div`; the variables were scoped to a class that wasn't being applied to the right element in the tree. Moved the theme class to `document.documentElement` (the `<html>` tag) rather than `document.body`, which made it the true root and resolved the cascade.

## Current Limitations

- Projects section is hardcoded; no CMS or data file driving it yet

## Next Steps
- Pull project data from a JSON file or markdown frontmatter so adding new work doesn't require touching component code
- Add a proper `/projects/[slug]` route with React Router for individual project write-ups

## Reflection

The theme toggle was the part I assumed would be trivial and wasn't. Not because it's technically hard, it isn't — but because there are three places state has to agree: React, the DOM, and localStorage. Getting all three in sync without a flash, without a stale read, without an effect that fires too late. The animation library by contrast was almost suspiciously straightforward once I understood that variants are inherited, not repeated. The contact form taught me to separate state logic from render logic earlier than I have been. That hook is four dozen lines and it made the component it feeds into genuinely readable. Going to keep doing that.
