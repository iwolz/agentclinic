# AgentClinic: Technical Decisions in Non-Technical Terms

**Audience:** Product management. No programming background is assumed.
**Companion to:** `specs/tech-stack.md` (the technical version of this document).
**Purpose:** Explain what we chose, why, what it means for the product and the schedule, and what it would cost to change our minds.

## How to read this document

Each decision is written as an Architecture Decision Record (ADR), a short, standard format for recording a technical choice. Every record answers the same questions:

| Heading | What it tells you |
|---|---|
| **The question** | What we had to decide |
| **Our decision** | What we picked |
| **In plain English** | What it means, using everyday comparisons |
| **Why we chose it** | The reasons, tied to stakeholder needs |
| **Alternatives we passed on** | What else we considered and why not |
| **What it means for you** | Effects on features, schedule, risk and cost |
| **How hard is it to change later?** | Whether the decision is a one-way door or a two-way door |

**A word on "reversibility."** Some choices are cheap to undo, like repainting a room. Others are expensive, like moving a load-bearing wall. We flag which is which, so you know where to spend your attention.

## The big picture in one paragraph

We are building AgentClinic as **one web application** written in **one programming language (TypeScript)**. It shows pages to agents and staff, handles their requests, and stores information in **a simple file-based database**. We chose popular, well-supported tools, so we can find help easily, hire for them easily, and rely on them to keep working. We also added automatic quality checks so that mistakes get caught early, before a user sees them.

## Decision summary

| # | Decision | Choice | Reversibility |
|---|---|---|---|
| 1 | Programming language | TypeScript | Hard to change |
| 2 | Application framework | Next.js | Hard to change |
| 3 | Look and feel of the dashboard | React + Tailwind CSS | Moderate |
| 4 | Where data is stored | SQLite | Moderate (planned upgrade path) |
| 5 | How the app talks to the database | Drizzle ORM | Moderate |
| 6 | Quality and safety tools | ESLint, Prettier, Vitest, Playwright | Easy |
| 7 | Browser support | Current modern browsers | Easy |
| 8 | Measuring site usage | Plausible Analytics (lightweight, hosted) | Easy |

---

## ADR-1: Programming language is TypeScript

**The question.** What language should we write the product in?

**Our decision.** TypeScript, for the whole product (the part users see and the part running behind the scenes).

**In plain English.** TypeScript is JavaScript, the language every web browser understands, plus a built-in spell-checker. Imagine writing a form where every field must be filled in a certain way, such as a date in a date box and a number in a number box. TypeScript catches "you put a name in the date box" while we are building, rather than when an agent is booking an appointment.

**Why we chose it.**
- Engineering (Mary) asked for a popular TypeScript-based stack.
- One language everywhere means one team can work on any part of the product. There are fewer hand-offs and fewer misunderstandings.
- The built-in checking prevents a whole category of bugs before they ever reach users, which supports Mary's goal of a *reliable site*.

**Alternatives we passed on.**
- *Plain JavaScript:* the same language without the spell-checker. Faster to start, but more bugs reach users.
- *Python, Java, or C#:* excellent languages, but then the browser side and the server side would need different languages and potentially different teams.

**What it means for you.** Slightly more effort up front, in exchange for fewer surprises and fewer bug-fix cycles later. It is also one of the easiest languages to hire for.

**How hard is it to change later?** *Hard.* The language touches every file. Treat this as settled.

---

## ADR-2: Application framework is Next.js

**The question.** A "framework" is a ready-made foundation, like buying a house frame instead of cutting every beam yourself. Which foundation do we use?

**Our decision.** Next.js, which runs on Node.js (the engine that lets TypeScript run on a server).

**In plain English.** A web product needs two halves: the *front of house* (the pages people see and click) and the *back of house* (the kitchen that processes requests, saves data and applies rules). Many projects build these as two separate things. Next.js lets us build both halves in **one project**, in one language, and deploy them together.

