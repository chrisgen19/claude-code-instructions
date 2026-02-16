# Global CLAUDE.md — Personal Development Preferences (Christian)

## About Me
- Full-stack web developer based in the Philippines
- I manage multiple client projects simultaneously
- WordPress: custom theme development, plugin development, WooCommerce customizations, and client site management
- Non-WordPress projects: built with Next.js, TypeScript, Prisma, and PostgreSQL
- Always confirm which project/environment we're working on if context is unclear

## Compact Instructions
When compacting this conversation, always preserve:
- Which project/client we're working on
- The current file(s) being edited
- Any decisions already made (tech choices, approach agreed on)
- Errors we're actively debugging
- The current git branch
- Environment context (staging vs production, hosting platform)

## Communication Style
- Be concise and direct — skip lengthy explanations unless I ask for them
- When I ask "how to do X", give me the code/commands first, then explain after
- When a task is ambiguous:
  - Ask clarifying questions if needed
  - If I'm unavailable, propose 1–2 options with tradeoffs, choose the safest default, and clearly state assumptions
- If something could break production, warn me clearly before proceeding and suggest a safer path
- When suggesting changes to existing code, show full context around the change (file path + surrounding lines)
- Use comments in code to explain non-obvious logic (comment the "why", not the "what")
- If a command fails, show me the error and suggest a fix — don't silently retry a different approach

## Response Format (Default)
For non-trivial tasks, structure your response as:
1. **Plan** (1–3 bullets)
2. **Code / Commands** (first)
3. **Notes / Risks** (including how to revert if something goes wrong)
4. **Suggested Tests / Verification**

## General Coding Standards

### Language Preferences
- TypeScript over JavaScript (always, unless a project is JS-only)
- Prefer named exports over default exports **except where frameworks require default exports**
  - e.g. Next.js `page.tsx`, `layout.tsx`, certain config files
- Components:
  - Prefer `function` declarations for React components
  - Prefer arrow functions for utilities/helpers
  - If a project already uses a different convention consistently, match the existing style
- Use `const` by default, `let` only when reassignment is needed, never `var`
- Always add proper types — prefer `unknown` over `any` when the type is uncertain, and avoid `any` unless absolutely necessary with a `// TODO: type this properly` comment

### Naming Conventions
- Files: kebab-case (`contact-form.tsx`, `utm-tracker.ts`)
- Components: PascalCase (`ContactForm`, `ServiceCard`)
- Functions/variables: camelCase (`handleSubmit`, `isLoading`)
- Constants: UPPER_SNAKE_CASE (`API_BASE_URL`, `MAX_RETRIES`)
- CSS: Tailwind utilities (avoid custom CSS unless unavoidable)
- Database columns: snake_case (`created_at`, `user_id`)

### Code Quality
- Keep functions under ~50 lines — extract helpers if longer
- Keep components under ~150 lines — extract sub-components if longer
- No `console.log` in committed code — use proper error handling or a logger
- Always handle loading, error, and empty states in UI components
- Add JSDoc comments for functions that aren't self-explanatory
- Prefer backward-compatible, non-breaking changes by default
- When modifying production-related code, suggest how to revert if something goes wrong

## Tooling Defaults (Use Existing Project Config First)
- Node.js: prefer 20+ unless constrained by hosting/project
- Package manager: `pnpm` for Node projects unless project uses something else
- Lock file: `pnpm-lock.yaml` (never delete or ignore this)
- Common commands: `pnpm add`, `pnpm dev`, `pnpm build`
- Lint/format: follow existing ESLint/Prettier configs; don't introduce new tooling unless needed
- Testing: follow existing test runner (Vitest/Jest/Playwright/Cypress). If none exists, suggest minimal tests for critical logic

## Git Conventions

### Commit Messages
Use conventional commits format:
```
type(scope): short description

feat(contact-form): add UTM tracking to form submissions
fix(woocommerce): resolve price display issue on variable products
chore(deps): update next.js to 14.2.0
refactor(api): extract validation logic into shared utility
```

### Branch Naming
```
feature/short-description
bugfix/short-description
hotfix/short-description
```

### Workflow
- Always check current branch before making changes
- Never commit directly to `main` or `master`
- Run lint and type-check before committing
- If the project has tests, run them before pushing
- When making significant changes, suggest a PR description summarizing what changed and why

## Tech Stack Preferences

### Frontend
- **Framework:** Next.js (App Router preferred) or React with Vite
- **Styling:** Tailwind CSS — use utility classes directly, avoid `@apply` in most cases
- **Forms:** React Hook Form + Zod for validation
- **Icons:** Lucide React
- **State:** React state/context for simple cases, Zustand for complex state
- **Animation:** Framer Motion when needed

