# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Structure

Excalidraw is a **monorepo** with a clear separation between the core library and the application:

- **`packages/excalidraw/`** - Main React component library published to npm as `@excalidraw/excalidraw`
- **`excalidraw-app/`** - Full-featured web application (excalidraw.com) that uses the library
- **`packages/`** - Core packages:
  - `@excalidraw/common` - Shared utilities and constants
  - `@excalidraw/element` - Element manipulation and logic
  - `@excalidraw/math` - Mathematical operations and geometry
  - `@excalidraw/utils` - General utilities
- **`examples/`** - Integration examples (NextJS, browser script)
- **`dev-docs/`** - Documentation site using Docusaurus

## Development Commands

```bash
# Development
yarn start              # Start dev server (port 3000)
yarn test              # Run tests interactively with Vitest
yarn test:update       # Update test snapshots (run before committing!)
yarn test:app --watch=false  # Run tests once (use after modifications)
yarn test:typecheck    # TypeScript type checking
yarn test:all          # Run ALL tests (type, code, formatting)
yarn test:coverage     # Generate coverage report
yarn test:ui           # Interactive test UI
yarn fix               # Auto-fix formatting and linting issues

# Running single tests
yarn test path/to/test.test.tsx    # Run specific test file
yarn test --grep "pattern"         # Run tests matching pattern

# Linting & formatting
yarn test:code         # Run ESLint
yarn test:other        # Run Prettier checks
yarn fix:code          # Auto-fix ESLint issues
yarn fix:other         # Auto-fix Prettier formatting

# Build commands
yarn build             # Build the app
yarn build:package     # Build npm package
yarn build:preview     # Build and preview
```

## Development Workflow

1. **Package Development**: Work in `packages/*` for editor features
2. **App Development**: Work in `excalidraw-app/` for app-specific features
3. **Testing**: Always run `yarn test:update` before committing
4. **Type Safety**: Use `yarn test:typecheck` to verify TypeScript
5. **After Modifications**: Run `yarn test:app --watch=false` and fix any issues

## Architecture Notes

### Package System

- Uses Yarn workspaces for monorepo management
- Internal packages use path aliases (see `vitest.config.mts`)
- Build system uses esbuild for packages, Vite for the app
- TypeScript throughout with strict configuration
- Node.js requirement: 18.0.0 - 22.x.x

### State Management

- **Jotai** for state management with custom wrappers
- State modules: `editor-jotai.ts`, `app-jotai.ts`
- Custom hooks for state access
- **Import Restriction**: Never import from "jotai" directly, use the wrapped exports from `editor-jotai.ts`

### Testing

- **Vitest** as test runner with jsdom environment
- Tests colocated with source files (`*.test.ts`, `*.test.tsx`)
- Test utilities in `packages/excalidraw/tests/test-utils.ts`
- Always attempt to fix test failures
- Coverage requirements: Lines 60%, Branches 70%, Functions 63%, Statements 60%

### Action System

- User interactions handled through the action system in `packages/excalidraw/actions/`
- Actions follow the `Action` interface with `name`, `perform`, and optional `keyTest`
- Actions return `ActionResult` to modify elements, appState, or files
- Register actions using `register()` from `actions/register.ts`

### Code Standards

- Use TypeScript for all new code
- Prefer immutable data (const, readonly)
- Use optional chaining (?.) and nullish coalescing (??) operators
- Use functional React components with hooks
- Keep components small and focused
- Use CSS modules for component styling
- Prefer performant solutions (trade RAM for less CPU cycles)

### Type Guidelines

- **Always include `packages/math/src/types.ts`** when writing math-related code
- **Always use the `Point` type instead of `{ x, y }`**

### Canvas Architecture

- Multiple canvas layers for performance
- Canvas-based rendering with WebGL support
- Action system for user interactions (`actions/` directory)

## Commit Conventions

Use semantic commit prefixes:
- `feat:` - New features
- `fix:` - Bug fixes
- `chore:` - Maintenance tasks
- `docs:` - Documentation changes
- `perf:` - Performance improvements
- `refactor:` - Code refactoring
- `test:` - Test additions or modifications

## Import Guidelines

- Packages cannot import from `@excalidraw/excalidraw` except for types
- Use path aliases for internal package imports
- Jotai imports must use wrapped exports from `editor-jotai.ts`
- Follow ESLint import ordering rules