**Why we chose it.**
- It is one of the most widely used web frameworks, so there is a huge amount of documentation, tutorials and available talent.
- One project means fewer moving parts, which means a more reliable product (Mary) and faster delivery of Susan's features.
- It produces fast-loading pages, which helps Steve's goal of an attractive site that works well.

**Alternatives we passed on.**
- *Express or Fastify (back-of-house only):* excellent, but we would still need to build and connect a separate front of house.
- *NestJS:* very structured, and better suited to large enterprise systems. It is heavier than a clinic needs.
- *Separate frontend and backend projects:* more flexible at huge scale, but more to build, coordinate and keep in sync.

**What it means for you.** Faster path to the first working version, and one place where everything lives. The trade-off is that we are tied to the way Next.js does things, and the framework evolves, so we will occasionally schedule upgrade work.

**How hard is it to change later?** *Hard.* This is the frame of the house.

---

## ADR-3: Dashboard built with React and styled with Tailwind CSS

**The question.** How do we build and style the screens agents and staff use?

**Our decision.** **React** for building the interactive screens and **Tailwind CSS** for the visual styling.

**In plain English.**
- *React* builds screens out of reusable pieces, like LEGO bricks. Build an "appointment card" once and reuse it everywhere. Change the brick and every place it appears updates together. This keeps the dashboard consistent.
- *Tailwind CSS* is a toolkit of ready-made styling instructions ("make this blue", "add this much space"). Designers and developers use it to make a polished, consistent look quickly, rather than writing custom styling for every element.

**Why we chose it.**
- React comes built into Next.js, so there is nothing extra to set up or integrate.
- Tailwind lets us hit Steve's "attractive" goal without a long design-build cycle, and keeps the look uniform across every screen.
- Both are extremely popular, which keeps hiring and support easy.

**Alternatives we passed on.**
- *Plain server-rendered pages (little interactivity):* simpler, but a dashboard feels dated without interactivity.
- *Ready-made component libraries:* faster at first, but they can make the product look like everyone else's.

**What it means for you.** Design changes (colors, spacing, branding) are fast and cheap. A brand refresh is a days-not-months job.

**How hard is it to change later?** *Moderate.* Restyling is easy; swapping out React would be a rebuild of the screens.

---

## ADR-4: Data is stored in SQLite

**The question.** Where does the product keep its information (agents, ailments, therapies, appointments)?

**Our decision.** **SQLite**, a database that lives in a single file on the server.

**In plain English.** Most databases are like a separate office building with its own staff that you must hire, set up and keep running. SQLite is like a very well-organized filing cabinet inside the same room as the application. There is nothing extra to run, install or pay for. For a product of this size, it is fast and dependable.

**Why we chose it.**
- No database server to set up or maintain, which means lower cost and fewer things that can break (Mary: *reliable*).
- Very quick to start. Developers, students and demo audiences can all run the full product on a laptop.
- It is one of the most widely deployed databases in the world, so it is mature and trustworthy.

**Alternatives we passed on.**
- *PostgreSQL:* the best choice for a large, busy, multi-server product. It needs a separate server and more setup, which is more than the clinic needs today.
- *In-memory only (no saved data):* data would vanish every time the app restarted. That is not acceptable for appointments.

**What it means for you.**
- Great for the current scope: a single clinic, a demo, a course.
- **Known limit:** SQLite is not built for many separate application servers writing at the same time. If AgentClinic ever grows to thousands of simultaneous users or needs to run across multiple machines, we would move to a larger database. This is a *planned upgrade path*, not a surprise. The choice of Drizzle (next decision) is part of what keeps that move manageable.

**How hard is it to change later?** *Moderate.* Moving data from one database to another is routine work, but it needs planning and testing.

---

## ADR-5: Talking to the database through Drizzle ORM

This is the term you asked about, so here it is in full.

**The question.** How does the application read from and write to the database?

**Our decision.** Use a helper called **Drizzle ORM**.

### What "ORM" means

