# Decision Journal

A personal decision tracking web app that helps you log important decisions, record your reasoning, and review outcomes over time — so you can identify patterns in how you think and improve future decisions.

---

## Why this exists

Our brains are terrible at remembering what we actually thought at the time of a decision. After the fact, we unconsciously rewrite our reasoning to match the outcome — a cognitive bias called **hindsight bias**. If a decision worked out, we remember being confident. If it didn't, we remember having doubts.

Decision Journal captures your thinking *before* you know the outcome. Over time, you start noticing real patterns: when you're overconfident, what categories you decide well in, and whether your gut instinct is actually reliable.

---

## Features

- **Log decisions** with full context — situation, options considered, reasoning, expected outcome
- **Rate your confidence** (1–10) at the time of the decision before knowing the outcome
- **Review outcomes** once the result is clear — rate accuracy and record lessons learned
- **Spot patterns** via an insights dashboard with accuracy trends, category breakdowns, and confidence vs. accuracy scatter plots
- **100% private** — all data lives in your browser via `localStorage`, nothing is sent to a server
- **Export / import** your journal as JSON for backup or migration

---

## Screens

| Screen | Description |
|---|---|
| Dashboard | List of all decisions with category badges, status indicators, and summary metrics |
| New Decision | 3-step form — Context → Reasoning → Confidence |
| Entry Detail | Side-by-side view of original entry and review panel |
| Insights | Charts showing accuracy over time, decisions by category, confidence vs. accuracy |

---

## Tech stack

| Layer | Choice |
|---|---|
| Framework | React 18 (Vite) |
| Styling | Tailwind CSS + shadcn/ui |
| Routing | React Router v6 |
| Charts | Chart.js + react-chartjs-2 |
| Storage | localStorage (JSON) |
| Theme | CSS custom properties + `prefers-color-scheme` + manual toggle |
| Language | JavaScript (ES6+) |

---

## Project structure

```
decision-journal/
├── public/
│   └── favicon.svg
├── src/
│   ├── components/
│   │   ├── Dashboard.jsx          # Entry list, filters, search
│   │   ├── EntryCard.jsx          # Single decision card
│   │   ├── NewEntryForm.jsx       # 3-step form
│   │   ├── EntryDetail.jsx        # View + review an entry
│   │   ├── Insights.jsx           # Charts and pattern view
│   │   ├── ConfidenceSlider.jsx   # Reusable 1–10 slider
│   │   ├── CategoryBadge.jsx      # Colored pill label
│   │   ├── MetricCard.jsx         # Summary stat card
│   │   └── Navbar.jsx             # Shared top navigation
│   ├── hooks/
│   │   └── useEntries.js          # All localStorage logic
│   ├── utils/
│   │   └── export.js              # JSON export / import helpers
│   ├── App.jsx                    # Route definitions
│   ├── main.jsx                   # Entry point
│   └── index.css                  # Global styles + CSS variables
├── index.html
├── vite.config.js
├── tailwind.config.js
└── README.md
```

---

## Data model

Each decision is stored as a JSON object:

```js
{
  id: "1718293847123",           // Date.now() — unique and sortable
  title: "Accept senior role at Zeta",
  context: "Had two offers on the table...",
  optionsConsidered: "Stay at current job. Join a startup.",
  reasoning: "Long-term growth matters more than short-term salary.",
  expectedOutcome: "I'll grow faster and be promotion-ready in 2 years.",
  confidence: 7,                 // 1–10 slider at time of writing
  category: "Career",            // Career | Finance | Health | Personal | Other
  createdAt: "2025-04-18T10:23:00.000Z",
  review: null                   // null until reviewed
}
```

Once reviewed, the `review` field is populated:

```js
review: {
  outcome: "Joined Zeta. Shipped a major feature in 2 months.",
  accuracyRating: 9,             // 1–10 — how right were you?
  lessons: "Trust the team signal more than the salary number.",
  reviewedAt: "2025-05-01T08:45:00.000Z"
}
```

All entries are stored as a JSON array under the key `"decisions"` in `localStorage`.

---

## Getting started

### Prerequisites

- Node.js 18+
- npm or yarn

### Installation

```bash
# Clone the repo
git clone https://github.com/yourname/decision-journal.git
cd decision-journal

# Install dependencies
npm install

# Start the dev server
npm run dev
```

Open [http://localhost:5173](http://localhost:5173) in your browser.

### Build for production

```bash
npm run build
```

Output goes to the `dist/` folder. Deploy to Vercel, Netlify, or GitHub Pages — no server required.

---

## Dark mode

Dark mode is supported out of the box with two layers:

**System preference (automatic)**
The app reads `prefers-color-scheme: dark` by default, so users on a system-wide dark theme see the dark UI immediately with no action needed.

**Manual toggle**
A sun/moon icon button in the navbar lets users override the system preference. The chosen theme is saved to `localStorage` under the key `"theme"` and restored on every load.

**How it works in code**

All colors are CSS custom properties defined in `index.css`:

```css
:root {
  --bg-primary: #ffffff;
  --bg-secondary: #f5f5f4;
  --text-primary: #1a1a1a;
  --text-secondary: #6b7280;
  --accent: #534AB7;
  --border: #e5e5e3;
}

[data-theme="dark"] {
  --bg-primary: #141414;
  --bg-secondary: #1f1f1f;
  --text-primary: #f0f0ef;
  --text-secondary: #9ca3af;
  --accent: #7F77DD;
  --border: #2e2e2e;
}
```

Toggling dark mode is a single line:

```js
document.documentElement.setAttribute("data-theme", "dark");
```

The accent color shifts from `#534AB7` to a slightly lighter `#7F77DD` in dark mode to maintain contrast against dark backgrounds. All other component colors inherit automatically from the CSS variables — no component-level dark mode logic needed.

---



**Why localStorage and not a database?**
This is deeply personal data. Keeping it on-device is a feature — there's no account to create, no data breach risk, and it works offline. A JSON export button covers backup needs.

**Why a 3-step form?**
Breaking the form into Context → Reasoning → Confidence encourages deliberate thinking. A single long form gets filled out quickly and carelessly. Steps slow you down just enough.

**Why track confidence separately from reasoning?**
The gap between confidence and accuracy is where the insight lives. Someone who rates confidence 9 and accuracy 4 consistently is systematically overconfident. That pattern is invisible without both numbers.

**Why dark mode?**
Dark mode is implemented as a first-class feature — not an afterthought. The app respects the user's system preference by default via `prefers-color-scheme`, and also lets users toggle it manually. The preference is persisted in `localStorage` so it survives page refreshes. All colors are defined as CSS custom properties (variables), making the theme switch a single class toggle on the root element.

---

## Roadmap

- [ ] Reminder system — get notified to review decisions after 30/60/90 days
- [ ] Tags — freeform labels in addition to categories
- [ ] Search across all fields
- [ ] Mobile app (React Native)
- [x] Dark mode — system preference + manual toggle, persisted in localStorage
- [ ] Cloud sync (optional, opt-in)
- [ ] Weekly digest — email summary of pending reviews

---

## Contributing

Pull requests are welcome. For major changes, open an issue first to discuss what you'd like to change.

---

## License

MIT — free to use, modify and distribute.

---

## Acknowledgements

Inspired by the decision journaling practices of Annie Duke (*Thinking in Bets*) and the mental models community. The confidence vs. accuracy framework is drawn from research on forecasting and calibration training.