### WordPress / PHP
- I build **custom themes** and **custom plugins** — not just site configuration
- Follow WordPress Coding Standards for PHP
- Use modern PHP (7.4+ features: typed properties, arrow functions, null coalescing)
- **Theme Development:**
  - Use a clean starter theme or build from scratch depending on project needs
  - Follow WordPress template hierarchy (`single.php`, `archive.php`, `page-{slug}.php`, etc.)
  - Use `get_template_part()` for reusable components
  - Keep `functions.php` organized — split into includes for hooks, CPTs, taxonomies, enqueues, etc.
- **Plugin Development:**
  - Use OOP structure with proper namespacing when complexity warrants it
  - Use a unique prefix per client (e.g., `ag_` for Access Group, `mmg_` for Magnetite)
  - Register activation/deactivation/uninstall hooks properly
  - Use WordPress hooks and filters — never modify core or third-party plugin files directly
- Prefer WP REST API for custom integrations
- Use ACF Pro for custom fields — register field groups in code when possible
- Contact Form 7 for forms — use `wpcf7_before_send_mail` hook for custom processing
- Always sanitize and escape: `sanitize_text_field()`, `esc_html()`, `wp_nonce_field()`
- Use `$wpdb->prepare()` for all database queries — never concatenate raw SQL
- Enqueue scripts and styles properly with `wp_enqueue_script()` / `wp_enqueue_style()`
- For WooCommerce customizations, prefer hooks over template overrides when possible
- Always test with common plugins (Elementor, WooCommerce, ACF) to avoid conflicts

### Backend / Database
- **Node.js:** Next.js API routes (primary), Express when needed
- **PHP:** WordPress REST API or custom endpoints
- **Database:** MySQL for WordPress, PostgreSQL for Next.js/Node projects
- **ORM:** Prisma (primary for Next.js projects), Drizzle ORM as alternative
- Always use migrations for schema changes — never modify the database manually in production
- Validate external data (API responses, webhooks) with Zod schemas at the boundary

### Deployment & Hosting
- **Production:** Vercel (Next.js), traditional cPanel/VPS (WordPress)
- **Staging:** Coolify (self-hosted) or Vercel preview deployments
- **CI/CD:** GitHub Actions or Coolify auto-deploy via webhooks
- **Containers:** Docker when using Coolify/Dokploy
- Before deploying, always double-check the target environment (staging vs production)

## Development Workflow

### Before Writing Code
1. Understand the requirements — ask clarifying questions if the task is ambiguous
2. Check existing code patterns in the project before introducing new ones
3. Plan the approach briefly if it's a complex feature

### While Writing Code
1. Follow existing project patterns and conventions
2. Write code incrementally — don't try to build everything at once
3. Handle edge cases (null checks, empty arrays, network errors)
4. Add TypeScript types as you go, not as an afterthought

### After Writing Code
1. Review for any hardcoded values that should be env variables
2. Check for security issues (XSS, SQL injection, exposed secrets)
3. Make sure the code works on mobile (responsive)
4. Suggest tests if the logic is complex or critical
5. **For TypeScript / React / Next.js projects:** Always run lint and type-check before considering the task done:
   ```bash
   pnpm lint
   pnpm type-check   # or: pnpm tsc --noEmit
   ```
   - Fix any errors or warnings before moving on
   - If the project has a `build` command, run `pnpm build` to catch build-time issues too
6. Summarize what changed and any follow-up tasks remaining
7. **When code changes were made**, end with a ready-to-use `git commit` command describing what was added or changed:
   ```bash
   git add .
   git commit -m "feat(contact-form): add email validation and UTM tracking to form submissions"
   ```
   - Use conventional commit format (`feat`, `fix`, `refactor`, `chore`, `docs`, `style`, `perf`)
   - If multiple files/features were changed, use a multi-line commit:
   ```bash
   git add .
   git commit -m "feat(checkout): add discount code validation" -m "- Add Zod schema for discount codes
   - Integrate validation with WooCommerce coupon API
   - Display inline error messages on invalid codes"
   ```

## Security Reminders
- Never hardcode API keys, passwords, or secrets in code
- Use `.env` files locally, environment variables in production
- Always validate and sanitize user input (both client and server side)
- Use HTTPS everywhere
- Add CSRF protection (nonces in WordPress, CSRF tokens in Next.js)
- Be careful with file uploads — validate file types and sizes
- Flag any potential security issues immediately, even if I didn't ask

## Things I Don't Want
- Don't add unnecessary dependencies — check if native APIs or existing packages can do the job
- Don't over-engineer simple features with complex design patterns
- Don't create separate CSS/SCSS files when Tailwind can handle it
- Don't use `index.ts` barrel files unless the project already uses them
- Don't introduce new linting rules, formatters, or tooling without asking first
- Don't assume the project structure — check first, then follow existing conventions
- Don't make changes across multiple unrelated files in one go without explaining the scope
- Don't introduce packages that aren't already in the project without asking first
- If suggesting a new dependency, verify it exists and link to its npm/packagist page
- NEVER use `2>/dev/null` or `2>&1` in Bash commands — this is a known tokenization bug that causes errors