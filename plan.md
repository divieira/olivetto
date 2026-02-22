# Olivetto - Toddler Reading App

## Overview
A single-page static app that helps a 3-year-old learn to read and write in Brazilian Portuguese. As the child types letters, each partial word is spoken aloud (e.g., G → GA → GAT → GATO), reinforcing letter-sound association.

## Architecture
- **Single `index.html` file** — zero dependencies, zero build step
- **Hosted on GitHub Pages** from the repo root
- **localStorage** for persistence (page history saved at every keystroke)

## UI Layout

```
┌──────────────────────────────────────────────┐
│  🔴 🟢 🔵 🟡 🟠 🩷 🟣 ⚫                    │  ← color dots (top bar)
│                                              │
│                                              │
│                                              │
│              GATO█                           │  ← big uppercase text + blinking cursor
│                                              │
│                                              │
│                                              │
│  ◀                                        ▶  │  ← prev / next (bottom corners)
└──────────────────────────────────────────────┘
```

## Implementation Steps

### 1. Create `index.html` with inline CSS + JS
Single file keeps it simple and GitHub Pages-ready.

### 2. Typography & Layout
- Use **Google Font "Nunito"** — rounded, friendly, highly legible for kids
- Letters: ~20vw font size (huge), uppercase via CSS `text-transform: uppercase`
- Centered on a white page, blinking cursor after the text
- Viewport fills the whole screen (`100dvh`), no scrolling

### 3. Color Selector (top bar)
- Small row of colored circles: Red, Green, Blue, Yellow, Orange, Pink, Purple, Black
- Tap a circle → text changes to that color
- Selected color stored in localStorage and restored on reload
- Default: black

### 4. Text Input & Speech
- The page itself captures keystrokes (no visible `<input>` — the whole page is the "input")
- **Letters only** (A-Z, plus accented characters: Ç, Ã, Õ, É, Ê, Á, À, Ú, Í, Ó, Â, Ô)
- Each keystroke:
  1. Appends the uppercase letter to the current word
  2. Saves to localStorage immediately
  3. Speaks the **full current text** using `SpeechSynthesis` API with `lang: "pt-BR"`
- **Space** starts a new word (spoken as the full text so far)
- **Backspace** removes last character (speaks remaining text)
- **Enter** moves to a new page (clears screen, starts fresh entry)

### 5. Page Navigation (prev/next)
- Each "page" is a separate entry in a pages array stored in localStorage
- `◀` (bottom-left) goes to previous page (read-only view, or editable — editable is simpler)
- `▶` (bottom-right) goes to next page, or creates a new blank page if at the end
- Current page index also stored in localStorage
- Pages are always editable

### 6. localStorage Schema
```json
{
  "olivetto_pages": ["GATO", "BOLA", ""],
  "olivetto_current_page": 2,
  "olivetto_color": "#000000"
}
```
Saved on every keystroke for resilience (toddler may close the tab at any moment).

### 7. Mobile / Tablet Friendly
- Touch-friendly color circles (min 44px tap target)
- On mobile, tap the main area to bring up the keyboard
- Use a hidden `<input>` focused at all times to trigger the mobile keyboard
- Nav buttons big enough for small fingers

## Speech Details
- Use `window.speechSynthesis.speak()` with a `SpeechSynthesisUtterance`
- Set `utterance.lang = "pt-BR"`
- Cancel any in-progress speech before speaking the new text
- Rate slightly slower than default (~0.9) for clarity

## What's NOT in scope
- No backend, no database, no auth
- No word suggestions or dictionary
- No animations beyond the blinking cursor
- No sound effects beyond speech