Databases have their own language (SQL) that is different from TypeScript. An **ORM** (Object-Relational Mapper) is a translator that sits between the application and the database. Developers describe what they want in TypeScript, such as "get all appointments for this agent next week", and the translator turns it into the database's language.

*Everyday comparison:* think of a professional interpreter at a business meeting. You speak your language, the other side speaks theirs, and the interpreter makes sure nothing gets lost. Without one, you either learn the other language perfectly or risk misunderstandings.

### What "typed queries" means

A **query** is a question asked of the database ("which agents have a booking tomorrow?"). **Typed** means the spell-checker from ADR-1 also checks those questions.

*Example:* if someone tries to ask for an appointment's `ailmentName`, but the field is really called `ailment`, a typed query gets flagged immediately while the developer is typing. Without typing, that mistake only shows up when a real user hits that screen and gets an error.

**Benefit to you:** fewer bugs in data-heavy features such as booking, which is the heart of Susan's feature set.

### What "migrations" means

The shape of your data will change as the product grows. For example, Susan may later want to track an agent's "severity of ailment" on every appointment. That means adding a new column to the database.

A **migration** is a **saved, step-by-step change instruction** for the database, like a renovation work order. Each change is written down, numbered and replayable.

*Everyday comparison:* a building's renovation log. "Step 12: add a second bathroom." Anyone can see the history, and a new building site can follow the same steps to end up identical.

**Benefits to you:**
- **No lost or damaged data** when features change. Updates are planned, not improvised.
- **Everyone has the same database shape**: the developer's laptop, the test environment and production. This avoids the "it worked on my machine" problem.
- **A history of every change**, which is useful for audits and for understanding when and why something changed.

### Why we chose Drizzle specifically

- It is built for TypeScript from the ground up, which matches ADR-1.
- It is lightweight and does not hide what the database is actually doing, which makes problems easier to diagnose.
- It works with SQLite now and with larger databases later, which supports the upgrade path from ADR-4.

**Alternatives we passed on.**
- *Prisma:* very popular and friendly, but heavier and more of a "black box".
- *Writing raw database instructions by hand:* maximum control, but more mistakes and more work for every feature.

### How an ORM keeps a future move manageable (and where it does not)

Your understanding is on the right track, and it needs one honest qualification: **an ORM does not make a database move easy. It makes it smaller and safer.** Here is what it does and does not do.

*Comparison:* imagine moving offices. Without an ORM, the address of the old office is written on hundreds of sticky notes all over the building, and every one has to be found and rewritten. With an ORM, the address lives in one directory, and the sticky notes point to the directory.

**What the ORM gives us:**

1. **One place that describes the data.** Our data shape (agents, ailments, therapies, appointments) is written once, in one file called the *schema*. Every question we ask the database is built from that schema. Without an ORM, the database's language is scattered through the code as hand-written text in dozens of places, and every one of them would need finding and checking.
2. **Most of the application never "knows" which database it is using.** Booking logic, dashboards and rules ask the ORM for data. They do not care whether the answer comes from SQLite or a larger database. When we move, that code stays largely untouched.
3. **The compiler tells us what broke.** Because queries are typed (see above), when the schema changes, TypeScript points at every place that needs attention. The to-do list builds itself instead of being discovered by users.
4. **The migration history is already written down.** A new database can be built by replaying the saved migration steps, which avoids reconstructing its structure by hand.

**What the ORM does not do for us:**

- **The schema file still needs rewriting.** SQLite and larger databases (such as PostgreSQL) describe some things differently, such as dates, true/false values and automatic IDs. Drizzle uses a slightly different set of building blocks for each database, so the schema file must be converted. This is a small, focused job, but it is a job.
- **The existing data must be copied across.** The ORM does not move stored appointments from one place to another. That is a separate, one-time task (see "migration service" below).
- **Differences in behavior must be tested.** Two databases can answer the same question slightly differently, for instance how they sort text or handle two people writing at the same time. This is exactly what the test suite from ADR-6 is for.

