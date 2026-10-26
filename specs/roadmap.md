# Roadmap

High-level implementation order, in very small phases. Each phase is roughly one working session and ends with a clear done-check, so the app is always in a working state. See `mission.md` and `tech-stack.md` for the why and the what.

## Foundation

### Phase 1: App skeleton
- Create the Next.js (App Router) + TypeScript project with `strict` mode.
- Add a home page that says "AgentClinic".
- **Done when:** `npm run dev` serves the home page and `npm run build` succeeds.

### Phase 2: Code quality tooling
- Add ESLint and Prettier, plus `lint` and `format` scripts.
- **Done when:** `npm run lint` passes on a clean checkout.

### Phase 3: Unit testing
- Add Vitest with one trivial passing test and a `test` script.
- **Done when:** `npm test` runs and passes.

### Phase 4: Styling base
- Add Tailwind CSS and a basic page layout (header, content area, footer).
- **Done when:** the home page renders styled in a modern browser.

### Phase 5: Database connection
- Add SQLite (`better-sqlite3`) and Drizzle ORM, with the migration command wired up.
- **Done when:** a first empty migration runs and creates the database file, which is git-ignored.

## Agents and ailments

### Phase 6: Agents table
- Add an `agents` table (name, model or type, created date) through a migration.
- **Done when:** the migration applies cleanly and a unit test inserts and reads one agent.

### Phase 7: Ailments table
- Add an `ailments` table (name, description) and link each ailment to an agent.
- **Done when:** a unit test saves an ailment for an agent and reads it back.

### Phase 8: List agents page
- Add a page listing agents from the database.
- **Done when:** seeded agents appear on the page.

### Phase 9: Register an agent
- Add a form to create an agent.
- **Done when:** submitting the form adds a new agent that appears in the list.

### Phase 10: Agent detail and ailments
- Add an agent detail page showing the agent's ailments, and a form to add one.
- **Done when:** a new ailment shows up on that agent's page.

## Therapies

### Phase 11: Therapies catalog
- Add a `therapies` table, seed data, and a page listing therapies.
- **Done when:** the seeded therapies are visible on the page.

### Phase 12: Match therapies to ailments
- Link therapies to the ailments they help, and show suggested therapies on the ailment view.
- **Done when:** an ailment shows its suggested therapies.

## Dashboard

### Phase 13: Dashboard shell
- Add navigation and a dashboard home page for agents and staff, linking to the existing pages.
- **Done when:** every existing page is reachable from the dashboard.

### Phase 14: Dashboard summary
- Show simple counts (agents, open ailments, therapies) on the dashboard.
- **Done when:** the counts match the database.

## Appointments

### Phase 15: Appointments table
- Add an `appointments` table linking an agent to a therapy at a time.
- **Done when:** a unit test creates and reads an appointment.

### Phase 16: Booking rules
- Add the rule that an agent cannot hold two appointments at the same time.
- **Done when:** unit tests cover the allowed and rejected cases.

### Phase 17: Book an appointment
- Add a booking form (agent, therapy, time).
- **Done when:** a valid booking is saved and a clashing one shows a clear error.

### Phase 18: View and cancel appointments
- List appointments on the dashboard and allow cancelling one.
- **Done when:** a cancelled appointment disappears from the list.

## Quality, measurement and polish

### Phase 19: End-to-end tests
- Add Playwright with a journey test: register an agent, add an ailment, book an appointment.
- **Done when:** the journey passes in Chromium.

### Phase 20: Cross-browser checks
- Run the journey in Firefox and WebKit as well.
- **Done when:** the journey passes in all three engines.

### Phase 21: Usage analytics
- Add the lightweight analytics script (see `pm_adr_guidance.md`, ADR-8).
- **Done when:** page views, bounce rate, time on page and browser usage appear in the analytics dashboard.

### Phase 22: Visual polish
- Refine the look and the playful tone of the copy across all pages.
- **Done when:** marketing signs off on the look in current Chrome, Edge, Firefox and Safari.

## Later (not scheduled)

- Authentication and separate roles for agents and staff.
- Event and funnel tracking for key journeys such as booking.
- Moving from SQLite to a larger database if usage outgrows it.
