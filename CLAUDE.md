# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This is **百家姓AI姓氏溯源助手** (Baijia Surname AI Assistant) - a Chinese surname culture exploration app built with React, TypeScript, and Vite. Users can query surname origins, ancestral occupations, historical celebrities, and generate personalized ancient identity cards.

## Tech Stack

- **Frontend**: React 19, TypeScript 5.9, Vite 7
- **Styling**: Tailwind CSS v3.4.19, shadcn/ui components (New York style)
- **State Management**: React hooks + localStorage (via `useLocalStorage` hook)
- **Icons**: Lucide React
- **Voice Input**: Web Speech API

## Common Commands

All commands run from the `app/` directory:

```bash
cd app

# Development
npm run dev          # Start Vite dev server (port 5173)

# Building
npm run build        # TypeScript compile + Vite build (outputs to dist/)
npm run preview      # Preview production build locally

# Linting
npm run lint         # Run ESLint on all files
```

## Project Architecture

### Directory Structure

```
app/src/
├── pages/              # Page-level components (SPA routing)
│   ├── Home/           # Chat interface with AI assistant
│   ├── Detail/         # Surname detail view with rich content
│   └── Profile/        # User's favorites, history, generated cards
├── components/ui/      # shadcn/ui components (40+ components)
├── data/surnames/      # Static surname data (10 surnames)
├── hooks/              # Custom React hooks
│   ├── useLocalStorage.ts   # Persistent state via localStorage
│   ├── useVoiceInput.ts     # Web Speech API integration
│   └── use-mobile.ts        # Mobile breakpoint detection
├── utils/              # Business logic
│   └── aiEngine.ts     # Question classification & response generation
└── lib/
    └── utils.ts        # Utility functions (cn helper for Tailwind)
```

### Routing Architecture

Simple client-side routing in `App.tsx` uses a `PageType` state ('home' | 'detail' | 'profile'):

- **Home** (`/`): Chat interface for querying surnames
- **Detail** (`/detail`): Shows comprehensive surname info
- **Profile** (`/profile`): User's saved content (favorites, history, identity cards)

Navigation happens via callback props passed from App.tsx to pages.

### AI Engine (`utils/aiEngine.ts`)

The AI is rule-based, not using LLM APIs:

1. **Question Classification**: Regex pattern matching determines question type (origin, occupation, celebrity, story, culture, trivia, generate-card, general)
2. **Surname Extraction**: Matches input against 10 hardcoded surnames (李, 王, 张, 刘, 陈, 杨, 黄, 赵, 周, 吴)
3. **Response Generation**: Templates populated from static data in `data/surnames/index.ts`

### Data Model

Surname data structure (`SurnameData` interface):
- Basic info: surname, pinyin, rank, population
- Origin: source, ancestor, story, dynasty
- Occupation: title, description, level
- Identity: status, region, county, hall
- Celebrities: array of historical figures with stories
- Culture: idioms, poems, allusions
- Trivia: distribution, facts

### State Persistence

Three localStorage keys:
- `surname-favorites`: Array of favorited surnames
- `surname-history`: Array of chat history items
- `surname-cards`: Array of generated identity cards

## Key Conventions

### Component Imports

Always use path aliases:
```typescript
import { Button } from '@/components/ui/button'
import { useLocalStorage } from '@/hooks/useLocalStorage'
```

### shadcn/ui Components

Components are pre-configured in `components.json`:
- Style: New York
- Base color: Slate
- CSS variables enabled
- RSC disabled (client-side app)

### Styling

- Dark theme with ink-texture background (`bg-[#0a0a0a]`, `bg-ink-texture`)
- Chinese traditional aesthetic with gold accents
- Tailwind CSS variables for theming in `index.css`

### Voice Input

Uses Web Speech API with Chinese (`zh-CN`) recognition. See `useVoiceInput.ts` for implementation.

## Adding New Surnames

To add a new surname, edit `src/data/surnames/index.ts`:

1. Add entry to `surnamesData` object following the `SurnameData` interface
2. Update `getHotSurnames()` if it should appear in the hot list
3. No API changes needed - the AI engine uses pattern matching

## Environment Notes

- Node.js 20 required
- No environment variables needed (static data, no backend)
- Base URL configured as `./` in `vite.config.ts` for relative path deployment
- Uses `kimi-plugin-inspect-react` for development inspection