**The bottom line:** without an ORM, a database move touches the whole codebase and is hard to size. With one, it is a contained project with a known to-do list (convert the schema, copy the data, re-run the tests). That is what "manageable" means here.

### What a "migration service" is

You have heard "migration" used in two senses, and "migration service" can also mean two different things. Here is how they fit together.

**Two kinds of migration:**

| | Schema migration | Data migration (database move) |
|---|---|---|
| **What changes** | The *shape* of the database | *Where* the database lives |
| **Example** | Add a "severity" column to appointments | Move from SQLite to PostgreSQL |
| **How often** | Frequently, with new features | Rarely, perhaps once in the product's life |
| **Comparison** | Adding shelves to the filing cabinet | Moving every file to a new building |
| **Who does it** | Drizzle's migration files (this is what ADR-5 describes) | A one-time copy job |

**"Migration service" can mean either of these:**

1. **A step in our own release process.** When a new version of the product goes live, an automatic step first runs any new schema migrations, then starts the app. You could call this a small in-house migration service. It is the *foreman* who makes sure the building work is done before the doors open. **This is what we will have from day one.** Drizzle provides a command for it, and we make it a part of every release. This avoids someone forgetting to update the database by hand.
2. **A managed service from a cloud provider.** Providers such as Amazon sell services (for example, *AWS Database Migration Service*) that copy a large, busy, live database from one place to another with little or no downtime. It is the *professional removals company*. It makes sense when a database is large, business-critical and cannot go offline. **We do not need this.** The clinic's database is small, and a one-time copy using a simple script or a standard free tool can do the job during a short maintenance window.

**How this plays out for AgentClinic:**

- **Now:** each release runs schema migrations automatically. This is lightweight and part of ADR-5.
- **If we outgrow SQLite:** a short, planned project: convert the schema, copy the data once, run the tests, switch over. No paid migration service is needed at this scale.
- **Only if the product becomes large and cannot tolerate any downtime** would a managed migration service become worth its cost.

**What it means for you.** Slightly more setup at the start, in return for safer, faster feature work afterwards, especially when a feature changes what we store. If a stakeholder asks for a new field mid-project, the answer is "yes, that is a routine migration", not "that is risky".

**How hard is it to change later?** *Moderate.* The data itself is unaffected; the translator layer would need to be rewritten.

---

## ADR-6: Quality and safety tools

This section explains each tool you asked about. None of these tools is a feature users see. They exist so that **what users see is correct, consistent and reliable**.

*Overall comparison:* think of publishing a newsletter. You have a proofreader (catches mistakes), a style editor (makes it look consistent), a fact-checker (confirms each piece works) and a final read-through as a customer would experience it (confirms the whole thing works together). That is what these tools do for code.

### 6a. ESLint: the proofreader

- **What it does:** automatically reads the code and flags likely mistakes and risky patterns, such as a variable that is never used or a common bug pattern.
- **Comparison:** spell-check and grammar-check for code.
- **Why it matters:** catches problems in seconds, before any person has to review them or any user can hit them.

### 6b. Prettier: the style editor

- **What it does:** automatically formats all code the same way (indentation, spacing, line breaks).
- **Comparison:** a style guide enforced automatically, so every document in the company looks like it came from the same author.
- **Why it matters:** reviews focus on what the code does instead of how it looks, and new contributors can read any file easily. It saves time and removes pointless arguments.

### 6c. Vitest: the unit tests (the fact-checker)

- **What a "unit test" is:** a small automatic check of one small piece of logic in isolation.
- **Example:** "If an agent already has an appointment at 10:00, can a second one be booked at 10:00? Expected answer: no." The test runs that rule and confirms the answer.
- **Comparison:** testing each ingredient before it goes into the recipe, or checking that each calculator button works on its own.
- **Speed:** very fast. Hundreds can run in seconds.
- **Why it matters:** every business rule Susan defines can have a matching test. Once written, that test runs **every time** anyone changes anything, so old features do not silently break when new ones are added. This is called *catching regressions*.

### 6d. Playwright: the end-to-end tests (the customer read-through)

