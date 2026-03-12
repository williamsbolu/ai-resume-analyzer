# WARP.md

This file provides guidance to WARP (warp.dev) when working with code in this repository.

## Project Overview

AI Resume Analyzer is a React Router v7 application that helps users analyze resumes against job descriptions using AI. The app provides detailed feedback across multiple categories (ATS compatibility, tone & style, content, structure, and skills).

**Technology Stack:**
- React Router v7 (file-based routing)
- React 19
- TypeScript (strict mode)
- Vite
- TailwindCSS v4
- pdfjs-dist for PDF processing
- Zustand for state management

## Commands

### Development
```bash
npm install           # Install dependencies
npm run dev          # Start dev server at http://localhost:5173
npm run typecheck    # Run TypeScript type checking (generates types + tsc)
```

### Production
```bash
npm run build        # Build for production
npm run start        # Start production server
```

### Docker
```bash
docker build -t ai-resume-analyzer .
docker run -p 3000:3000 ai-resume-analyzer
```

## Architecture

### Rendering Mode
This application runs in **SPA mode** (`ssr: false` in `react-router.config.ts`). Server-side rendering is disabled.

### Routing Structure
Uses React Router v7's file-based routing system defined in `app/routes.ts`:
- Routes are defined as array exports (not file-based discovery)
- Route components live in `app/routes/`
- Currently: `index("routes/home.tsx")` is the only route
- Route types are auto-generated in `.react-router/types/` by the `typecheck` command

### Import Aliases
The project uses TypeScript path aliases configured in `tsconfig.json`:
- `~/*` maps to `./app/*`
- Example: `import Navbar from "~/components/Navbar"`

### Component Organization
- **Components:** `app/components/` - Reusable UI components (Navbar, ResumeCard, ScoreCircle)
- **Routes:** `app/routes/` - Page-level components
- **Types:** `types/index.d.ts` - Global type definitions (Resume, Feedback, Job)
- **Constants:** `constants/index.ts` - Static data and AI prompt formatting

### Data Model
The core types are:
- `Resume` - Contains company/job info, image/PDF paths, and structured feedback
- `Feedback` - Multi-category scoring system with tips for improvement
  - Categories: ATS, toneAndStyle, content, structure, skills
  - Each category has a score (0-100) and tips array
  - Tips have type ("good" | "improve"), title, and explanation
- `AIResponseFormat` - Template string defining expected AI response structure

### Styling Approach
- Uses TailwindCSS v4 with custom theme defined in `app/app.css`
- Custom utilities: `primary-gradient`, `text-gradient`, `inset-shadow`, `gradient-hover`
- Custom components: `.resume-card`, `.navbar`, `.feedback-section`, `.score-badge`
- Color palette uses custom CSS variables (`--color-*`)
- Responsive design with `max-sm:`, `max-md:`, `lg:`, `xl:` breakpoints

### AI Integration Strategy
The `prepareInstructions` function in `constants/index.ts` generates prompts for AI resume analysis:
- Takes job title, description, and response format
- Instructs AI to be thorough and critical (not lenient with scoring)
- Expects JSON response matching the Feedback interface
- AI should consider job description context when available

## Key Patterns

### Route Type Generation
React Router v7 generates route-specific types. Import them as:
```typescript
import type { Route } from "./+types/route-name";
```

### Component Imports
Always use the `~` alias for app imports:
```typescript
import Navbar from "~/components/Navbar";
```

### Adding New Routes
1. Create route file in `app/routes/`
2. Add route to the array in `app/routes.ts` using React Router dev utilities
3. Run `npm run typecheck` to generate route types

### PDF Processing
The project includes `pdfjs-dist` for PDF parsing. This is likely used for extracting resume text before sending to AI for analysis.
