# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Auth.js (previously NextAuth.js) is a comprehensive authentication library for modern web applications. The project uses a monorepo structure with pnpm workspaces, built using Turborepo for efficient builds and testing.

## Development Commands

### Package Management
- `pnpm install` - Install dependencies
- Uses pnpm workspace with packages defined in `pnpm-workspace.yaml`

### Building
- `pnpm build` - Build core packages (`@auth/*` and `next-auth`)
- `pnpm build:app` - Build the Next.js example app
- `pnpm build:docs` - Build documentation site
- `turbo run build` - Build with Turborepo (respects dependency graph)

### Development
- `pnpm dev` or `pnpm dev:next` - Start Next.js development server with dependencies
- `pnpm dev:sveltekit` - Start SvelteKit development server
- `pnpm dev:express` - Start Express development server  
- `pnpm dev:qwik` - Start Qwik development server
- `pnpm dev:docs` - Start documentation development server
- `pnpm dev:db` - Start development with database dependencies

### Testing
- `pnpm test` - Run unit tests (excludes adapters with external dependencies)
- `pnpm test:e2e` - Run end-to-end tests with Playwright
- `pnpm test:e2e:watch` - Run e2e tests with UI
- Individual adapter tests often require specific setup (see `test.sh` files)

### Linting & Formatting
- `pnpm lint` - Run ESLint with caching
- `pnpm format` - Check formatting with Prettier
- `pnpm format:write` - Fix formatting issues
- Uses Lefthook for pre-commit hooks (formats staged files automatically)

### Email Testing
- `pnpm email` - Start fake SMTP server for testing email functionality

## Architecture

### Monorepo Structure
```
packages/
├── core/                    # Core Auth.js functionality (@auth/core)
├── next-auth/              # Next.js integration (next-auth package)
├── adapter-*/              # Database adapters (@auth/*-adapter)
├── frameworks-*/           # Framework integrations (@auth/*)
└── utils/                  # Shared utilities and scripts
```

### Core Package (`@auth/core`)
- Framework-agnostic authentication core
- Providers in `src/providers/` (70+ OAuth/OIDC/email providers)
- Actions in `src/lib/actions/` (signin, signout, callback, session)
- Built-in pages and styling in `src/lib/pages/`
- JWT handling, session management, and security utilities

### Framework Integrations
- `next-auth` - Next.js integration (App Router and Pages Router)
- `@auth/sveltekit` - SvelteKit integration with hooks and components
- `@auth/express` - Express.js integration
- `@auth/qwik` - Qwik framework integration
- `@auth/solid-start` - SolidStart integration

### Database Adapters
25+ adapters for different databases and services:
- SQL: Prisma, Drizzle, Kysely, TypeORM, Sequelize, pg, Neon
- NoSQL: MongoDB, DynamoDB, Firebase, Supabase, PouchDB
- Graph: Neo4j, Hasura, Dgraph, EdgeDB
- Cloud: Azure Tables, Xata, Fauna, SurrealDB
- Cache/KV: Upstash Redis, Unstorage

## Development Workflows

### Working with Adapters
1. Each adapter has its own test environment and setup script
2. Many adapters require external services (databases, Docker containers)
3. Check `test/test.sh` scripts for setup requirements
4. Adapters are excluded from main test suite due to external dependencies

### Framework Development
1. Framework packages are in `packages/frameworks-*`
2. Development apps are in `apps/dev/` for testing
3. Example apps are in `apps/examples/` for documentation

### Provider Development
1. OAuth providers are in `packages/core/src/providers/`
2. Provider types are auto-generated in build process
3. Each provider follows the OAuth 2.0/OIDC specification

### Testing Strategy
- Unit tests for core functionality and utilities
- E2E tests using Playwright for critical user flows
- Manual testing with development applications
- Database adapter tests are isolated and require external setup

## TypeScript Configuration
- Uses TypeScript 5.3.3 across the monorepo
- Project references configured in Turborepo
- Strict type checking enabled
- JSDoc comments required for public APIs in core packages

## Key Files & Conventions
- `turbo.json` - Defines build pipeline and task dependencies
- `eslint.config.mjs` - ESLint configuration with TypeScript, React, Svelte rules
- `lefthook.yml` - Git hooks configuration (auto-formatting)
- Each package has its own `tsconfig.json` and `package.json`
- Adapter packages include `typedoc.config.cjs` for documentation generation

## Environment Variables
Required for E2E testing:
- `AUTH_SECRET` - Secret for JWT encryption
- `AUTH_TRUST_HOST` - Trust host header in development
- Provider-specific credentials (e.g., `AUTH_KEYCLOAK_*` for Keycloak tests)

## Release Process
- `pnpm release` - Create release using custom tooling
- `pnpm peek` - Preview release changes
- Uses `@balazsorban/monorepo-release` with custom patches