- **What an "end-to-end test" is:** an automatic robot that opens a real browser, clicks around the real application the way a user would, and checks that the result is correct.
- **Example:** "Open the site, log in as an agent, choose a therapy, pick a time, press Book, and confirm the appointment shows on the dashboard."
- **Comparison:** a mystery shopper who walks through your complete customer journey and reports whether it worked.
- **Speed:** slower than unit tests, so we use them for the important journeys rather than every detail.
- **Why it matters:**
  - It confirms that the whole system works **together**, not only the individual parts. Every part can pass its own test and the whole experience can still be broken.
  - It can run the same journey in several browsers, such as Chrome, Edge, Firefox and Safari. This directly supports Steve's goal of working well in modern browsers.

### Unit tests and end-to-end tests: how they differ

| | Unit tests (Vitest) | End-to-end tests (Playwright) |
|---|---|---|
| **What is checked** | One small rule or calculation | A complete user journey |
| **Analogy** | Testing each ingredient | Tasting the finished dish |
| **Speed** | Very fast (seconds) | Slower (minutes) |
| **Finds** | Logic mistakes | Broken connections between parts, screen problems |
| **How many** | Many | Fewer, focused on key journeys |

Both are needed. Unit tests tell us *where* a problem is. End-to-end tests tell us *whether the customer would feel it*.

### Alternatives we considered for each tool

Each tool below has real competitors. These are the realistic options and why we did not pick them.

**Proofreader (ESLint)**

| Alternative | What it is | Why we passed |
|---|---|---|
| **Biome** | One newer tool that does both proofreading and formatting, and is very fast | Younger, with fewer rules and plug-ins. ESLint is the industry standard and Next.js works with it out of the box |
| **oxlint** | A very fast newer proofreader | Same reason: newer and narrower. Could be added later purely for speed |
| **No proofreader (the compiler only)** | Rely on TypeScript's own checks | Catches type mistakes, but not risky patterns or bad habits |

**Style editor (Prettier)**

| Alternative | What it is | Why we passed |
|---|---|---|
| **Biome** | Formats as well as proofreads | We would be betting on one newer tool for two jobs. It is a sensible future simplification |
| **dprint** | A fast formatter | Smaller community and fewer editor integrations |
| **Editor settings only** | Rely on each developer's setup | Everyone's code ends up looking slightly different, and reviews get noisy |

**Unit tests (Vitest)**

| Alternative | What it is | Why we passed |
|---|---|---|
| **Jest** | The long-time standard test tool | Very popular, but needs more configuration with modern TypeScript and is slower. Vitest uses a nearly identical style, so skills transfer |
| **Node's built-in test runner** | Comes with Node.js, nothing to install | Fewer features and a thinner ecosystem. A fine choice for tiny projects |
| **Mocha** | An older, flexible test tool | Needs several add-ons to match what Vitest does out of the box |

**End-to-end tests (Playwright)**

| Alternative | What it is | Why we passed |
|---|---|---|
| **Cypress** | A friendly, visual testing tool | Excellent developer experience, but weaker for testing several browsers (Safari in particular) and several tabs at once, which matters for Steve's browser goal |
| **Selenium** | The original browser-automation tool | Most flexible and widest in support, but slower to set up and more fragile to maintain |
| **Puppeteer** | Browser robot from Google | Mainly Chrome-focused, so less coverage across browsers |

**What it means for you.**
- Releases are safer, and fewer bugs reach users.
- You can ask for a test for any rule in your requirements, and "it has a passing test" becomes a clear, shared definition of "done".
- There is a small upfront time cost per feature (writing the tests), which is repaid by fewer bug-fix and rework cycles.

**How hard is it to change later?** *Easy.* These are supporting tools. They can be swapped or removed without changing what the product does.

---

## ADR-7: Browser support is current modern browsers

**The question.** Which web browsers must the site work in?

**Our decision.** The current versions of Chrome, Edge, Firefox and Safari. We will not support very old browsers.

