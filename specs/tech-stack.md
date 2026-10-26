# Tech Stack

A popular, reliable, all-TypeScript stack, per engineering's request.

## Language

- **TypeScript** everywhere, on the server and in the browser, with `strict` mode on.
- **Node.js** (current LTS) as the runtime.

## Framework

- **Next.js (App Router)** is a full-stack framework: server-side rendering, server routes, and the React dashboard live in one TypeScript project.
- Why: it is widely used and well documented, and it needs no separate backend and frontend. That keeps the clinic reliable and easy to demo.

## Frontend

- **React**, provided by Next.js, for the agent and staff dashboard.
- Server components by default; client components only where interaction needs them.

## Styling

- **Tailwind CSS** for a polished, consistent look in modern browsers.

## Database

- **SQLite** through `better-sqlite3`, stored in a local file.
- **Drizzle ORM** for typed queries and schema migrations.
- Why: no database server to run, which suits a small clinic and live demos.

## Tooling

- **npm** for packages.
- **ESLint** and **Prettier** for code quality.
- **Vitest** for unit tests.
- **Playwright** for end-to-end browser checks.

## Browser support

- Current versions of modern browsers (Chrome, Edge, Firefox, Safari).