**In plain English.** Supporting old browsers is like requiring every restaurant dish to also be cookable on a 1990s stove. It is possible, but it slows everything down and limits what we can offer. Modern browsers update automatically, so nearly all real users already have a recent one.

**Why we chose it.** It matches Steve's request for a site that works well in modern browsers, and it lets us use modern, attractive design features without workarounds.

**What it means for you.** Faster delivery and a better-looking product. A very small number of users on out-of-date browsers may see a degraded experience. If your analytics show that group matters, tell us early. ADR-8 below is how we will find out.

**How hard is it to change later?** *Easy to loosen or tighten,* though supporting older browsers costs extra work.

---

## ADR-8: Measuring site usage with a lightweight analytics tool

**The question.** How will we know how people actually use the site, including which browsers they use, so the decision in ADR-7 is based on evidence instead of guesses?

**What we want to measure.**

| Measure | What it tells you |
|---|---|
| **Time on page** | Whether agents are reading and engaging, or leaving quickly |
| **Bounce rate** | The share of visitors who arrive, look at one page and leave. A high rate on a page can mean it is confusing or not what people expected |
| **Browser usage** | Which browsers (and versions) visitors use. This answers the ADR-7 question directly |

**Our decision (quick implementation).** Add a lightweight, privacy-friendly analytics service, with **Plausible Analytics** as our recommendation.

**In plain English.** Analytics is a counter at the clinic's front door. It does not follow anyone around. It simply notes that someone arrived, which page they came to, how long they stayed, whether they went on to a second page, and what browser they came with. Adding it means pasting **one small line of code** into the site. There is nothing to build and nothing to maintain, and the numbers appear on a ready-made dashboard.

**Why we chose it.**
- **Fast:** a few minutes to add, and it fits our roadmap's "very small phases".
- **Covers all three measures** (time on page, bounce rate, browser usage) out of the box.
- **Privacy-friendly:** it does not use tracking cookies, so in many cases we avoid the cookie-consent pop-up that most analytics tools require. (Legal and privacy requirements vary by region, so confirm with whoever advises on compliance.)
- **Simple:** one page of numbers that a non-technical person can read, which suits both you and Steve.
- **Cost:** it is a paid hosted service after a trial period. Check current pricing when we set it up.

**Alternatives we considered.**

| Alternative | What it is | Why we passed (for now) |
|---|---|---|
| **Google Analytics** | Free, extremely powerful, the best-known option | Free in price, but heavy: it uses tracking cookies, so it usually needs a consent pop-up, and it is complex to learn. Overkill for our questions |
| **Umami** | Open source and simple, similar to Plausible; has a hosted version and can be self-hosted | A strong runner-up. Comparable features. Choose it if you prefer a free or self-run option |
| **PostHog** | A deeper "product analytics" platform (funnels, session replay, experiments) | Very capable, but much more than three simple measures need. A good long-term candidate |
| **Vercel Analytics** (or a similar host's built-in tool) | Built into some hosting platforms | Convenient, but only an option if we host there, and more limited in what it reports |
| **Build our own** | Count visits ourselves in our database | Reinvents a solved problem and takes real engineering time |

**What it means for you.**
- Within days of launch you can answer: "Which browsers do our visitors use, and do any of the older ones matter?"
- You get a baseline for time on page and bounce rate, so you can see whether changes (a new design from Steve, a new feature from Susan) actually improve things.
- A caution on reading the numbers: bounce rate and time on page describe behavior but not *reasons*. A high bounce rate on a page that gives a quick answer can be perfectly healthy. Treat these as prompts for questions, not verdicts.

### Long-term changes to expect

The quick option answers "what is happening". As the product matures, we will want "why is it happening, and did our change help". Plan for these steps, roughly in this order:

1. **Define the key journeys.** Agree which actions matter most (for example, "agent books an appointment") and track them as named *events*, not just page visits. This is the biggest upgrade in value.
2. **Track funnels.** See where people drop out step by step: choose an ailment, choose a therapy, pick a time, confirm. This directly serves Susan's booking features.
3. **Consider moving to a deeper tool** such as PostHog if we want funnels, session replays and experiments (A/B tests) in one place. Because we start with a one-line install, switching later is a small job.
4. **Review privacy and consent** as the product reaches new regions, particularly if we adopt a tool that uses cookies or collects personal information.
5. **Decide on hosting the data ourselves** if we need full control over where visitor data lives (for example, for compliance or customer requirements).
6. **Connect analytics to what we already know.** For logged-in staff and agents, link behavior to anonymized clinic data to answer questions like "which therapies lead to repeat bookings".

**How hard is it to change later?** *Easy.* Removing or replacing the tool is as simple as changing that one line of code. The only thing we lose when we switch is the history stored in the old tool, so export it if it matters.

---

## Risks and things to watch

| Risk | Plain-English explanation | How we handle it |
|---|---|---|
| Growth beyond SQLite | If AgentClinic becomes very busy or runs across many servers, the filing cabinet becomes too small | Planned move to a larger database; Drizzle keeps this manageable |
| Framework upgrades | Next.js releases new major versions, and upgrading takes engineering time | Schedule periodic upgrade work in the roadmap |
| Tool sprawl | Many supporting tools means more to keep updated | We chose well-known, widely supported tools |
| Misreading analytics | Bounce rate and time on page show behavior, not reasons | Treat them as questions to investigate, and add event tracking (ADR-8) for key journeys |

## How decisions map to stakeholder needs

| Stakeholder need | Decisions that serve it |
|---|---|
| **Mary (engineering):** reliable site, popular TypeScript stack, dashboard | ADR-1, 2, 4, 5, 6 |
| **Susan (product):** agents, ailments, therapies, appointment booking | ADR-4, 5, 6 (safe data and well-tested booking rules) |
| **Steve (marketing):** attractive, works in modern browsers | ADR-3, 6d, 7, 8 (evidence for which browsers matter) |
| **Product management:** evidence to guide priorities | ADR-8 (time on page, bounce rate, browser usage) |

## Questions to bring to engineering

If you want to go deeper, these are good questions to ask:

1. "At what number of users or amount of data would we outgrow SQLite?"
2. "Which of our key user journeys will have end-to-end tests?"
3. "What is our plan and cadence for framework upgrades?"
4. "How will we handle a data change that Susan's team requests mid-project?" (The answer should mention migrations.)

## Glossary

| Term | Meaning |
|---|---|
| **Framework** | A ready-made foundation that handles common web-app work so we do not build it from scratch |
| **Frontend / backend** | The screens users see / the behind-the-scenes processing and storage |
| **Database** | Where the product's information is stored permanently |
| **ORM** | A translator between the application's language and the database's language |
| **Query** | A question asked of the database |
| **Typed** | Checked automatically for the right kind of data, catching mistakes early |
| **Migration** | A saved, replayable instruction for changing the database's structure safely |
| **Unit test** | An automatic check of one small rule in isolation |
| **End-to-end test** | An automatic check of a whole user journey in a real browser |
| **Regression** | Something that used to work and then broke after an unrelated change |
| **ADR** | Architecture Decision Record, a short write-up of a technical choice and the reasons for it |
| **Schema** | The written description of what data the database holds and how it is organized |
| **Schema migration** | A migration that changes the shape of the database (for example, adding a column) |
| **Data migration** | Moving the stored data from one database or location to another |
| **Migration service** | Either a step in our release process that applies pending migrations automatically, or a paid cloud service that copies large databases between systems |
| **Analytics** | Counting and describing how people use the site |
| **Bounce rate** | The share of visitors who view only one page and then leave |
| **Time on page** | How long visitors stay on a page before moving on or leaving |
| **Event** | A named action (such as "booked an appointment") that analytics records |
| **Funnel** | The ordered steps toward a goal, showing where people drop out |
| **Linter / formatter** | A proofreader for code (ESLint) / an automatic style editor for code (Prettier) |
