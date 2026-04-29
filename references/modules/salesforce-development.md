## Module: Salesforce Development
---
name: salesforce-development
description: "Expert patterns for Salesforce platform development including Lightning Web Components (LWC), Apex triggers and classes, REST/Bulk APIs, Connected Apps, and Salesforce DX with scratch orgs and 2nd ..."
risk: unknown
source: "vibeship-spawner-skills (Apache 2.0)"
date_added: "2026-02-27"
---

# Salesforce Development

## Patterns

### Lightning Web Component with Wire Service

Use @wire decorator for reactive data binding with Lightning Data Service
or Apex methods. @wire fits LWC's reactive architecture and enables
Salesforce performance optimizations.


### Bulkified Apex Trigger with Handler Pattern

Apex triggers must be bulkified to handle 200+ records per transaction.
Use handler pattern for separation of concerns, testability, and
recursion prevention.


### Queueable Apex for Async Processing

Use Queueable Apex for async processing with support for non-primitive
types, monitoring via AsyncApexJob, and job chaining. Limit: 50 jobs
per transaction, 1 child job when chaining.


## Anti-Patterns

### ❌ SOQL Inside Loops

### ❌ DML Inside Loops

### ❌ Hardcoding IDs

## ⚠️ Sharp Edges

| Issue | Severity | Solution |
|-------|----------|----------|
| Issue | critical | See docs |
| Issue | high | See docs |
| Issue | medium | See docs |
| Issue | high | See docs |
| Issue | critical | See docs |
| Issue | high | See docs |
| Issue | high | See docs |
| Issue | critical | See docs |

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

---

## Imported Reference

---
name: convex
description: "Convex reactive backend expert: schema design, TypeScript functions, real-time subscriptions, auth, file storage, scheduling, and deployment."
risk: safe
source: "https://docs.convex.dev"
date_added: "2026-02-27"
---

# Convex

You are an expert in Convex — the open-source, reactive backend platform where queries are TypeScript code. You have deep knowledge of schema design, function authoring (queries, mutations, actions), real-time data subscriptions, authentication, file storage, scheduling, and deployment workflows across React, Next.js, Angular, Vue, Svelte, React Native, and server-side environments.

## When to Use

- Use when building a new project with Convex as the backend
- Use when adding Convex to an existing React, Next.js, Angular, Vue, Svelte, or React Native app
- Use when designing schemas for a Convex document-relational database
- Use when writing or debugging Convex functions (queries, mutations, actions)
- Use when implementing real-time/reactive data patterns
- Use when setting up authentication with Convex Auth or third-party providers (Clerk, Auth0, etc.)
- Use when working with Convex file storage, scheduled functions, or cron jobs
- Use when deploying or managing Convex projects

## Core Concepts

Convex is a **document-relational** database with a fully managed backend. Key differentiators:

- **Reactive by default**: Queries automatically re-run and push updates to all connected clients when underlying data changes
- **TypeScript-first**: All backend logic — queries, mutations, actions, schemas — is written in TypeScript
- **ACID transactions**: Serializable isolation with optimistic concurrency control
- **No infrastructure to manage**: Serverless, scales automatically, zero config
- **End-to-end type safety**: Types flow from schema → backend functions → client hooks

### Function Types

| Type            | Purpose                   | Can Read DB    | Can Write DB      | Can Call External APIs | Cached/Reactive |
| :-------------- | :------------------------ | :------------- | :---------------- | :--------------------- | :-------------- |
| **Query**       | Read data                 | ✅             | ❌                | ❌                     | ✅              |
| **Mutation**    | Write data                | ✅             | ✅                | ❌                     | ❌              |
| **Action**      | Side effects              | via `runQuery` | via `runMutation` | ✅                     | ❌              |
| **HTTP Action** | Webhooks/custom endpoints | via `runQuery` | via `runMutation` | ✅                     | ❌              |

## Project Setup

### New Project (Next.js)

```bash
npx create-next-app@latest my-app
cd my-app && npm install convex
npx convex dev
```

### Add to Existing Project

```bash
npm install convex
npx convex dev
```

The `npx convex dev` command:

1. Prompts you to log in (GitHub)
2. Creates a project and deployment
3. Generates `convex/` folder for backend functions
4. Syncs functions to your dev deployment in real-time
5. Creates `.env.local` with `CONVEX_DEPLOYMENT` and `NEXT_PUBLIC_CONVEX_URL`

### Folder Structure

```
my-app/
├── convex/
│   ├── _generated/        ← Auto-generated (DO NOT EDIT)
│   │   ├── api.d.ts
│   │   ├── dataModel.d.ts
│   │   └── server.d.ts
│   ├── schema.ts          ← Database schema definition
│   ├── tasks.ts           ← Query/mutation functions
│   └── http.ts            ← HTTP actions (optional)
├── .env.local             ← CONVEX_DEPLOYMENT, NEXT_PUBLIC_CONVEX_URL
└── convex.json            ← Project config (optional)
```

## Schema Design

Define your schema in `convex/schema.ts` using the validator library:

```typescript
import { defineSchema, defineTable } from "convex/server";
import { v } from "convex/values";

export default defineSchema({
  users: defineTable({
    name: v.string(),
    email: v.string(),
    avatarUrl: v.optional(v.string()),
    tokenIdentifier: v.string(),
  })
    .index("by_token", ["tokenIdentifier"])
    .index("by_email", ["email"]),

  messages: defineTable({
    authorId: v.id("users"),
    channelId: v.id("channels"),
    body: v.string(),
    attachmentId: v.optional(v.id("_storage")),
  })
    .index("by_channel", ["channelId"])
    .searchIndex("search_body", { searchField: "body" }),

  channels: defineTable({
    name: v.string(),
    description: v.optional(v.string()),
    isPrivate: v.boolean(),
  }),
});
```

### Validator Types

| Validator                         | TypeScript Type       | Notes                                          |
| :-------------------------------- | :-------------------- | :--------------------------------------------- |
| `v.string()`                      | `string`              |                                                |
| `v.number()`                      | `number`              | IEEE 754 float                                 |
| `v.bigint()`                      | `bigint`              |                                                |
| `v.boolean()`                     | `boolean`             |                                                |
| `v.null()`                        | `null`                |                                                |
| `v.id("tableName")`               | `Id<"tableName">`     | Document reference                             |
| `v.array(v.string())`             | `string[]`            |                                                |
| `v.object({...})`                 | `{...}`               | Nested objects                                 |
| `v.optional(v.string())`          | `string \| undefined` |                                                |
| `v.union(v.string(), v.number())` | `string \| number`    |                                                |
| `v.literal("active")`             | `"active"`            | Literal types                                  |
| `v.bytes()`                       | `ArrayBuffer`         | Binary data                                    |
| `v.float64()`                     | `number`              | Explicit 64-bit float (used in vector indexes) |
| `v.any()`                         | `any`                 | Escape hatch                                   |

### Indexes

```typescript
// Single-field index
defineTable({ email: v.string() }).index("by_email", ["email"]);

// Compound index (order matters for range queries)
defineTable({
  orgId: v.string(),
  createdAt: v.number(),
}).index("by_org_and_date", ["orgId", "createdAt"]);

// Full-text search index
defineTable({ body: v.string(), channelId: v.id("channels") }).searchIndex(
  "search_body",
  {
    searchField: "body",
    filterFields: ["channelId"],
  },
);

// Vector search index (for AI/embeddings)
defineTable({ embedding: v.array(v.float64()), text: v.string() }).vectorIndex(
  "by_embedding",
  {
    vectorField: "embedding",
    dimensions: 1536,
  },
);
```

## Writing Functions

### Queries (Read Data)

Queries are reactive — clients automatically get updates when data changes.

````typescript
import { query } from "./_generated/server";
import { v } from "convex/values";

// Simple query — list all tasks
export const list = query({
  args: {},
  handler: async (ctx) => {
    return await ctx.db.query("tasks").collect();
  },
});

// Query with arguments and filtering
export const getByChannel = query({
  args: { channelId: v.id("channels") },
  handler: async (ctx, args) => {
    return await ctx.db
      .query("messages")
      .withIndex("by_channel", (q) => q.eq("channelId", args.channelId))
      .order("desc")
      .take(50);
  },
});

// Query with auth check
export const getMyProfile = query({
  args: {},
  handler: async (ctx) => {
    const identity = await ctx.auth.getUserIdentity();
    if (!identity) return null;

    return await ctx.db
      .query("users")
      .withIndex("by_token", (q) =>
        q.eq("tokenIdentifier", identity.tokenIdentifier),
      )
      .unique();
  },
});

### Paginated Queries

Use cursor-based pagination for lists or infinite scroll UIs.

```typescript
import { query } from "./_generated/server";
import { paginationOptsValidator } from "convex/server";

export const listPaginated = query({
  args: {
    paginationOpts: paginationOptsValidator
  },
  handler: async (ctx, args) => {
    return await ctx.db
      .query("messages")
      .order("desc")
      .paginate(args.paginationOpts);
  },
});
```

### Mutations (Write Data)

Mutations run as ACID transactions with serializable isolation.

```typescript
import { mutation } from "./_generated/server";
import { v } from "convex/values";

// Insert a document
export const create = mutation({
  args: { text: v.string(), isCompleted: v.boolean() },
  handler: async (ctx, args) => {
    const taskId = await ctx.db.insert("tasks", {
      text: args.text,
      isCompleted: args.isCompleted,
    });
    return taskId;
  },
});

// Update a document
export const update = mutation({
  args: { id: v.id("tasks"), isCompleted: v.boolean() },
  handler: async (ctx, args) => {
    await ctx.db.patch(args.id, { isCompleted: args.isCompleted });
  },
});

// Delete a document
export const remove = mutation({
  args: { id: v.id("tasks") },
  handler: async (ctx, args) => {
    await ctx.db.delete(args.id);
  },
});

// Multi-document transaction (automatically atomic)
export const transferCredits = mutation({
  args: {
    fromUserId: v.id("users"),
    toUserId: v.id("users"),
    amount: v.number(),
  },
  handler: async (ctx, args) => {
    const fromUser = await ctx.db.get(args.fromUserId);
    const toUser = await ctx.db.get(args.toUserId);
    if (!fromUser || !toUser) throw new Error("User not found");
    if (fromUser.credits < args.amount) throw new Error("Insufficient credits");

    await ctx.db.patch(args.fromUserId, {
      credits: fromUser.credits - args.amount,
    });
    await ctx.db.patch(args.toUserId, {
      credits: toUser.credits + args.amount,
    });
  },
});
````

### Actions (External APIs & Side Effects)

Actions can call third-party services but cannot directly access the database — they must use `ctx.runQuery` and `ctx.runMutation`.

```typescript
import { action } from "./_generated/server";
import { v } from "convex/values";
import { api } from "./_generated/api";

export const sendEmail = action({
  args: { to: v.string(), subject: v.string(), body: v.string() },
  handler: async (ctx, args) => {
    // Call external API
    const response = await fetch("https://api.sendgrid.com/v3/mail/send", {
      method: "POST",
      headers: {
        Authorization: `Bearer ${process.env.SENDGRID_API_KEY}`,
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        personalizations: [{ to: [{ email: args.to }] }],
        from: { email: "noreply@example.com" },
        subject: args.subject,
        content: [{ type: "text/plain", value: args.body }],
      }),
    });

    if (!response.ok) throw new Error("Failed to send email");

    // Write result back to database via mutation
    await ctx.runMutation(api.emails.recordSent, {
      to: args.to,
      subject: args.subject,
      sentAt: Date.now(),
    });
  },
});

// Generate AI embeddings
export const generateEmbedding = action({
  args: { text: v.string(), documentId: v.id("documents") },
  handler: async (ctx, args) => {
    const response = await fetch("https://api.openai.com/v1/embeddings", {
      method: "POST",
      headers: {
        Authorization: `Bearer ${process.env.OPENAI_API_KEY}`,
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        model: "text-embedding-3-small",
        input: args.text,
      }),
    });

    const { data } = await response.json();
    await ctx.runMutation(api.documents.saveEmbedding, {
      documentId: args.documentId,
      embedding: data[0].embedding,
    });
  },
});
```

### HTTP Actions (Webhooks)

```typescript
import { httpRouter } from "convex/server";
import { httpAction } from "./_generated/server";
import { api } from "./_generated/api";

const http = httpRouter();

http.route({
  path: "/webhooks/stripe",
  method: "POST",
  handler: httpAction(async (ctx, request) => {
    const body = await request.text();
    const signature = request.headers.get("stripe-signature");

    // Verify webhook signature here...

    const event = JSON.parse(body);
    await ctx.runMutation(api.payments.handleWebhook, { event });

    return new Response("OK", { status: 200 });
  }),
});

export default http;
```

## Client-Side Integration

### React / Next.js

```typescript
// app/ConvexClientProvider.tsx
"use client";
import { ConvexProvider, ConvexReactClient } from "convex/react";
import { ReactNode } from "react";

const convex = new ConvexReactClient(process.env.NEXT_PUBLIC_CONVEX_URL!);

export function ConvexClientProvider({ children }: { children: ReactNode }) {
  return <ConvexProvider client={convex}>{children}</ConvexProvider>;
}
```

```typescript
// app/layout.tsx — wrap children
import { ConvexClientProvider } from "./ConvexClientProvider";

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>
        <ConvexClientProvider>{children}</ConvexClientProvider>
      </body>
    </html>
  );
}
```

```typescript
// Component using Convex hooks
"use client";
import { useQuery, useMutation } from "convex/react";
import { api } from "@/convex/_generated/api";

export function TaskList() {
  // Reactive query — auto-updates when data changes
  const tasks = useQuery(api.tasks.list);
  const addTask = useMutation(api.tasks.create);
  const toggleTask = useMutation(api.tasks.update);

  if (tasks === undefined) return <p>Loading...</p>;

  return (
    <div>
      {tasks.map((task) => (
        <div key={task._id}>
          <input
            type="checkbox"
            checked={task.isCompleted}
            onChange={() =>
              toggleTask({ id: task._id, isCompleted: !task.isCompleted })
            }
          />
          {task.text}
        </div>
      ))}
      <button onClick={() => addTask({ text: "New task", isCompleted: false })}>
        Add Task
      </button>
    </div>
  );
}
```

```typescript
// Component using Paginated Queries
"use client";
import { usePaginatedQuery } from "convex/react";
import { api } from "@/convex/_generated/api";

export function MessageLog() {
  const { results, status, loadMore } = usePaginatedQuery(
    api.messages.listPaginated,
    {}, // args
    { initialNumItems: 20 }
  );

  return (
    <div>
      {results.map((msg) => (
        <div key={msg._id}>{msg.body}</div>
      ))}

      {status === "LoadingFirstPage" && <p>Loading...</p>}

      {status === "CanLoadMore" && (
        <button onClick={() => loadMore(20)}>Load More</button>
      )}
    </div>
  );
}
```

### With Auth (First-Party Convex Auth)

Convex provides a robust, native authentication library (`@convex-dev/auth`) featuring Magic Links, Passwords, and 80+ OAuth providers without needing a third-party service.

```typescript
// app/ConvexClientProvider.tsx
"use client";
import { ConvexAuthProvider } from "@convex-dev/auth/react";
import { ConvexReactClient } from "convex/react";
import { ReactNode } from "react";

const convex = new ConvexReactClient(process.env.NEXT_PUBLIC_CONVEX_URL!);

export function ConvexClientProvider({ children }: { children: ReactNode }) {
  return (
    <ConvexAuthProvider client={convex}>
      {children}
    </ConvexAuthProvider>
  );
}
```

```typescript
// Client-side sign in
import { useAuthActions } from "@convex-dev/auth/react";

export function Login() {
  const { signIn } = useAuthActions();
  return <button onClick={() => signIn("github")}>Sign in with GitHub</button>;
}
```

### With Auth (Third-Party Clerk Example)

If you prefer a hosted third-party solution like Clerk:

```typescript
// app/ConvexClientProvider.tsx
"use client";
import { ConvexProviderWithClerk } from "convex/react-clerk";
import { ClerkProvider, useAuth } from "@clerk/nextjs";
import { ConvexReactClient } from "convex/react";

const convex = new ConvexReactClient(process.env.NEXT_PUBLIC_CONVEX_URL!);

export function ConvexClientProvider({ children }: { children: ReactNode }) {
  return (
    <ClerkProvider publishableKey={process.env.NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY!}>
      <ConvexProviderWithClerk client={convex} useAuth={useAuth}>
        {children}
      </ConvexProviderWithClerk>
    </ClerkProvider>
  );
}
```

### With Auth (Better Auth Component)

Convex also has a community component (`@convex-dev/better-auth`) that integrates the Better Auth library directly into the Convex backend. This is currently in **early alpha**.

```bash
npm install better-auth @convex-dev/better-auth
npx convex env set BETTER_AUTH_SECRET your-secret-here
npx convex env set SITE_URL http://localhost:3000
```

Better Auth provides email/password, social logins, two-factor authentication, and session management — all running inside Convex functions rather than an external auth server.

### Angular Integration

Convex does not have an official Angular client library, but Angular apps can use the core `convex` package directly with Angular's Dependency Injection and Signals.

```typescript
// services/convex.service.ts
import { Injectable, signal, effect, OnDestroy } from "@angular/core";
import { ConvexClient } from "convex/browser";
import { api } from "../../convex/_generated/api";
import { FunctionReturnType } from "convex/server";

@Injectable({ providedIn: "root" })
export class ConvexService implements OnDestroy {
  private client = new ConvexClient(environment.convexUrl);

  // Reactive signal — updates automatically when data changes
  tasks = signal<FunctionReturnType<typeof api.tasks.list> | undefined>(
    undefined,
  );

  constructor() {
    // Subscribe to a reactive query
    this.client.onUpdate(api.tasks.list, {}, (result) => {
      this.tasks.set(result);
    });
  }

  async addTask(text: string) {
    await this.client.mutation(api.tasks.create, {
      text,
      isCompleted: false,
    });
  }

  ngOnDestroy() {
    this.client.close();
  }
}
```

```typescript
// Component usage
import { Component, inject } from "@angular/core";
import { ConvexService } from "./services/convex.service";

@Component({
  selector: "app-task-list",
  template: `
    @if (convex.tasks(); as tasks) {
      @for (task of tasks; track task._id) {
        <div>{{ task.text }}</div>
      }
    } @else {
      <p>Loading...</p>
    }
    <button (click)="convex.addTask('New task')">Add Task</button>
  `,
})
export class TaskListComponent {
  convex = inject(ConvexService);
}
```

> **Note:** The community library `@robmanganelly/ngx-convex` provides a more Angular-native experience with React-like hooks adapted for Angular DI and Signals.

## Scheduling & Cron Jobs

### One-off Scheduled Functions

```typescript
import { mutation } from "./_generated/server";
import { api } from "./_generated/api";

export const sendReminder = mutation({
  args: { userId: v.id("users"), message: v.string(), delayMs: v.number() },
  handler: async (ctx, args) => {
    await ctx.scheduler.runAfter(args.delayMs, api.notifications.send, {
      userId: args.userId,
      message: args.message,
    });
  },
});
```

### Cron Jobs

```typescript
// convex/crons.ts
import { cronJobs } from "convex/server";
import { api } from "./_generated/api";

const crons = cronJobs();

crons.interval("clear old logs", { hours: 24 }, api.logs.clearOld);

crons.cron(
  "weekly digest",
  "0 9 * * 1", // Every Monday at 9 AM
  api.emails.sendWeeklyDigest,
);

export default crons;
```

## File Storage

```typescript
// Generate an upload URL (mutation)
export const generateUploadUrl = mutation({
  args: {},
  handler: async (ctx) => {
    return await ctx.storage.generateUploadUrl();
  },
});

// Save file reference after upload (mutation)
export const saveFile = mutation({
  args: { storageId: v.id("_storage"), name: v.string() },
  handler: async (ctx, args) => {
    await ctx.db.insert("files", {
      storageId: args.storageId,
      name: args.name,
    });
  },
});

// Get a URL to serve a file (query)
export const getFileUrl = query({
  args: { storageId: v.id("_storage") },
  handler: async (ctx, args) => {
    return await ctx.storage.getUrl(args.storageId);
  },
});
```

## Environment Variables

```bash
# Set environment variables for your deployment
npx convex env set OPENAI_API_KEY sk-...
npx convex env set SENDGRID_API_KEY SG...

# List current env vars
npx convex env list

# Remove an env var
npx convex env unset OPENAI_API_KEY
```

Access in actions (NOT in queries or mutations):

```typescript
// Only available in actions
const apiKey = process.env.OPENAI_API_KEY;
```

## Deployment & CLI

```bash
# Development (watches for changes, syncs to dev deployment)
npx convex dev

# Deploy to production
npx convex deploy

# Import data
npx convex import --table tasks data.jsonl

# Export data
npx convex export --path ./backup

# Open Convex dashboard
npx convex dashboard

# Run a function from CLI
npx convex run tasks:list

# View logs
npx convex logs
```

## Best Practices

- ✅ Define schemas — adds type safety across your entire stack
- ✅ Use indexes for queries — avoids full table scans
- ✅ Use compound indexes with equality filters first, range filter last
- ✅ Rely on native determinism — `Date.now()` and `Math.random()` are 100% safe to use in queries and mutations because Convex freezes time at the start of every function execution!
- ✅ Use `v.id("tableName")` for document references instead of plain strings
- ✅ Use actions for external API calls (never call external APIs from queries or mutations)
- ✅ Use `ctx.runQuery` / `ctx.runMutation` from actions — never access `ctx.db` directly in actions
- ✅ Add argument validators to all functions — they enforce runtime type safety
- ✅ Return `null` when a document isn't found instead of throwing an error unless missing is exceptional
- ✅ Prefer `withIndex` over `.filter()` for query performance

## Anti-Patterns to Avoid

1. **❌ External API calls in queries/mutations**: Only actions can call external services. Queries and mutations run in the Convex transaction engine.
2. **❌ Doing slow CPU-bound work in mutations**: Mutations block database commits; offload heavy processing to actions.
3. **❌ Using `.collect()` on large tables without limits**: Fetches all documents into memory. Use `.take(N)` or `.paginate()`.
4. **❌ Skipping schema definition**: Without a schema you lose end-to-end type safety, the main Convex advantage.
5. **❌ Using `.filter()` instead of indexes**: `.filter()` does a full table scan. Define an index and use `.withIndex()`.
6. **❌ Storing large blobs in documents**: Use Convex file storage (`_storage`) for files; keep documents lean.
7. **❌ Circular `runQuery`/`runMutation` chains**: Actions calling mutations that schedule actions can create infinite loops.

## Common Pitfalls

- **Problem:** "Query returns `undefined` on first render"
  **Solution:** This is expected — Convex queries are async. Check for `undefined` before rendering (this means loading, not empty).

- **Problem:** "Mutation throws `Document not found`"
  **Solution:** Documents may have been deleted between your read and write due to optimistic concurrency. Re-read inside the mutation.

- **Problem:** "`process.env` is undefined in query/mutation"
  **Solution:** Environment variables are only accessible in **actions** (not queries or mutations) because queries/mutations run in the deterministic transaction engine.

- **Problem:** "Function handler is too slow"
  **Solution:** Add indexes for your query patterns. Use `withIndex()` instead of `.filter()`. For complex operations, break into smaller mutations.

- **Problem:** "Schema push fails with existing data"
  **Solution:** Convex validates existing data against new schemas. Either migrate existing documents first, or use `v.optional()` for new fields.

## Limitations

- Queries and mutations cannot call external HTTP APIs (use actions instead)
- No raw SQL — you work with the Convex query builder API
- Environment variables only available in actions, not in queries or mutations
- Document size limit of 1MB
- Maximum function execution time limits apply
- No server-side rendering of Convex data without specific SSR patterns (use preloading)
- Schemas are enforced at write-time; changing schemas requires data migration for existing documents

## Related Skills

- `@firebase` — Alternative BaaS with Firestore (compare: Convex is TypeScript-first with ACID transactions)
- `@supabase-automation` — Alternative with PostgreSQL backend (compare: Convex is document-relational with built-in reactivity)
- `@prisma-expert` — ORM for traditional databases (Convex replaces both ORM and database)
- `@react-patterns` — Frontend patterns that pair well with Convex React hooks
- `@nextjs-app-router` — Next.js App Router integration patterns
- `@authentication-oauth` — Auth patterns (Convex supports Clerk, Auth0, Convex Auth)
- `@stripe` — Payment integration via Convex actions and HTTP webhooks

## Resources

- [Official Docs](https://docs.convex.dev)
- [Convex Stack (Blog)](https://stack.convex.dev)
- [GitHub](https://github.com/get-convex/convex-backend)
- [Discord Community](https://convex.dev/community)
- [Convex Chef (AI Starter)](https://chef.convex.dev)

---

## Imported Reference

---
name: django-access-review
description: django-access-review
---

---
name: django-access-review
description: Django access control and IDOR security review. Use when reviewing Django views, DRF viewsets, ORM queries, or any Python/Django code handling user authorization. Trigger keywords: "IDOR", "access control", "authorization", "Django permissions", "object permissions", "tenant...
--- LICENSE
---

<!--
Reference material based on OWASP Cheat Sheet Series (CC BY-SA 4.0)
https://cheatsheetseries.owasp.org/
-->

# Django Access Control & IDOR Review

Find access control vulnerabilities by investigating how the codebase answers one question:

**Can User A access, modify, or delete User B's data?**

## Philosophy: Investigation Over Pattern Matching

Do NOT scan for predefined vulnerable patterns. Instead:

1. **Understand** how authorization works in THIS codebase
2. **Ask questions** about specific data flows
3. **Trace code** to find where (or if) access checks happen
4. **Report** only what you've confirmed through investigation

Every codebase implements authorization differently. Your job is to understand this specific implementation, then find gaps.

---

## Phase 1: Understand the Authorization Model

Before looking for bugs, answer these questions about the codebase:

### How is authorization enforced?

Research the codebase to find:

```
□ Where are permission checks implemented?
  - Decorators? (@login_required, @permission_required, custom?)
  - Middleware? (TenantMiddleware, AuthorizationMiddleware?)
  - Base classes? (BaseAPIView, TenantScopedViewSet?)
  - Permission classes? (DRF permission_classes?)
  - Custom mixins? (OwnershipMixin, TenantMixin?)

□ How are queries scoped?
  - Custom managers? (TenantManager, UserScopedManager?)
  - get_queryset() overrides?
  - Middleware that sets query context?

□ What's the ownership model?
  - Single user ownership? (document.owner_id)
  - Organization/tenant ownership? (document.organization_id)
  - Hierarchical? (org -> team -> user -> resource)
  - Role-based within context? (org admin vs member)
```

### Investigation commands

```bash
# Find how auth is typically done
grep -rn "permission_classes\|@login_required\|@permission_required" --include="*.py" | head -20

# Find base classes that views inherit from
grep -rn "class Base.*View\|class.*Mixin.*:" --include="*.py" | head -20

# Find custom managers
grep -rn "class.*Manager\|def get_queryset" --include="*.py" | head -20

# Find ownership fields on models
grep -rn "owner\|user_id\|organization\|tenant" --include="models.py" | head -30
```

**Do not proceed until you understand the authorization model.**

---

## Phase 2: Map the Attack Surface

Identify endpoints that handle user-specific data:

### What resources exist?

```
□ What models contain user data?
□ Which have ownership fields (owner_id, user_id, organization_id)?
□ Which are accessed via ID in URLs or request bodies?
```

### What operations are exposed?

For each resource, map:
- List endpoints - what data is returned?
- Detail/retrieve endpoints - how is the object fetched?
- Create endpoints - who sets the owner?
- Update endpoints - can users modify others' data?
- Delete endpoints - can users delete others' data?
- Custom actions - what do they access?

---

## Phase 3: Ask Questions and Investigate

For each endpoint that handles user data, ask:

### The Core Question

**"If I'm User A and I know the ID of User B's resource, can I access it?"**

Trace the code to answer this:

```
1. Where does the resource ID enter the system?
   - URL path: /api/documents/{id}/
   - Query param: ?document_id=123
   - Request body: {"document_id": 123}

2. Where is that ID used to fetch data?
   - Find the ORM query or database call

3. Between (1) and (2), what checks exist?
   - Is the query scoped to current user?
   - Is there an explicit ownership check?
   - Is there a permission check on the object?
   - Does a base class or mixin enforce access?

4. If you can't find a check, is there one you missed?
   - Check parent classes
   - Check middleware
   - Check managers
   - Check decorators at URL level
```

### Follow-Up Questions

```
□ For list endpoints: Does the query filter to user's data, or return everything?

□ For create endpoints: Who sets the owner - the server or the request?

□ For bulk operations: Are they scoped to user's data?

□ For related resources: If I can access a document, can I access its comments?
  What if the document belongs to someone else?

□ For tenant/org resources: Can User in Org A access Org B's data by changing
  the org_id in the URL?
```

---

## Phase 4: Trace Specific Flows

Pick a concrete endpoint and trace it completely.

### Example Investigation

```
Endpoint: GET /api/documents/{pk}/

1. Find the view handling this URL
   → DocumentViewSet.retrieve() in api/views.py

2. Check what DocumentViewSet inherits from
   → class DocumentViewSet(viewsets.ModelViewSet)
   → No custom base class with authorization

3. Check permission_classes
   → permission_classes = [IsAuthenticated]
   → Only checks login, not ownership

4. Check get_queryset()
   → def get_queryset(self):
   →     return Document.objects.all()
   → Returns ALL documents!

5. Check for has_object_permission()
   → Not implemented

6. Check retrieve() method
   → Uses default, which calls get_object()
   → get_object() uses get_queryset(), which returns all

7. Conclusion: IDOR - Any authenticated user can access any document
```

### What to look for when tracing

```
Potential gap indicators (investigate further, don't auto-flag):
- get_queryset() returns .all() or filters without user
- Direct Model.objects.get(pk=pk) without ownership in query
- ID comes from request body for sensitive operations
- Permission class checks auth but not ownership
- No has_object_permission() and queryset isn't scoped

Likely safe patterns (but verify the implementation):
- get_queryset() filters by request.user or user's org
- Custom permission class with has_object_permission()
- Base class that enforces scoping
- Manager that auto-filters
```

---

## Phase 5: Report Findings

Only report issues you've confirmed through investigation.

### Confidence Levels

| Level | Meaning | Action |
|-------|---------|--------|
| **HIGH** | Traced the flow, confirmed no check exists | Report with evidence |
| **MEDIUM** | Check may exist but couldn't confirm | Note for manual verification |
| **LOW** | Theoretical, likely mitigated | Do not report |

### Suggested Fixes Must Enforce, Not Document

**Bad fix**: Adding a comment saying "caller must validate permissions"
**Good fix**: Adding code that actually validates permissions

A comment or docstring does not enforce authorization. Your suggested fix must include actual code that:
- Validates the user has permission before proceeding
- Raises an exception or returns an error if unauthorized
- Makes unauthorized access impossible, not just discouraged

Example of a BAD fix suggestion:
```python
def get_resource(resource_id):
    # IMPORTANT: Caller must ensure user has access to this resource
    return Resource.objects.get(pk=resource_id)
```

Example of a GOOD fix suggestion:
```python
def get_resource(resource_id, user):
    resource = Resource.objects.get(pk=resource_id)
    if resource.owner_id != user.id:
        raise PermissionDenied("Access denied")
    return resource
```

If you can't determine the right enforcement mechanism, say so - but never suggest documentation as the fix.

### Report Format

```markdown
## Access Control Review: [Component]

### Authorization Model
[Brief description of how this codebase handles authorization]

### Findings

#### [IDOR-001] [Title] (Severity: High/Medium)
- **Location**: `path/to/file.py:123`
- **Confidence**: High - confirmed through code tracing
- **The Question**: Can User A access User B's documents?
- **Investigation**:
  1. Traced GET /api/documents/{pk}/ to DocumentViewSet
  2. Checked get_queryset() - returns Document.objects.all()
  3. Checked permission_classes - only IsAuthenticated
  4. Checked for has_object_permission() - not implemented
  5. Verified no relevant middleware or base class checks
- **Evidence**: [Code snippet showing the gap]
- **Impact**: Any authenticated user can read any document by ID
- **Suggested Fix**: [Code that enforces authorization - NOT a comment]

### Needs Manual Verification
[Issues where authorization exists but couldn't confirm effectiveness]

### Areas Not Reviewed
[Endpoints or flows not covered in this review]
```

---

## Common Django Authorization Patterns

These are patterns you might find - not a checklist to match against.

### Query Scoping
```python
# Scoped to user
Document.objects.filter(owner=request.user)

# Scoped to organization
Document.objects.filter(organization=request.user.organization)

# Using a custom manager
Document.objects.for_user(request.user)  # Investigate what this does
```

### Permission Enforcement
```python
# DRF permission classes
permission_classes = [IsAuthenticated, IsOwner]

# Custom has_object_permission
def has_object_permission(self, request, view, obj):
    return obj.owner == request.user

# Django decorators
@permission_required('app.view_document')

# Manual checks
if document.owner != request.user:
    raise PermissionDenied()
```

### Ownership Assignment
```python
# Server-side (safe)
def perform_create(self, serializer):
    serializer.save(owner=self.request.user)

# From request (investigate)
serializer.save(**request.data)  # Does request.data include owner?
```

---

## Investigation Checklist

Use this to guide your review, not as a pass/fail checklist:

```
□ I understand how authorization is typically implemented in this codebase
□ I've identified the ownership model (user, org, tenant, etc.)
□ I've mapped the key endpoints that handle user data
□ For each sensitive endpoint, I've traced the flow and asked:
  - Where does the ID come from?
  - Where is data fetched?
  - What checks exist between input and data access?
□ I've verified my findings by checking parent classes and middleware
□ I've only reported issues I've confirmed through investigation
```

---

## Imported Reference

---
name: django-perf-review
description: Django performance code review. Use when asked to "review Django performance", "find N+1 queries", "optimize Django", "check queryset performance", "database performance", "Django ORM issues", or audit Django code for performance problems.
allowed-tools: Read, Grep, Glob, Bash, Task
license: LICENSE
---

# Django Performance Review

Review Django code for **validated** performance issues. Research the codebase to confirm issues before reporting. Report only what you can prove.

## Review Approach

1. **Research first** - Trace data flow, check for existing optimizations, verify data volume
2. **Validate before reporting** - Pattern matching is not validation
3. **Zero findings is acceptable** - Don't manufacture issues to appear thorough
4. **Severity must match impact** - If you catch yourself writing "minor" in a CRITICAL finding, it's not critical. Downgrade or skip it.

## Impact Categories

Issues are organized by impact. Focus on CRITICAL and HIGH - these cause real problems at scale.

| Priority | Category | Impact |
|----------|----------|--------|
| 1 | N+1 Queries | **CRITICAL** - Multiplies with data, causes timeouts |
| 2 | Unbounded Querysets | **CRITICAL** - Memory exhaustion, OOM kills |
| 3 | Missing Indexes | **HIGH** - Full table scans on large tables |
| 4 | Write Loops | **HIGH** - Lock contention, slow requests |
| 5 | Inefficient Patterns | **LOW** - Rarely worth reporting |

---

## Priority 1: N+1 Queries (CRITICAL)

**Impact:** Each N+1 adds `O(n)` database round trips. 100 rows = 100 extra queries. 10,000 rows = timeout.

### Rule: Prefetch related data accessed in loops

Validate by tracing: View → Queryset → Template/Serializer → Loop access

```python
# PROBLEM: N+1 - each iteration queries profile
def user_list(request):
    users = User.objects.all()
    return render(request, 'users.html', {'users': users})

# Template:
# {% for user in users %}
#     {{ user.profile.bio }}  ← triggers query per user
# {% endfor %}

# SOLUTION: Prefetch in view
def user_list(request):
    users = User.objects.select_related('profile')
    return render(request, 'users.html', {'users': users})
```

### Rule: Prefetch in serializers, not just views

DRF serializers accessing related fields cause N+1 if queryset isn't optimized.

```python
# PROBLEM: SerializerMethodField queries per object
class UserSerializer(serializers.ModelSerializer):
    order_count = serializers.SerializerMethodField()

    def get_order_count(self, obj):
        return obj.orders.count()  # ← query per user

# SOLUTION: Annotate in viewset, access in serializer
class UserViewSet(viewsets.ModelViewSet):
    def get_queryset(self):
        return User.objects.annotate(order_count=Count('orders'))

class UserSerializer(serializers.ModelSerializer):
    order_count = serializers.IntegerField(read_only=True)
```

### Rule: Model properties that query are dangerous in loops

```python
# PROBLEM: Property triggers query when accessed
class User(models.Model):
    @property
    def recent_orders(self):
        return self.orders.filter(created__gte=last_week)[:5]

# Used in template loop = N+1

# SOLUTION: Use Prefetch with custom queryset, or annotate
```

### Validation Checklist for N+1
- [ ] Traced data flow from view to template/serializer
- [ ] Confirmed related field is accessed inside a loop
- [ ] Searched codebase for existing select_related/prefetch_related
- [ ] Verified table has significant row count (1000+)
- [ ] Confirmed this is a hot path (not admin, not rare action)

---

## Priority 2: Unbounded Querysets (CRITICAL)

**Impact:** Loading entire tables exhausts memory. Large tables cause OOM kills and worker restarts.

### Rule: Always paginate list endpoints

```python
# PROBLEM: No pagination - loads all rows
class UserListView(ListView):
    model = User
    template_name = 'users.html'

# SOLUTION: Add pagination
class UserListView(ListView):
    model = User
    template_name = 'users.html'
    paginate_by = 25
```

### Rule: Use iterator() for large batch processing

```python
# PROBLEM: Loads all objects into memory at once
for user in User.objects.all():
    process(user)

# SOLUTION: Stream with iterator()
for user in User.objects.iterator(chunk_size=1000):
    process(user)
```

### Rule: Never call list() on unbounded querysets

```python
# PROBLEM: Forces full evaluation into memory
all_users = list(User.objects.all())

# SOLUTION: Keep as queryset, slice if needed
users = User.objects.all()[:100]
```

### Validation Checklist for Unbounded Querysets
- [ ] Table is large (10k+ rows) or will grow unbounded
- [ ] No pagination class, paginate_by, or slicing
- [ ] This runs on user-facing request (not background job with chunking)

---

## Priority 3: Missing Indexes (HIGH)

**Impact:** Full table scans. Negligible on small tables, catastrophic on large ones.

### Rule: Index fields used in WHERE clauses on large tables

```python
# PROBLEM: Filtering on unindexed field
# User.objects.filter(email=email)  # full scan if no index

class User(models.Model):
    email = models.EmailField()  # ← no db_index

# SOLUTION: Add index
class User(models.Model):
    email = models.EmailField(db_index=True)
```

### Rule: Index fields used in ORDER BY on large tables

```python
# PROBLEM: Sorting requires full scan without index
Order.objects.order_by('-created')

# SOLUTION: Index the sort field
class Order(models.Model):
    created = models.DateTimeField(db_index=True)
```

### Rule: Use composite indexes for common query patterns

```python
class Order(models.Model):
    user = models.ForeignKey(User)
    status = models.CharField(max_length=20)
    created = models.DateTimeField()

    class Meta:
        indexes = [
            models.Index(fields=['user', 'status']),  # for filter(user=x, status=y)
            models.Index(fields=['status', '-created']),  # for filter(status=x).order_by('-created')
        ]
```

### Validation Checklist for Missing Indexes
- [ ] Table has 10k+ rows
- [ ] Field is used in filter() or order_by() on hot path
- [ ] Checked model - no db_index=True or Meta.indexes entry
- [ ] Not a foreign key (already indexed automatically)

---

## Priority 4: Write Loops (HIGH)

**Impact:** N database writes instead of 1. Lock contention. Slow requests.

### Rule: Use bulk_create instead of create() in loops

```python
# PROBLEM: N inserts, N round trips
for item in items:
    Model.objects.create(name=item['name'])

# SOLUTION: Single bulk insert
Model.objects.bulk_create([
    Model(name=item['name']) for item in items
])
```

### Rule: Use update() or bulk_update instead of save() in loops

```python
# PROBLEM: N updates
for obj in queryset:
    obj.status = 'done'
    obj.save()

# SOLUTION A: Single UPDATE statement (same value for all)
queryset.update(status='done')

# SOLUTION B: bulk_update (different values)
for obj in objects:
    obj.status = compute_status(obj)
Model.objects.bulk_update(objects, ['status'], batch_size=500)
```

### Rule: Use delete() on queryset, not in loops

```python
# PROBLEM: N deletes
for obj in queryset:
    obj.delete()

# SOLUTION: Single DELETE
queryset.delete()
```

### Validation Checklist for Write Loops
- [ ] Loop iterates over 100+ items (or unbounded)
- [ ] Each iteration calls create(), save(), or delete()
- [ ] This runs on user-facing request (not one-time migration script)

---

## Priority 5: Inefficient Patterns (LOW)

**Rarely worth reporting.** Include only as minor notes if you're already reporting real issues.

### Pattern: count() vs exists()

```python
# Slightly suboptimal
if queryset.count() > 0:
    do_thing()

# Marginally better
if queryset.exists():
    do_thing()
```

**Usually skip** - difference is <1ms in most cases.

### Pattern: len(queryset) vs count()

```python
# Fetches all rows to count
if len(queryset) > 0:  # bad if queryset not yet evaluated

# Single COUNT query
if queryset.count() > 0:
```

**Only flag** if queryset is large and not already evaluated.

### Pattern: get() in small loops

```python
# N queries, but if N is small (< 20), often fine
for id in ids:
    obj = Model.objects.get(id=id)
```

**Only flag** if loop is large or this is in a very hot path.

---

## Validation Requirements

Before reporting ANY issue:

1. **Trace the data flow** - Follow queryset from creation to consumption
2. **Search for existing optimizations** - Grep for select_related, prefetch_related, pagination
3. **Verify data volume** - Check if table is actually large
4. **Confirm hot path** - Trace call sites, verify this runs frequently
5. **Rule out mitigations** - Check for caching, rate limiting

**If you cannot validate all steps, do not report.**

---

## Output Format

```markdown
## Django Performance Review: [File/Component Name]

### Summary
Validated issues: X (Y Critical, Z High)

### Findings

#### [PERF-001] N+1 Query in UserListView (CRITICAL)
**Location:** `views.py:45`

**Issue:** Related field `profile` accessed in template loop without prefetch.

**Validation:**
- Traced: UserListView → users queryset → user_list.html → `{{ user.profile.bio }}` in loop
- Searched codebase: no select_related('profile') found
- User table: 50k+ rows (verified in admin)
- Hot path: linked from homepage navigation

**Evidence:**
```python
def get_queryset(self):
    return User.objects.filter(active=True)  # no select_related
```

**Fix:**
```python
def get_queryset(self):
    return User.objects.filter(active=True).select_related('profile')
```
```

If no issues found: "No performance issues identified after reviewing [files] and validating [what you checked]."

**Before submitting, sanity check each finding:**
- Does the severity match the actual impact? ("Minor inefficiency" ≠ CRITICAL)
- Is this a real performance issue or just a style preference?
- Would fixing this measurably improve performance?

If the answer to any is "no" - remove the finding.

---

## What NOT to Report

- Test files
- Admin-only views
- Management commands
- Migration files
- One-time scripts
- Code behind disabled feature flags
- Tables with <1000 rows that won't grow
- Patterns in cold paths (rarely executed code)
- Micro-optimizations (exists vs count, only/defer without evidence)

### False Positives to Avoid

**Queryset variable assignment is not an issue:**
```python
# This is FINE - no performance difference
projects_qs = Project.objects.filter(org=org)
projects = list(projects_qs)

# vs this - identical performance
projects = list(Project.objects.filter(org=org))
```
Querysets are lazy. Assigning to a variable doesn't execute anything.

**Single query patterns are not N+1:**
```python
# This is ONE query, not N+1
projects = list(Project.objects.filter(org=org))
```
N+1 requires a loop that triggers additional queries. A single `list()` call is fine.

**Missing select_related on single object fetch is not N+1:**
```python
# This is 2 queries, not N+1 - report as LOW at most
state = AutofixState.objects.filter(pr_id=pr_id).first()
project_id = state.request.project_id  # second query
```
N+1 requires a loop. A single object doing 2 queries instead of 1 can be reported as LOW if relevant, but never as CRITICAL/HIGH.

**Style preferences are not performance issues:**
If your only suggestion is "combine these two lines" or "rename this variable" - that's style, not performance. Don't report it.

---

## Imported Reference

---
name: django-pro
description: Master Django 5.x with async views, DRF, Celery, and Django Channels. Build scalable web applications with proper architecture, testing, and deployment.
risk: unknown
source: community
date_added: '2026-02-27'
---

## Use this skill when

- Working on django pro tasks or workflows
- Needing guidance, best practices, or checklists for django pro

## Do not use this skill when

- The task is unrelated to django pro
- You need a different domain or tool outside this scope

## Instructions

- Clarify goals, constraints, and required inputs.
- Apply relevant best practices and validate outcomes.
- Provide actionable steps and verification.
- If detailed examples are required, open `resources/implementation-playbook.md`.

You are a Django expert specializing in Django 5.x best practices, scalable architecture, and modern web application development.

## Purpose

Expert Django developer specializing in Django 5.x best practices, scalable architecture, and modern web application development. Masters both traditional synchronous and async Django patterns, with deep knowledge of the Django ecosystem including DRF, Celery, and Django Channels.

## Capabilities

### Core Django Expertise

- Django 5.x features including async views, middleware, and ORM operations
- Model design with proper relationships, indexes, and database optimization
- Class-based views (CBVs) and function-based views (FBVs) best practices
- Django ORM optimization with select_related, prefetch_related, and query annotations
- Custom model managers, querysets, and database functions
- Django signals and their proper usage patterns
- Django admin customization and ModelAdmin configuration

### Architecture & Project Structure

- Scalable Django project architecture for enterprise applications
- Modular app design following Django's reusability principles
- Settings management with environment-specific configurations
- Service layer pattern for business logic separation
- Repository pattern implementation when appropriate
- Django REST Framework (DRF) for API development
- GraphQL with Strawberry Django or Graphene-Django

### Modern Django Features

- Async views and middleware for high-performance applications
- ASGI deployment with Uvicorn/Daphne/Hypercorn
- Django Channels for WebSocket and real-time features
- Background task processing with Celery and Redis/RabbitMQ
- Django's built-in caching framework with Redis/Memcached
- Database connection pooling and optimization
- Full-text search with PostgreSQL or Elasticsearch

### Testing & Quality

- Comprehensive testing with pytest-django
- Factory pattern with factory_boy for test data
- Django TestCase, TransactionTestCase, and LiveServerTestCase
- API testing with DRF test client
- Coverage analysis and test optimization
- Performance testing and profiling with django-silk
- Django Debug Toolbar integration

### Security & Authentication

- Django's security middleware and best practices
- Custom authentication backends and user models
- JWT authentication with djangorestframework-simplejwt
- OAuth2/OIDC integration
- Permission classes and object-level permissions with django-guardian
- CORS, CSRF, and XSS protection
- SQL injection prevention and query parameterization

### Database & ORM

- Complex database migrations and data migrations
- Multi-database configurations and database routing
- PostgreSQL-specific features (JSONField, ArrayField, etc.)
- Database performance optimization and query analysis
- Raw SQL when necessary with proper parameterization
- Database transactions and atomic operations
- Connection pooling with django-db-pool or pgbouncer

### Deployment & DevOps

- Production-ready Django configurations
- Docker containerization with multi-stage builds
- Gunicorn/uWSGI configuration for WSGI
- Static file serving with WhiteNoise or CDN integration
- Media file handling with django-storages
- Environment variable management with django-environ
- CI/CD pipelines for Django applications

### Frontend Integration

- Django templates with modern JavaScript frameworks
- HTMX integration for dynamic UIs without complex JavaScript
- Django + React/Vue/Angular architectures
- Webpack integration with django-webpack-loader
- Server-side rendering strategies
- API-first development patterns

### Performance Optimization

- Database query optimization and indexing strategies
- Django ORM query optimization techniques
- Caching strategies at multiple levels (query, view, template)
- Lazy loading and eager loading patterns
- Database connection pooling
- Asynchronous task processing
- CDN and static file optimization

### Third-Party Integrations

- Payment processing (Stripe, PayPal, etc.)
- Email backends and transactional email services
- SMS and notification services
- Cloud storage (AWS S3, Google Cloud Storage, Azure)
- Search engines (Elasticsearch, Algolia)
- Monitoring and logging (Sentry, DataDog, New Relic)

## Behavioral Traits

- Follows Django's "batteries included" philosophy
- Emphasizes reusable, maintainable code
- Prioritizes security and performance equally
- Uses Django's built-in features before reaching for third-party packages
- Writes comprehensive tests for all critical paths
- Documents code with clear docstrings and type hints
- Follows PEP 8 and Django coding style
- Implements proper error handling and logging
- Considers database implications of all ORM operations
- Uses Django's migration system effectively

## Knowledge Base

- Django 5.x documentation and release notes
- Django REST Framework patterns and best practices
- PostgreSQL optimization for Django
- Python 3.11+ features and type hints
- Modern deployment strategies for Django
- Django security best practices and OWASP guidelines
- Celery and distributed task processing
- Redis for caching and message queuing
- Docker and container orchestration
- Modern frontend integration patterns

## Response Approach

1. **Analyze requirements** for Django-specific considerations
2. **Suggest Django-idiomatic solutions** using built-in features
3. **Provide production-ready code** with proper error handling
4. **Include tests** for the implemented functionality
5. **Consider performance implications** of database queries
6. **Document security considerations** when relevant
7. **Offer migration strategies** for database changes
8. **Suggest deployment configurations** when applicable

## Example Interactions

- "Help me optimize this Django queryset that's causing N+1 queries"
- "Design a scalable Django architecture for a multi-tenant SaaS application"
- "Implement async views for handling long-running API requests"
- "Create a custom Django admin interface with inline formsets"
- "Set up Django Channels for real-time notifications"
- "Optimize database queries for a high-traffic Django application"
- "Implement JWT authentication with refresh tokens in DRF"
- "Create a robust background task system with Celery"

---

## Imported Reference

---
name: expo-dev-client
description: Build and distribute Expo development clients locally or via TestFlight
version: 1.0.0
license: MIT
---

Use EAS Build to create development clients for testing native code changes on physical devices. Use this for creating custom Expo Go clients for testing branches of your app.

## Important: When Development Clients Are Needed

**Only create development clients when your app requires custom native code.** Most apps work fine in Expo Go.

You need a dev client ONLY when using:
- Local Expo modules (custom native code)
- Apple targets (widgets, app clips, extensions)
- Third-party native modules not in Expo Go

**Try Expo Go first** with `npx expo start`. If everything works, you don't need a dev client.

## EAS Configuration

Ensure `eas.json` has a development profile:

```json
{
  "cli": {
    "version": ">= 16.0.1",
    "appVersionSource": "remote"
  },
  "build": {
    "production": {
      "autoIncrement": true
    },
    "development": {
      "autoIncrement": true,
      "developmentClient": true
    }
  },
  "submit": {
    "production": {},
    "development": {}
  }
}
```

Key settings:
- `developmentClient: true` - Bundles expo-dev-client for development builds
- `autoIncrement: true` - Automatically increments build numbers
- `appVersionSource: "remote"` - Uses EAS as the source of truth for version numbers

## Building for TestFlight

Build iOS dev client and submit to TestFlight in one command:

```bash
eas build -p ios --profile development --submit
```

This will:
1. Build the development client in the cloud
2. Automatically submit to App Store Connect
3. Send you an email when the build is ready in TestFlight

After receiving the TestFlight email:
1. Download the build from TestFlight on your device
2. Launch the app to see the expo-dev-client UI
3. Connect to your local Metro bundler or scan a QR code

## Building Locally

Build a development client on your machine:

```bash
# iOS (requires Xcode)
eas build -p ios --profile development --local

# Android
eas build -p android --profile development --local
```

Local builds output:
- iOS: `.ipa` file
- Android: `.apk` or `.aab` file

## Installing Local Builds

Install iOS build on simulator:

```bash
# Find the .app in the .tar.gz output
tar -xzf build-*.tar.gz
xcrun simctl install booted ./path/to/App.app
```

Install iOS build on device (requires signing):

```bash
# Use Xcode Devices window or ideviceinstaller
ideviceinstaller -i build.ipa
```

Install Android build:

```bash
adb install build.apk
```

## Building for Specific Platform

```bash
# iOS only
eas build -p ios --profile development

# Android only
eas build -p android --profile development

# Both platforms
eas build --profile development
```

## Checking Build Status

```bash
# List recent builds
eas build:list

# View build details
eas build:view
```

## Using the Dev Client

Once installed, the dev client provides:
- **Development server connection** - Enter your Metro bundler URL or scan QR
- **Build information** - View native build details
- **Launcher UI** - Switch between development servers

Connect to local development:

```bash
# Start Metro bundler
npx expo start --dev-client

# Scan QR code with dev client or enter URL manually
```

## Troubleshooting

**Build fails with signing errors:**
```bash
eas credentials
```

**Clear build cache:**
```bash
eas build -p ios --profile development --clear-cache
```

**Check EAS CLI version:**
```bash
eas --version
eas update
```

---

## Imported Reference

---
name: expo-tailwind-setup
description: Set up Tailwind CSS v4 in Expo with react-native-css and NativeWind v5 for universal styling
version: 1.0.0
license: MIT
---

# Tailwind CSS Setup for Expo with react-native-css

This guide covers setting up Tailwind CSS v4 in Expo using react-native-css and NativeWind v5 for universal styling across iOS, Android, and Web.

## Overview

This setup uses:

- **Tailwind CSS v4** - Modern CSS-first configuration
- **react-native-css** - CSS runtime for React Native
- **NativeWind v5** - Metro transformer for Tailwind in React Native
- **@tailwindcss/postcss** - PostCSS plugin for Tailwind v4

## Installation

```bash
# Install dependencies
npx expo install tailwindcss@^4 nativewind@5.0.0-preview.2 react-native-css@0.0.0-nightly.5ce6396 @tailwindcss/postcss tailwind-merge clsx
```

Add resolutions for lightningcss compatibility:

```json
// package.json
{
  "resolutions": {
    "lightningcss": "1.30.1"
  }
}
```

- autoprefixer is not needed in Expo because of lightningcss
- postcss is included in expo by default

## Configuration Files

### Metro Config

Create or update `metro.config.js`:

```js
// metro.config.js
const { getDefaultConfig } = require("expo/metro-config");
const { withNativewind } = require("nativewind/metro");

/** @type {import('expo/metro-config').MetroConfig} */
const config = getDefaultConfig(__dirname);

module.exports = withNativewind(config, {
  // inline variables break PlatformColor in CSS variables
  inlineVariables: false,
  // We add className support manually
  globalClassNamePolyfill: false,
});
```

### PostCSS Config

Create `postcss.config.mjs`:

```js
// postcss.config.mjs
export default {
  plugins: {
    "@tailwindcss/postcss": {},
  },
};
```

### Global CSS

Create `src/global.css`:

```css
@import "tailwindcss/theme.css" layer(theme);
@import "tailwindcss/preflight.css" layer(base);
@import "tailwindcss/utilities.css";

/* Platform-specific font families */
@media android {
  :root {
    --font-mono: monospace;
    --font-rounded: normal;
    --font-serif: serif;
    --font-sans: normal;
  }
}

@media ios {
  :root {
    --font-mono: ui-monospace;
    --font-serif: ui-serif;
    --font-sans: system-ui;
    --font-rounded: ui-rounded;
  }
}
```

## IMPORTANT: No Babel Config Needed

With Tailwind v4 and NativeWind v5, you do NOT need a babel.config.js for Tailwind. Remove any NativeWind babel presets if present:

```js
// DELETE babel.config.js if it only contains NativeWind config
// The following is NO LONGER needed:
// module.exports = function (api) {
//   api.cache(true);
//   return {
//     presets: [
//       ["babel-preset-expo", { jsxImportSource: "nativewind" }],
//       "nativewind/babel",
//     ],
//   };
// };
```

## CSS Component Wrappers

Since react-native-css requires explicit CSS element wrapping, create reusable components:

### Main Components (`src/tw/index.tsx`)

```tsx
import {
  useCssElement,
  useNativeVariable as useFunctionalVariable,
} from "react-native-css";

import { Link as RouterLink } from "expo-router";
import Animated from "react-native-reanimated";
import React from "react";
import {
  View as RNView,
  Text as RNText,
  Pressable as RNPressable,
  ScrollView as RNScrollView,
  TouchableHighlight as RNTouchableHighlight,
  TextInput as RNTextInput,
  StyleSheet,
} from "react-native";

// CSS-enabled Link
export const Link = (
  props: React.ComponentProps<typeof RouterLink> & { className?: string }
) => {
  return useCssElement(RouterLink, props, { className: "style" });
};

Link.Trigger = RouterLink.Trigger;
Link.Menu = RouterLink.Menu;
Link.MenuAction = RouterLink.MenuAction;
Link.Preview = RouterLink.Preview;

// CSS Variable hook
export const useCSSVariable =
  process.env.EXPO_OS !== "web"
    ? useFunctionalVariable
    : (variable: string) => `var(${variable})`;

// View
export type ViewProps = React.ComponentProps<typeof RNView> & {
  className?: string;
};

export const View = (props: ViewProps) => {
  return useCssElement(RNView, props, { className: "style" });
};
View.displayName = "CSS(View)";

// Text
export const Text = (
  props: React.ComponentProps<typeof RNText> & { className?: string }
) => {
  return useCssElement(RNText, props, { className: "style" });
};
Text.displayName = "CSS(Text)";

// ScrollView
export const ScrollView = (
  props: React.ComponentProps<typeof RNScrollView> & {
    className?: string;
    contentContainerClassName?: string;
  }
) => {
  return useCssElement(RNScrollView, props, {
    className: "style",
    contentContainerClassName: "contentContainerStyle",
  });
};
ScrollView.displayName = "CSS(ScrollView)";

// Pressable
export const Pressable = (
  props: React.ComponentProps<typeof RNPressable> & { className?: string }
) => {
  return useCssElement(RNPressable, props, { className: "style" });
};
Pressable.displayName = "CSS(Pressable)";

// TextInput
export const TextInput = (
  props: React.ComponentProps<typeof RNTextInput> & { className?: string }
) => {
  return useCssElement(RNTextInput, props, { className: "style" });
};
TextInput.displayName = "CSS(TextInput)";

// AnimatedScrollView
export const AnimatedScrollView = (
  props: React.ComponentProps<typeof Animated.ScrollView> & {
    className?: string;
    contentClassName?: string;
    contentContainerClassName?: string;
  }
) => {
  return useCssElement(Animated.ScrollView, props, {
    className: "style",
    contentClassName: "contentContainerStyle",
    contentContainerClassName: "contentContainerStyle",
  });
};

// TouchableHighlight with underlayColor extraction
function XXTouchableHighlight(
  props: React.ComponentProps<typeof RNTouchableHighlight>
) {
  const { underlayColor, ...style } = StyleSheet.flatten(props.style) || {};
  return (
    <RNTouchableHighlight
      underlayColor={underlayColor}
      {...props}
      style={style}
    />
  );
}

export const TouchableHighlight = (
  props: React.ComponentProps<typeof RNTouchableHighlight>
) => {
  return useCssElement(XXTouchableHighlight, props, { className: "style" });
};
TouchableHighlight.displayName = "CSS(TouchableHighlight)";
```

### Image Component (`src/tw/image.tsx`)

```tsx
import { useCssElement } from "react-native-css";
import React from "react";
import { StyleSheet } from "react-native";
import Animated from "react-native-reanimated";
import { Image as RNImage } from "expo-image";

const AnimatedExpoImage = Animated.createAnimatedComponent(RNImage);

export type ImageProps = React.ComponentProps<typeof Image>;

function CSSImage(props: React.ComponentProps<typeof AnimatedExpoImage>) {
  // @ts-expect-error: Remap objectFit style to contentFit property
  const { objectFit, objectPosition, ...style } =
    StyleSheet.flatten(props.style) || {};

  return (
    <AnimatedExpoImage
      contentFit={objectFit}
      contentPosition={objectPosition}
      {...props}
      source={
        typeof props.source === "string" ? { uri: props.source } : props.source
      }
      // @ts-expect-error: Style is remapped above
      style={style}
    />
  );
}

export const Image = (
  props: React.ComponentProps<typeof CSSImage> & { className?: string }
) => {
  return useCssElement(CSSImage, props, { className: "style" });
};

Image.displayName = "CSS(Image)";
```

### Animated Components (`src/tw/animated.tsx`)

```tsx
import * as TW from "./index";
import RNAnimated from "react-native-reanimated";

export const Animated = {
  ...RNAnimated,
  View: RNAnimated.createAnimatedComponent(TW.View),
};
```

## Usage

Import CSS-wrapped components from your tw directory:

```tsx
import { View, Text, ScrollView, Image } from "@/tw";

export default function MyScreen() {
  return (
    <ScrollView className="flex-1 bg-white">
      <View className="p-4 gap-4">
        <Text className="text-xl font-bold text-gray-900">Hello Tailwind!</Text>
        <Image
          className="w-full h-48 rounded-lg object-cover"
          source={{ uri: "https://example.com/image.jpg" }}
        />
      </View>
    </ScrollView>
  );
}
```

## Custom Theme Variables

Add custom theme variables in your global.css using `@theme`:

```css
@layer theme {
  @theme {
    /* Custom fonts */
    --font-rounded: "SF Pro Rounded", sans-serif;

    /* Custom line heights */
    --text-xs--line-height: calc(1em / 0.75);
    --text-sm--line-height: calc(1.25em / 0.875);
    --text-base--line-height: calc(1.5em / 1);

    /* Custom leading scales */
    --leading-tight: 1.25em;
    --leading-snug: 1.375em;
    --leading-normal: 1.5em;
  }
}
```

## Platform-Specific Styles

Use platform media queries for platform-specific styling:

```css
@media ios {
  :root {
    --font-sans: system-ui;
    --font-rounded: ui-rounded;
  }
}

@media android {
  :root {
    --font-sans: normal;
    --font-rounded: normal;
  }
}
```

## Apple System Colors with CSS Variables

Create a CSS file for Apple semantic colors:

```css
/* src/css/sf.css */
@layer base {
  html {
    color-scheme: light;
  }
}

:root {
  /* Accent colors with light/dark mode */
  --sf-blue: light-dark(rgb(0 122 255), rgb(10 132 255));
  --sf-green: light-dark(rgb(52 199 89), rgb(48 209 89));
  --sf-red: light-dark(rgb(255 59 48), rgb(255 69 58));

  /* Gray scales */
  --sf-gray: light-dark(rgb(142 142 147), rgb(142 142 147));
  --sf-gray-2: light-dark(rgb(174 174 178), rgb(99 99 102));

  /* Text colors */
  --sf-text: light-dark(rgb(0 0 0), rgb(255 255 255));
  --sf-text-2: light-dark(rgb(60 60 67 / 0.6), rgb(235 235 245 / 0.6));

  /* Background colors */
  --sf-bg: light-dark(rgb(255 255 255), rgb(0 0 0));
  --sf-bg-2: light-dark(rgb(242 242 247), rgb(28 28 30));
}

/* iOS native colors via platformColor */
@media ios {
  :root {
    --sf-blue: platformColor(systemBlue);
    --sf-green: platformColor(systemGreen);
    --sf-red: platformColor(systemRed);
    --sf-gray: platformColor(systemGray);
    --sf-text: platformColor(label);
    --sf-text-2: platformColor(secondaryLabel);
    --sf-bg: platformColor(systemBackground);
    --sf-bg-2: platformColor(secondarySystemBackground);
  }
}

/* Register as Tailwind theme colors */
@layer theme {
  @theme {
    --color-sf-blue: var(--sf-blue);
    --color-sf-green: var(--sf-green);
    --color-sf-red: var(--sf-red);
    --color-sf-gray: var(--sf-gray);
    --color-sf-text: var(--sf-text);
    --color-sf-text-2: var(--sf-text-2);
    --color-sf-bg: var(--sf-bg);
    --color-sf-bg-2: var(--sf-bg-2);
  }
}
```

Then use in components:

```tsx
<Text className="text-sf-text">Primary text</Text>
<Text className="text-sf-text-2">Secondary text</Text>
<View className="bg-sf-bg">...</View>
```

## Using CSS Variables in JavaScript

Use the `useCSSVariable` hook:

```tsx
import { useCSSVariable } from "@/tw";

function MyComponent() {
  const blue = useCSSVariable("--sf-blue");

  return <View style={{ borderColor: blue }} />;
}
```

## Key Differences from NativeWind v4 / Tailwind v3

1. **No babel.config.js** - Configuration is now CSS-first
2. **PostCSS plugin** - Uses `@tailwindcss/postcss` instead of `tailwindcss`
3. **CSS imports** - Use `@import "tailwindcss/..."` instead of `@tailwind` directives
4. **Theme config** - Use `@theme` in CSS instead of `tailwind.config.js`
5. **Component wrappers** - Must wrap components with `useCssElement` for className support
6. **Metro config** - Use `withNativewind` with different options (`inlineVariables: false`)

## Troubleshooting

### Styles not applying

1. Ensure you have the CSS file imported in your app entry
2. Check that components are wrapped with `useCssElement`
3. Verify Metro config has `withNativewind` applied

### Platform colors not working

1. Use `platformColor()` in `@media ios` blocks
2. Fall back to `light-dark()` for web/Android

### TypeScript errors

Add className to component props:

```tsx
type Props = React.ComponentProps<typeof RNView> & { className?: string };
```

---

## Imported Reference

---
name: firebase
description: "Firebase gives you a complete backend in minutes - auth, database, storage, functions, hosting. But the ease of setup hides real complexity. Security rules are your last line of defense, and they'r..."
risk: unknown
source: "vibeship-spawner-skills (Apache 2.0)"
date_added: "2026-02-27"
---

# Firebase

You're a developer who has shipped dozens of Firebase projects. You've seen the
"easy" path lead to security breaches, runaway costs, and impossible migrations.
You know Firebase is powerful, but you also know its sharp edges.

Your hard-won lessons: The team that skipped security rules got pwned. The team
that designed Firestore like SQL couldn't query their data. The team that
attached listeners to large collections got a $10k bill. You've learned from
all of them.

You advocate for Firebase w

## Capabilities

- firebase-auth
- firestore
- firebase-realtime-database
- firebase-cloud-functions
- firebase-storage
- firebase-hosting
- firebase-security-rules
- firebase-admin-sdk
- firebase-emulators

## Patterns

### Modular SDK Import

Import only what you need for smaller bundles

### Security Rules Design

Secure your data with proper rules from day one

### Data Modeling for Queries

Design Firestore data structure around query patterns

## Anti-Patterns

### ❌ No Security Rules

### ❌ Client-Side Admin Operations

### ❌ Listener on Large Collections

## Related Skills

Works well with: `nextjs-app-router`, `react-patterns`, `authentication-oauth`, `stripe`

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

---

## Imported Reference

---
name: hubspot-integration
description: "Expert patterns for HubSpot CRM integration including OAuth authentication, CRM objects, associations, batch operations, webhooks, and custom objects. Covers Node.js and Python SDKs. Use when: hubs..."
risk: unknown
source: "vibeship-spawner-skills (Apache 2.0)"
date_added: "2026-02-27"
---

# HubSpot Integration

## Patterns

### OAuth 2.0 Authentication

Secure authentication for public apps

### Private App Token

Authentication for single-account integrations

### CRM Object CRUD Operations

Create, read, update, delete CRM records

## Anti-Patterns

### ❌ Using Deprecated API Keys

### ❌ Individual Requests Instead of Batch

### ❌ Polling Instead of Webhooks

## ⚠️ Sharp Edges

| Issue | Severity | Solution |
|-------|----------|----------|
| Issue | high | See docs |
| Issue | high | See docs |
| Issue | critical | See docs |
| Issue | high | See docs |
| Issue | critical | See docs |
| Issue | medium | See docs |
| Issue | high | See docs |
| Issue | medium | See docs |

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

---

## Imported Reference

---
name: m365-agents-dotnet
description: Microsoft 365 Agents SDK for .NET. Build multichannel agents for Teams/M365/Copilot Studio with ASP.NET Core hosting, AgentApplication routing, and MSAL-based auth.
risk: unknown
source: community
date_added: '2026-02-27'
---

# Microsoft 365 Agents SDK (.NET)

## Overview
Build enterprise agents for Microsoft 365, Teams, and Copilot Studio using the Microsoft.Agents SDK with ASP.NET Core hosting, agent routing, and MSAL-based authentication.

## Before implementation
- Use the microsoft-docs MCP to verify the latest APIs for AddAgent, AgentApplication, and authentication options.
- Confirm package versions in NuGet for the Microsoft.Agents.* packages you plan to use.

## Installation

```bash
dotnet add package Microsoft.Agents.Hosting.AspNetCore
dotnet add package Microsoft.Agents.Authentication.Msal
dotnet add package Microsoft.Agents.Storage
dotnet add package Microsoft.Agents.CopilotStudio.Client
dotnet add package Microsoft.Identity.Client.Extensions.Msal
```

## Configuration (appsettings.json)

```json
{
  "TokenValidation": {
    "Enabled": true,
    "Audiences": [
      "{{ClientId}}"
    ],
    "TenantId": "{{TenantId}}"
  },
  "AgentApplication": {
    "StartTypingTimer": false,
    "RemoveRecipientMention": false,
    "NormalizeMentions": false
  },
  "Connections": {
    "ServiceConnection": {
      "Settings": {
        "AuthType": "ClientSecret",
        "ClientId": "{{ClientId}}",
        "ClientSecret": "{{ClientSecret}}",
        "AuthorityEndpoint": "https://login.microsoftonline.com/{{TenantId}}",
        "Scopes": [
          "https://api.botframework.com/.default"
        ]
      }
    }
  },
  "ConnectionsMap": [
    {
      "ServiceUrl": "*",
      "Connection": "ServiceConnection"
    }
  ],
  "CopilotStudioClientSettings": {
    "DirectConnectUrl": "",
    "EnvironmentId": "",
    "SchemaName": "",
    "TenantId": "",
    "AppClientId": "",
    "AppClientSecret": ""
  }
}
```

## Core Workflow: ASP.NET Core agent host

```csharp
using Microsoft.Agents.Builder;
using Microsoft.Agents.Hosting.AspNetCore;
using Microsoft.Agents.Storage;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddHttpClient();
builder.AddAgentApplicationOptions();
builder.AddAgent<MyAgent>();
builder.Services.AddSingleton<IStorage, MemoryStorage>();

builder.Services.AddControllers();
builder.Services.AddAgentAspNetAuthentication(builder.Configuration);

WebApplication app = builder.Build();

app.UseAuthentication();
app.UseAuthorization();

app.MapGet("/", () => "Microsoft Agents SDK Sample");

var incomingRoute = app.MapPost("/api/messages",
    async (HttpRequest request, HttpResponse response, IAgentHttpAdapter adapter, IAgent agent, CancellationToken ct) =>
    {
        await adapter.ProcessAsync(request, response, agent, ct);
    });

if (!app.Environment.IsDevelopment())
{
    incomingRoute.RequireAuthorization();
}
else
{
    app.Urls.Add("http://localhost:3978");
}

app.Run();
```

## AgentApplication routing

```csharp
using Microsoft.Agents.Builder;
using Microsoft.Agents.Builder.App;
using Microsoft.Agents.Builder.State;
using Microsoft.Agents.Core.Models;
using System;
using System.Threading;
using System.Threading.Tasks;

public sealed class MyAgent : AgentApplication
{
    public MyAgent(AgentApplicationOptions options) : base(options)
    {
        OnConversationUpdate(ConversationUpdateEvents.MembersAdded, WelcomeAsync);
        OnActivity(ActivityTypes.Message, OnMessageAsync, rank: RouteRank.Last);
        OnTurnError(OnTurnErrorAsync);
    }

    private static async Task WelcomeAsync(ITurnContext turnContext, ITurnState turnState, CancellationToken ct)
    {
        foreach (ChannelAccount member in turnContext.Activity.MembersAdded)
        {
            if (member.Id != turnContext.Activity.Recipient.Id)
            {
                await turnContext.SendActivityAsync(
                    MessageFactory.Text("Welcome to the agent."),
                    ct);
            }
        }
    }

    private static async Task OnMessageAsync(ITurnContext turnContext, ITurnState turnState, CancellationToken ct)
    {
        await turnContext.SendActivityAsync(
            MessageFactory.Text($"You said: {turnContext.Activity.Text}"),
            ct);
    }

    private static async Task OnTurnErrorAsync(
        ITurnContext turnContext,
        ITurnState turnState,
        Exception exception,
        CancellationToken ct)
    {
        await turnState.Conversation.DeleteStateAsync(turnContext, ct);

        var endOfConversation = Activity.CreateEndOfConversationActivity();
        endOfConversation.Code = EndOfConversationCodes.Error;
        endOfConversation.Text = exception.Message;
        await turnContext.SendActivityAsync(endOfConversation, ct);
    }
}
```

## Copilot Studio direct-to-engine client

### DelegatingHandler for token acquisition (interactive flow)

```csharp
using System.Net.Http.Headers;
using Microsoft.Agents.CopilotStudio.Client;
using Microsoft.Identity.Client;

internal sealed class AddTokenHandler : DelegatingHandler
{
    private readonly SampleConnectionSettings _settings;

    public AddTokenHandler(SampleConnectionSettings settings) : base(new HttpClientHandler())
    {
        _settings = settings;
    }

    protected override async Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request,
        CancellationToken cancellationToken)
    {
        if (request.Headers.Authorization is null)
        {
            string[] scopes = [CopilotClient.ScopeFromSettings(_settings)];

            IPublicClientApplication app = PublicClientApplicationBuilder
                .Create(_settings.AppClientId)
                .WithAuthority(AadAuthorityAudience.AzureAdMyOrg)
                .WithTenantId(_settings.TenantId)
                .WithRedirectUri("http://localhost")
                .Build();

            AuthenticationResult authResponse;
            try
            {
                var account = (await app.GetAccountsAsync()).FirstOrDefault();
                authResponse = await app.AcquireTokenSilent(scopes, account).ExecuteAsync(cancellationToken);
            }
            catch (MsalUiRequiredException)
            {
                authResponse = await app.AcquireTokenInteractive(scopes).ExecuteAsync(cancellationToken);
            }

            request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResponse.AccessToken);
        }

        return await base.SendAsync(request, cancellationToken);
    }
}
```

### Console host with CopilotClient

```csharp
using Microsoft.Agents.CopilotStudio.Client;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

HostApplicationBuilder builder = Host.CreateApplicationBuilder(args);

var settings = new SampleConnectionSettings(
    builder.Configuration.GetSection("CopilotStudioClientSettings"));

builder.Services.AddHttpClient("mcs").ConfigurePrimaryHttpMessageHandler(() =>
{
    return new AddTokenHandler(settings);
});

builder.Services
    .AddSingleton(settings)
    .AddTransient<CopilotClient>(sp =>
    {
        var logger = sp.GetRequiredService<ILoggerFactory>().CreateLogger<CopilotClient>();
        return new CopilotClient(settings, sp.GetRequiredService<IHttpClientFactory>(), logger, "mcs");
    });

IHost host = builder.Build();
var client = host.Services.GetRequiredService<CopilotClient>();

await foreach (var activity in client.StartConversationAsync(emitStartConversationEvent: true))
{
    Console.WriteLine(activity.Type);
}

await foreach (var activity in client.AskQuestionAsync("Hello!", null))
{
    Console.WriteLine(activity.Type);
}
```

## Best Practices

1. Use AgentApplication subclasses to centralize routing and error handling.
2. Use MemoryStorage only for development; use persisted storage in production.
3. Enable TokenValidation in production and require authorization on /api/messages.
4. Keep auth secrets in configuration providers (Key Vault, managed identity, env vars).
5. Reuse HttpClient from IHttpClientFactory and cache MSAL tokens.
6. Prefer async handlers and pass CancellationToken to SDK calls.

## Reference Files

| File | Contents |
| --- | --- |
| references/acceptance-criteria.md | Import paths, hosting pipeline, Copilot Studio client patterns, anti-patterns |

## Reference Links

| Resource | URL |
| --- | --- |
| Microsoft 365 Agents SDK | https://learn.microsoft.com/en-us/microsoft-365/agents-sdk/ |
| AddAgent API | https://learn.microsoft.com/en-us/dotnet/api/microsoft.agents.hosting.aspnetcore.servicecollectionextensions.addagent?view=m365-agents-sdk |
| AgentApplication API | https://learn.microsoft.com/en-us/dotnet/api/microsoft.agents.builder.app.agentapplication?view=m365-agents-sdk |
| Auth configuration options | https://learn.microsoft.com/en-us/microsoft-365/agents-sdk/microsoft-authentication-library-configuration-options |
| Copilot Studio integration | https://learn.microsoft.com/en-us/microsoft-365/agents-sdk/integrate-with-mcs |
| GitHub samples | https://github.com/microsoft/agents |

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

---

## Imported Reference

---
name: m365-agents-py
description: Microsoft 365 Agents SDK for Python. Build multichannel agents for Teams/M365/Copilot Studio with aiohttp hosting, AgentApplication routing, streaming responses, and MSAL-based auth.
risk: unknown
source: community
date_added: '2026-02-27'
---

# Microsoft 365 Agents SDK (Python)

Build enterprise agents for Microsoft 365, Teams, and Copilot Studio using the Microsoft Agents SDK with aiohttp hosting, AgentApplication routing, streaming responses, and MSAL-based authentication.

## Before implementation
- Use the microsoft-docs MCP to verify the latest API signatures for AgentApplication, start_agent_process, and authentication options.
- Confirm package versions on PyPI for the microsoft-agents-* packages you plan to use.

## Important Notice - Import Changes

> **⚠️ Breaking Change**: Recent updates have changed the Python import structure from `microsoft.agents` to `microsoft_agents` (using underscores instead of dots).

## Installation

```bash
pip install microsoft-agents-hosting-core
pip install microsoft-agents-hosting-aiohttp
pip install microsoft-agents-activity
pip install microsoft-agents-authentication-msal
pip install microsoft-agents-copilotstudio-client
pip install python-dotenv aiohttp
```

## Environment Variables (.env)

```bash
CONNECTIONS__SERVICE_CONNECTION__SETTINGS__CLIENTID=<client-id>
CONNECTIONS__SERVICE_CONNECTION__SETTINGS__CLIENTSECRET=<client-secret>
CONNECTIONS__SERVICE_CONNECTION__SETTINGS__TENANTID=<tenant-id>

# Optional: OAuth handlers for auto sign-in
AGENTAPPLICATION__USERAUTHORIZATION__HANDLERS__GRAPH__SETTINGS__AZUREBOTOAUTHCONNECTIONNAME=<connection-name>

# Optional: Azure OpenAI for streaming
AZURE_OPENAI_ENDPOINT=<endpoint>
AZURE_OPENAI_API_VERSION=<version>
AZURE_OPENAI_API_KEY=<key>

# Optional: Copilot Studio client
COPILOTSTUDIOAGENT__ENVIRONMENTID=<environment-id>
COPILOTSTUDIOAGENT__SCHEMANAME=<schema-name>
COPILOTSTUDIOAGENT__TENANTID=<tenant-id>
COPILOTSTUDIOAGENT__AGENTAPPID=<app-id>
```

## Core Workflow: aiohttp-hosted AgentApplication

```python
import logging
from os import environ

from dotenv import load_dotenv
from aiohttp.web import Request, Response, Application, run_app

from microsoft_agents.activity import load_configuration_from_env
from microsoft_agents.hosting.core import (
    Authorization,
    AgentApplication,
    TurnState,
    TurnContext,
    MemoryStorage,
)
from microsoft_agents.hosting.aiohttp import (
    CloudAdapter,
    start_agent_process,
    jwt_authorization_middleware,
)
from microsoft_agents.authentication.msal import MsalConnectionManager

# Enable logging
ms_agents_logger = logging.getLogger("microsoft_agents")
ms_agents_logger.addHandler(logging.StreamHandler())
ms_agents_logger.setLevel(logging.INFO)

# Load configuration
load_dotenv()
agents_sdk_config = load_configuration_from_env(environ)

# Create storage and connection manager
STORAGE = MemoryStorage()
CONNECTION_MANAGER = MsalConnectionManager(**agents_sdk_config)
ADAPTER = CloudAdapter(connection_manager=CONNECTION_MANAGER)
AUTHORIZATION = Authorization(STORAGE, CONNECTION_MANAGER, **agents_sdk_config)

# Create AgentApplication
AGENT_APP = AgentApplicationTurnState


@AGENT_APP.conversation_update("membersAdded")
async def on_members_added(context: TurnContext, _state: TurnState):
    await context.send_activity("Welcome to the agent!")


@AGENT_APP.activity("message")
async def on_message(context: TurnContext, _state: TurnState):
    await context.send_activity(f"You said: {context.activity.text}")


@AGENT_APP.error
async def on_error(context: TurnContext, error: Exception):
    await context.send_activity("The agent encountered an error.")


# Server setup
async def entry_point(req: Request) -> Response:
    agent: AgentApplication = req.app["agent_app"]
    adapter: CloudAdapter = req.app["adapter"]
    return await start_agent_process(req, agent, adapter)


APP = Application(middlewares=[jwt_authorization_middleware])
APP.router.add_post("/api/messages", entry_point)
APP["agent_configuration"] = CONNECTION_MANAGER.get_default_connection_configuration()
APP["agent_app"] = AGENT_APP
APP["adapter"] = AGENT_APP.adapter

if __name__ == "__main__":
    run_app(APP, host="localhost", port=environ.get("PORT", 3978))
```

## AgentApplication Routing

```python
import re
from microsoft_agents.hosting.core import (
    AgentApplication, TurnState, TurnContext, MessageFactory
)
from microsoft_agents.activity import ActivityTypes

AGENT_APP = AgentApplicationTurnState

# Welcome handler
@AGENT_APP.conversation_update("membersAdded")
async def on_members_added(context: TurnContext, _state: TurnState):
    await context.send_activity("Welcome!")

# Regex-based message handler
@AGENT_APP.message(re.compile(r"^hello$", re.IGNORECASE))
async def on_hello(context: TurnContext, _state: TurnState):
    await context.send_activity("Hello!")

# Simple string message handler
@AGENT_APP.message("/status")
async def on_status(context: TurnContext, _state: TurnState):
    await context.send_activity("Status: OK")

# Auth-protected message handler
@AGENT_APP.message("/me", auth_handlers=["GRAPH"])
async def on_profile(context: TurnContext, state: TurnState):
    token_response = await AGENT_APP.auth.get_token(context, "GRAPH")
    if token_response and token_response.token:
        # Use token to call Graph API
        await context.send_activity("Profile retrieved")

# Invoke activity handler
@AGENT_APP.activity(ActivityTypes.invoke)
async def on_invoke(context: TurnContext, _state: TurnState):
    invoke_response = Activity(
        type=ActivityTypes.invoke_response, value={"status": 200}
    )
    await context.send_activity(invoke_response)

# Fallback message handler
@AGENT_APP.activity("message")
async def on_message(context: TurnContext, _state: TurnState):
    await context.send_activity(f"Echo: {context.activity.text}")

# Error handler
@AGENT_APP.error
async def on_error(context: TurnContext, error: Exception):
    await context.send_activity("An error occurred.")
```

## Streaming Responses with Azure OpenAI

```python
from openai import AsyncAzureOpenAI
from microsoft_agents.activity import SensitivityUsageInfo

CLIENT = AsyncAzureOpenAI(
    api_version=environ["AZURE_OPENAI_API_VERSION"],
    azure_endpoint=environ["AZURE_OPENAI_ENDPOINT"],
    api_key=environ["AZURE_OPENAI_API_KEY"]
)

@AGENT_APP.message("poem")
async def on_poem_message(context: TurnContext, _state: TurnState):
    # Configure streaming response
    context.streaming_response.set_feedback_loop(True)
    context.streaming_response.set_generated_by_ai_label(True)
    context.streaming_response.set_sensitivity_label(
        SensitivityUsageInfo(
            type="https://schema.org/Message",
            schema_type="CreativeWork",
            name="Internal",
        )
    )
    context.streaming_response.queue_informative_update("Starting a poem...\n")

    # Stream from Azure OpenAI
    streamed_response = await CLIENT.chat.completions.create(
        model="gpt-4o",
        messages=[
            {"role": "system", "content": "You are a creative assistant."},
            {"role": "user", "content": "Write a poem about Python."}
        ],
        stream=True,
    )
    
    try:
        async for chunk in streamed_response:
            if chunk.choices and chunk.choices[0].delta.content:
                context.streaming_response.queue_text_chunk(
                    chunk.choices[0].delta.content
                )
    finally:
        await context.streaming_response.end_stream()
```

## OAuth / Auto Sign-In

```python
@AGENT_APP.message("/logout")
async def logout(context: TurnContext, state: TurnState):
    await AGENT_APP.auth.sign_out(context, "GRAPH")
    await context.send_activity(MessageFactory.text("You have been logged out."))


@AGENT_APP.message("/me", auth_handlers=["GRAPH"])
async def profile_request(context: TurnContext, state: TurnState):
    user_token_response = await AGENT_APP.auth.get_token(context, "GRAPH")
    if user_token_response and user_token_response.token:
        # Use token to call Microsoft Graph
        async with aiohttp.ClientSession() as session:
            headers = {
                "Authorization": f"Bearer {user_token_response.token}",
                "Content-Type": "application/json",
            }
            async with session.get(
                "https://graph.microsoft.com/v1.0/me", headers=headers
            ) as response:
                if response.status == 200:
                    user_info = await response.json()
                    await context.send_activity(f"Hello, {user_info['displayName']}!")
```

## Copilot Studio Client (Direct to Engine)

```python
import asyncio
from msal import PublicClientApplication
from microsoft_agents.activity import ActivityTypes, load_configuration_from_env
from microsoft_agents.copilotstudio.client import (
    ConnectionSettings,
    CopilotClient,
)

# Token cache (local file for interactive flows)
class LocalTokenCache:
    # See samples for full implementation
    pass

def acquire_token(settings, app_client_id, tenant_id):
    pca = PublicClientApplication(
        client_id=app_client_id,
        authority=f"https://login.microsoftonline.com/{tenant_id}",
    )
    
    token_request = {"scopes": ["https://api.powerplatform.com/.default"]}
    accounts = pca.get_accounts()
    
    if accounts:
        response = pca.acquire_token_silent(token_request["scopes"], account=accounts[0])
        return response.get("access_token")
    else:
        response = pca.acquire_token_interactive(**token_request)
        return response.get("access_token")


async def main():
    settings = ConnectionSettings(
        environment_id=environ.get("COPILOTSTUDIOAGENT__ENVIRONMENTID"),
        agent_identifier=environ.get("COPILOTSTUDIOAGENT__SCHEMANAME"),
    )
    
    token = acquire_token(
        settings,
        app_client_id=environ.get("COPILOTSTUDIOAGENT__AGENTAPPID"),
        tenant_id=environ.get("COPILOTSTUDIOAGENT__TENANTID"),
    )
    
    copilot_client = CopilotClient(settings, token)
    
    # Start conversation
    act = copilot_client.start_conversation(True)
    async for action in act:
        if action.text:
            print(action.text)
    
    # Ask question
    replies = copilot_client.ask_question("Hello!", action.conversation.id)
    async for reply in replies:
        if reply.type == ActivityTypes.message:
            print(reply.text)


asyncio.run(main())
```

## Best Practices

1. Use `microsoft_agents` import prefix (underscores, not dots).
2. Use `MemoryStorage` only for development; use BlobStorage or CosmosDB in production.
3. Always use `load_configuration_from_env(environ)` to load SDK configuration.
4. Include `jwt_authorization_middleware` in aiohttp Application middlewares.
5. Use `MsalConnectionManager` for MSAL-based authentication.
6. Call `end_stream()` in finally blocks when using streaming responses.
7. Use `auth_handlers` parameter on message decorators for OAuth-protected routes.
8. Keep secrets in environment variables, not in source code.

## Reference Files

| File | Contents |
| --- | --- |
| references/acceptance-criteria.md | Import paths, hosting pipeline, streaming, OAuth, and Copilot Studio patterns |

## Reference Links

| Resource | URL |
| --- | --- |
| Microsoft 365 Agents SDK | https://learn.microsoft.com/en-us/microsoft-365/agents-sdk/ |
| GitHub samples (Python) | https://github.com/microsoft/Agents-for-python |
| PyPI packages | https://pypi.org/search/?q=microsoft-agents |
| Integrate with Copilot Studio | https://learn.microsoft.com/en-us/microsoft-365/agents-sdk/integrate-with-mcs |

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

---

## Imported Reference

---
name: m365-agents-ts
description: Microsoft 365 Agents SDK for TypeScript/Node.js.
risk: unknown
source: community
date_added: '2026-02-27'
---

# Microsoft 365 Agents SDK (TypeScript)

Build enterprise agents for Microsoft 365, Teams, and Copilot Studio using the Microsoft 365 Agents SDK with Express hosting, AgentApplication routing, streaming responses, and Copilot Studio client integrations.

## Before implementation
- Use the microsoft-docs MCP to verify the latest API signatures for AgentApplication, startServer, and CopilotStudioClient.
- Confirm package versions on npm before wiring up samples or templates.

## Installation

```bash
npm install @microsoft/agents-hosting @microsoft/agents-hosting-express @microsoft/agents-activity
npm install @microsoft/agents-copilotstudio-client
```

## Environment Variables

```bash
PORT=3978
AZURE_RESOURCE_NAME=<azure-openai-resource>
AZURE_API_KEY=<azure-openai-key>
AZURE_OPENAI_DEPLOYMENT_NAME=gpt-4o-mini

TENANT_ID=<tenant-id>
CLIENT_ID=<client-id>
CLIENT_SECRET=<client-secret>

COPILOT_ENVIRONMENT_ID=<environment-id>
COPILOT_SCHEMA_NAME=<schema-name>
COPILOT_CLIENT_ID=<copilot-app-client-id>
COPILOT_BEARER_TOKEN=<copilot-jwt>
```

## Core Workflow: Express-hosted AgentApplication

```typescript
import { AgentApplication, TurnContext, TurnState } from "@microsoft/agents-hosting";
import { startServer } from "@microsoft/agents-hosting-express";

const agent = new AgentApplication<TurnState>();

agent.onConversationUpdate("membersAdded", async (context: TurnContext) => {
  await context.sendActivity("Welcome to the agent.");
});

agent.onMessage("hello", async (context: TurnContext) => {
  await context.sendActivity(`Echo: ${context.activity.text}`);
});

startServer(agent);
```

## Streaming responses with Azure OpenAI

```typescript
import { azure } from "@ai-sdk/azure";
import { AgentApplication, TurnContext, TurnState } from "@microsoft/agents-hosting";
import { startServer } from "@microsoft/agents-hosting-express";
import { streamText } from "ai";

const agent = new AgentApplication<TurnState>();

agent.onMessage("poem", async (context: TurnContext) => {
  context.streamingResponse.setFeedbackLoop(true);
  context.streamingResponse.setGeneratedByAILabel(true);
  context.streamingResponse.setSensitivityLabel({
    type: "https://schema.org/Message",
    "@type": "CreativeWork",
    name: "Internal",
  });

  await context.streamingResponse.queueInformativeUpdate("starting a poem...");

  const { fullStream } = streamText({
    model: azure(process.env.AZURE_OPENAI_DEPLOYMENT_NAME || "gpt-4o-mini"),
    system: "You are a creative assistant.",
    prompt: "Write a poem about Apollo.",
  });

  try {
    for await (const part of fullStream) {
      if (part.type === "text-delta" && part.text.length > 0) {
        await context.streamingResponse.queueTextChunk(part.text);
      }
      if (part.type === "error") {
        throw new Error(`Streaming error: ${part.error}`);
      }
    }
  } finally {
    await context.streamingResponse.endStream();
  }
});

startServer(agent);
```

## Invoke activity handling

```typescript
import { Activity, ActivityTypes } from "@microsoft/agents-activity";
import { AgentApplication, TurnContext, TurnState } from "@microsoft/agents-hosting";

const agent = new AgentApplication<TurnState>();

agent.onActivity("invoke", async (context: TurnContext) => {
  const invokeResponse = Activity.fromObject({
    type: ActivityTypes.InvokeResponse,
    value: { status: 200 },
  });

  await context.sendActivity(invokeResponse);
  await context.sendActivity("Thanks for submitting your feedback.");
});
```

## Copilot Studio client (Direct to Engine)

```typescript
import { CopilotStudioClient } from "@microsoft/agents-copilotstudio-client";

const settings = {
  environmentId: process.env.COPILOT_ENVIRONMENT_ID!,
  schemaName: process.env.COPILOT_SCHEMA_NAME!,
  clientId: process.env.COPILOT_CLIENT_ID!,
};

const tokenProvider = async (): Promise<string> => {
  return process.env.COPILOT_BEARER_TOKEN!;
};

const client = new CopilotStudioClient(settings, tokenProvider);

const conversation = await client.startConversationAsync();
const reply = await client.askQuestionAsync("Hello!", conversation.id);
console.log(reply);
```

## Copilot Studio WebChat integration

```typescript
import { CopilotStudioWebChat } from "@microsoft/agents-copilotstudio-client";

const directLine = CopilotStudioWebChat.createConnection(client, {
  showTyping: true,
});

window.WebChat.renderWebChat({
  directLine,
}, document.getElementById("webchat")!);
```

## Best Practices

1. Use AgentApplication for routing and keep handlers focused on one responsibility.
2. Prefer streamingResponse for long-running completions and call endStream in finally blocks.
3. Keep secrets out of source code; load tokens from environment variables or secure stores.
4. Reuse CopilotStudioClient instances and cache tokens in your token provider.
5. Validate invoke payloads before logging or persisting feedback.

## Reference Files

| File | Contents |
| --- | --- |
| references/acceptance-criteria.md | Import paths, hosting pipeline, streaming, and Copilot Studio patterns |

## Reference Links

| Resource | URL |
| --- | --- |
| Microsoft 365 Agents SDK | https://learn.microsoft.com/en-us/microsoft-365/agents-sdk/ |
| JavaScript SDK overview | https://learn.microsoft.com/en-us/javascript/api/overview/agents-overview?view=agents-sdk-js-latest |
| @microsoft/agents-hosting-express | https://learn.microsoft.com/en-us/javascript/api/%40microsoft/agents-hosting-express?view=agents-sdk-js-latest |
| @microsoft/agents-copilotstudio-client | https://learn.microsoft.com/en-us/javascript/api/%40microsoft/agents-copilotstudio-client?view=agents-sdk-js-latest |
| Integrate with Copilot Studio | https://learn.microsoft.com/en-us/microsoft-365/agents-sdk/integrate-with-mcs |
| GitHub samples | https://github.com/microsoft/Agents/tree/main/samples/nodejs |

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

---

## Imported Reference

---
name: neon-postgres
description: "Expert patterns for Neon serverless Postgres, branching, connection pooling, and Prisma/Drizzle integration Use when: neon database, serverless postgres, database branching, neon postgres, postgres..."
risk: unknown
source: "vibeship-spawner-skills (Apache 2.0)"
date_added: "2026-02-27"
---

# Neon Postgres

## Patterns

### Prisma with Neon Connection

Configure Prisma for Neon with connection pooling.

Use two connection strings:
- DATABASE_URL: Pooled connection for Prisma Client
- DIRECT_URL: Direct connection for Prisma Migrate

The pooled connection uses PgBouncer for up to 10K connections.
Direct connection required for migrations (DDL operations).


### Drizzle with Neon Serverless Driver

Use Drizzle ORM with Neon's serverless HTTP driver for
edge/serverless environments.

Two driver options:
- neon-http: Single queries over HTTP (fastest for one-off queries)
- neon-serverless: WebSocket for transactions and sessions


### Connection Pooling with PgBouncer

Neon provides built-in connection pooling via PgBouncer.

Key limits:
- Up to 10,000 concurrent connections to pooler
- Connections still consume underlying Postgres connections
- 7 connections reserved for Neon superuser

Use pooled endpoint for application, direct for migrations.


## ⚠️ Sharp Edges

| Issue | Severity | Solution |
|-------|----------|----------|
| Issue | high | See docs |
| Issue | high | See docs |
| Issue | high | See docs |
| Issue | medium | See docs |
| Issue | medium | See docs |
| Issue | low | See docs |
| Issue | medium | See docs |
| Issue | high | See docs |

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

---

## Imported Reference

---
name: nestjs-expert
description: "Nest.js framework expert specializing in module architecture, dependency injection, middleware, guards, interceptors, testing with Jest/Supertest, TypeORM/Mongoose integration, and Passport.js auth..."
category: framework
risk: unknown
source: community
date_added: "2026-02-27"
---

# Nest.js Expert

You are an expert in Nest.js with deep knowledge of enterprise-grade Node.js application architecture, dependency injection patterns, decorators, middleware, guards, interceptors, pipes, testing strategies, database integration, and authentication systems.

## When invoked:

0. If a more specialized expert fits better, recommend switching and stop:
   - Pure TypeScript type issues → typescript-type-expert
   - Database query optimization → database-expert  
   - Node.js runtime issues → nodejs-expert
   - Frontend React issues → react-expert
   
   Example: "This is a TypeScript type system issue. Use the typescript-type-expert subagent. Stopping here."

1. Detect Nest.js project setup using internal tools first (Read, Grep, Glob)
2. Identify architecture patterns and existing modules
3. Apply appropriate solutions following Nest.js best practices
4. Validate in order: typecheck → unit tests → integration tests → e2e tests

## Domain Coverage

### Module Architecture & Dependency Injection
- Common issues: Circular dependencies, provider scope conflicts, module imports
- Root causes: Incorrect module boundaries, missing exports, improper injection tokens
- Solution priority: 1) Refactor module structure, 2) Use forwardRef, 3) Adjust provider scope
- Tools: `nest generate module`, `nest generate service`
- Resources: [Nest.js Modules](https://docs.nestjs.com/modules), [Providers](https://docs.nestjs.com/providers)

### Controllers & Request Handling
- Common issues: Route conflicts, DTO validation, response serialization
- Root causes: Decorator misconfiguration, missing validation pipes, improper interceptors
- Solution priority: 1) Fix decorator configuration, 2) Add validation, 3) Implement interceptors
- Tools: `nest generate controller`, class-validator, class-transformer
- Resources: [Controllers](https://docs.nestjs.com/controllers), [Validation](https://docs.nestjs.com/techniques/validation)

### Middleware, Guards, Interceptors & Pipes
- Common issues: Execution order, context access, async operations
- Root causes: Incorrect implementation, missing async/await, improper error handling
- Solution priority: 1) Fix execution order, 2) Handle async properly, 3) Implement error handling
- Execution order: Middleware → Guards → Interceptors (before) → Pipes → Route handler → Interceptors (after)
- Resources: [Middleware](https://docs.nestjs.com/middleware), [Guards](https://docs.nestjs.com/guards)

### Testing Strategies (Jest & Supertest)
- Common issues: Mocking dependencies, testing modules, e2e test setup
- Root causes: Improper test module creation, missing mock providers, incorrect async handling
- Solution priority: 1) Fix test module setup, 2) Mock dependencies correctly, 3) Handle async tests
- Tools: `@nestjs/testing`, Jest, Supertest
- Resources: [Testing](https://docs.nestjs.com/fundamentals/testing)

### Database Integration (TypeORM & Mongoose)
- Common issues: Connection management, entity relationships, migrations
- Root causes: Incorrect configuration, missing decorators, improper transaction handling
- Solution priority: 1) Fix configuration, 2) Correct entity setup, 3) Implement transactions
- TypeORM: `@nestjs/typeorm`, entity decorators, repository pattern
- Mongoose: `@nestjs/mongoose`, schema decorators, model injection
- Resources: [TypeORM](https://docs.nestjs.com/techniques/database), [Mongoose](https://docs.nestjs.com/techniques/mongodb)

### Authentication & Authorization (Passport.js)
- Common issues: Strategy configuration, JWT handling, guard implementation
- Root causes: Missing strategy setup, incorrect token validation, improper guard usage
- Solution priority: 1) Configure Passport strategy, 2) Implement guards, 3) Handle JWT properly
- Tools: `@nestjs/passport`, `@nestjs/jwt`, passport strategies
- Resources: [Authentication](https://docs.nestjs.com/security/authentication), [Authorization](https://docs.nestjs.com/security/authorization)

### Configuration & Environment Management
- Common issues: Environment variables, configuration validation, async configuration
- Root causes: Missing config module, improper validation, incorrect async loading
- Solution priority: 1) Setup ConfigModule, 2) Add validation, 3) Handle async config
- Tools: `@nestjs/config`, Joi validation
- Resources: [Configuration](https://docs.nestjs.com/techniques/configuration)

### Error Handling & Logging
- Common issues: Exception filters, logging configuration, error propagation
- Root causes: Missing exception filters, improper logger setup, unhandled promises
- Solution priority: 1) Implement exception filters, 2) Configure logger, 3) Handle all errors
- Tools: Built-in Logger, custom exception filters
- Resources: [Exception Filters](https://docs.nestjs.com/exception-filters), [Logger](https://docs.nestjs.com/techniques/logger)

## Environmental Adaptation

### Detection Phase
I analyze the project to understand:
- Nest.js version and configuration
- Module structure and organization
- Database setup (TypeORM/Mongoose/Prisma)
- Testing framework configuration
- Authentication implementation

Detection commands:
```bash
# Check Nest.js setup
test -f nest-cli.json && echo "Nest.js CLI project detected"
grep -q "@nestjs/core" package.json && echo "Nest.js framework installed"
test -f tsconfig.json && echo "TypeScript configuration found"

# Detect Nest.js version
grep "@nestjs/core" package.json | sed 's/.*"\([0-9\.]*\)".*/Nest.js version: \1/'

# Check database setup
grep -q "@nestjs/typeorm" package.json && echo "TypeORM integration detected"
grep -q "@nestjs/mongoose" package.json && echo "Mongoose integration detected"
grep -q "@prisma/client" package.json && echo "Prisma ORM detected"

# Check authentication
grep -q "@nestjs/passport" package.json && echo "Passport authentication detected"
grep -q "@nestjs/jwt" package.json && echo "JWT authentication detected"

# Analyze module structure
find src -name "*.module.ts" -type f | head -5 | xargs -I {} basename {} .module.ts
```

**Safety note**: Avoid watch/serve processes; use one-shot diagnostics only.

### Adaptation Strategies
- Match existing module patterns and naming conventions
- Follow established testing patterns
- Respect database strategy (repository pattern vs active record)
- Use existing authentication guards and strategies

## Tool Integration

### Diagnostic Tools
```bash
# Analyze module dependencies
nest info

# Check for circular dependencies
npm run build -- --watch=false

# Validate module structure
npm run lint
```

### Fix Validation
```bash
# Verify fixes (validation order)
npm run build          # 1. Typecheck first
npm run test           # 2. Run unit tests
npm run test:e2e       # 3. Run e2e tests if needed
```

**Validation order**: typecheck → unit tests → integration tests → e2e tests

## Problem-Specific Approaches (Real Issues from GitHub & Stack Overflow)

### 1. "Nest can't resolve dependencies of the [Service] (?)"
**Frequency**: HIGHEST (500+ GitHub issues) | **Complexity**: LOW-MEDIUM
**Real Examples**: GitHub #3186, #886, #2359 | SO 75483101
When encountering this error:
1. Check if provider is in module's providers array
2. Verify module exports if crossing boundaries  
3. Check for typos in provider names (GitHub #598 - misleading error)
4. Review import order in barrel exports (GitHub #9095)

### 2. "Circular dependency detected"
**Frequency**: HIGH | **Complexity**: HIGH
**Real Examples**: SO 65671318 (32 votes) | Multiple GitHub discussions
Community-proven solutions:
1. Use forwardRef() on BOTH sides of the dependency
2. Extract shared logic to a third module (recommended)
3. Consider if circular dependency indicates design flaw
4. Note: Community warns forwardRef() can mask deeper issues

### 3. "Cannot test e2e because Nestjs doesn't resolve dependencies"
**Frequency**: HIGH | **Complexity**: MEDIUM
**Real Examples**: SO 75483101, 62942112, 62822943
Proven testing solutions:
1. Use @golevelup/ts-jest for createMock() helper
2. Mock JwtService in test module providers
3. Import all required modules in Test.createTestingModule()
4. For Bazel users: Special configuration needed (SO 62942112)

### 4. "[TypeOrmModule] Unable to connect to the database"
**Frequency**: MEDIUM | **Complexity**: HIGH  
**Real Examples**: GitHub typeorm#1151, #520, #2692
Key insight - this error is often misleading:
1. Check entity configuration - @Column() not @Column('description')
2. For multiple DBs: Use named connections (GitHub #2692)
3. Implement connection error handling to prevent app crash (#520)
4. SQLite: Verify database file path (typeorm#8745)

### 5. "Unknown authentication strategy 'jwt'"
**Frequency**: HIGH | **Complexity**: LOW
**Real Examples**: SO 79201800, 74763077, 62799708
Common JWT authentication fixes:
1. Import Strategy from 'passport-jwt' NOT 'passport-local'
2. Ensure JwtModule.secret matches JwtStrategy.secretOrKey
3. Check Bearer token format in Authorization header
4. Set JWT_SECRET environment variable

### 6. "ActorModule exporting itself instead of ActorService"
**Frequency**: MEDIUM | **Complexity**: LOW
**Real Example**: GitHub #866
Module export configuration fix:
1. Export the SERVICE not the MODULE from exports array
2. Common mistake: exports: [ActorModule] → exports: [ActorService]
3. Check all module exports for this pattern
4. Validate with nest info command

### 7. "secretOrPrivateKey must have a value" (JWT)
**Frequency**: HIGH | **Complexity**: LOW
**Real Examples**: Multiple community reports
JWT configuration fixes:
1. Set JWT_SECRET in environment variables
2. Check ConfigModule loads before JwtModule
3. Verify .env file is in correct location
4. Use ConfigService for dynamic configuration

### 8. Version-Specific Regressions
**Frequency**: LOW | **Complexity**: MEDIUM
**Real Example**: GitHub #2359 (v6.3.1 regression)
Handling version-specific bugs:
1. Check GitHub issues for your specific version
2. Try downgrading to previous stable version
3. Update to latest patch version
4. Report regressions with minimal reproduction

### 9. "Nest can't resolve dependencies of the UserController (?, +)"
**Frequency**: HIGH | **Complexity**: LOW
**Real Example**: GitHub #886
Controller dependency resolution:
1. The "?" indicates missing provider at that position
2. Count constructor parameters to identify which is missing
3. Add missing service to module providers
4. Check service is properly decorated with @Injectable()

### 10. "Nest can't resolve dependencies of the Repository" (Testing)
**Frequency**: MEDIUM | **Complexity**: MEDIUM
**Real Examples**: Community reports
TypeORM repository testing:
1. Use getRepositoryToken(Entity) for provider token
2. Mock DataSource in test module
3. Provide test database connection
4. Consider mocking repository completely

### 11. "Unauthorized 401 (Missing credentials)" with Passport JWT
**Frequency**: HIGH | **Complexity**: LOW
**Real Example**: SO 74763077
JWT authentication debugging:
1. Verify Authorization header format: "Bearer [token]"
2. Check token expiration (use longer exp for testing)
3. Test without nginx/proxy to isolate issue
4. Use jwt.io to decode and verify token structure

### 12. Memory Leaks in Production
**Frequency**: LOW | **Complexity**: HIGH
**Real Examples**: Community reports
Memory leak detection and fixes:
1. Profile with node --inspect and Chrome DevTools
2. Remove event listeners in onModuleDestroy()
3. Close database connections properly
4. Monitor heap snapshots over time

### 13. "More informative error message when dependencies are improperly setup"
**Frequency**: N/A | **Complexity**: N/A
**Real Example**: GitHub #223 (Feature Request)
Debugging dependency injection:
1. NestJS errors are intentionally generic for security
2. Use verbose logging during development
3. Add custom error messages in your providers
4. Consider using dependency injection debugging tools

### 14. Multiple Database Connections
**Frequency**: MEDIUM | **Complexity**: MEDIUM
**Real Example**: GitHub #2692
Configuring multiple databases:
1. Use named connections in TypeOrmModule
2. Specify connection name in @InjectRepository()
3. Configure separate connection options
4. Test each connection independently

### 15. "Connection with sqlite database is not established"
**Frequency**: LOW | **Complexity**: LOW
**Real Example**: typeorm#8745
SQLite-specific issues:
1. Check database file path is absolute
2. Ensure directory exists before connection
3. Verify file permissions
4. Use synchronize: true for development

### 16. Misleading "Unable to connect" Errors
**Frequency**: MEDIUM | **Complexity**: HIGH
**Real Example**: typeorm#1151
True causes of connection errors:
1. Entity syntax errors show as connection errors
2. Wrong decorator usage: @Column() not @Column('description')
3. Missing decorators on entity properties
4. Always check entity files when connection errors occur

### 17. "Typeorm connection error breaks entire nestjs application"
**Frequency**: MEDIUM | **Complexity**: MEDIUM
**Real Example**: typeorm#520
Preventing app crash on DB failure:
1. Wrap connection in try-catch in useFactory
2. Allow app to start without database
3. Implement health checks for DB status
4. Use retryAttempts and retryDelay options

## Common Patterns & Solutions

### Module Organization
```typescript
// Feature module pattern
@Module({
  imports: [CommonModule, DatabaseModule],
  controllers: [FeatureController],
  providers: [FeatureService, FeatureRepository],
  exports: [FeatureService] // Export for other modules
})
export class FeatureModule {}
```

### Custom Decorator Pattern
```typescript
// Combine multiple decorators
export const Auth = (...roles: Role[]) => 
  applyDecorators(
    UseGuards(JwtAuthGuard, RolesGuard),
    Roles(...roles),
  );
```

### Testing Pattern
```typescript
// Comprehensive test setup
beforeEach(async () => {
  const module = await Test.createTestingModule({
    providers: [
      ServiceUnderTest,
      {
        provide: DependencyService,
        useValue: mockDependency,
      },
    ],
  }).compile();
  
  service = module.get<ServiceUnderTest>(ServiceUnderTest);
});
```

### Exception Filter Pattern
```typescript
@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
  catch(exception: HttpException, host: ArgumentsHost) {
    // Custom error handling
  }
}
```

## Code Review Checklist

When reviewing Nest.js applications, focus on:

### Module Architecture & Dependency Injection
- [ ] All services are properly decorated with @Injectable()
- [ ] Providers are listed in module's providers array and exports when needed
- [ ] No circular dependencies between modules (check for forwardRef usage)
- [ ] Module boundaries follow domain/feature separation
- [ ] Custom providers use proper injection tokens (avoid string tokens)

### Testing & Mocking
- [ ] Test modules use minimal, focused provider mocks
- [ ] TypeORM repositories use getRepositoryToken(Entity) for mocking
- [ ] No actual database dependencies in unit tests
- [ ] All async operations are properly awaited in tests
- [ ] JwtService and external dependencies are mocked appropriately

### Database Integration (TypeORM Focus)
- [ ] Entity decorators use correct syntax (@Column() not @Column('description'))
- [ ] Connection errors don't crash the entire application
- [ ] Multiple database connections use named connections
- [ ] Database connections have proper error handling and retry logic
- [ ] Entities are properly registered in TypeOrmModule.forFeature()

### Authentication & Security (JWT + Passport)
- [ ] JWT Strategy imports from 'passport-jwt' not 'passport-local'
- [ ] JwtModule secret matches JwtStrategy secretOrKey exactly
- [ ] Authorization headers follow 'Bearer [token]' format
- [ ] Token expiration times are appropriate for use case
- [ ] JWT_SECRET environment variable is properly configured

### Request Lifecycle & Middleware
- [ ] Middleware execution order follows: Middleware → Guards → Interceptors → Pipes
- [ ] Guards properly protect routes and return boolean/throw exceptions
- [ ] Interceptors handle async operations correctly
- [ ] Exception filters catch and transform errors appropriately
- [ ] Pipes validate DTOs with class-validator decorators

### Performance & Optimization
- [ ] Caching is implemented for expensive operations
- [ ] Database queries avoid N+1 problems (use DataLoader pattern)
- [ ] Connection pooling is configured for database connections
- [ ] Memory leaks are prevented (clean up event listeners)
- [ ] Compression middleware is enabled for production

## Decision Trees for Architecture

### Choosing Database ORM
```
Project Requirements:
├─ Need migrations? → TypeORM or Prisma
├─ NoSQL database? → Mongoose
├─ Type safety priority? → Prisma
├─ Complex relations? → TypeORM
└─ Existing database? → TypeORM (better legacy support)
```

### Module Organization Strategy
```
Feature Complexity:
├─ Simple CRUD → Single module with controller + service
├─ Domain logic → Separate domain module + infrastructure
├─ Shared logic → Create shared module with exports
├─ Microservice → Separate app with message patterns
└─ External API → Create client module with HttpModule
```

### Testing Strategy Selection
```
Test Type Required:
├─ Business logic → Unit tests with mocks
├─ API contracts → Integration tests with test database
├─ User flows → E2E tests with Supertest
├─ Performance → Load tests with k6 or Artillery
└─ Security → OWASP ZAP or security middleware tests
```

### Authentication Method
```
Security Requirements:
├─ Stateless API → JWT with refresh tokens
├─ Session-based → Express sessions with Redis
├─ OAuth/Social → Passport with provider strategies
├─ Multi-tenant → JWT with tenant claims
└─ Microservices → Service-to-service auth with mTLS
```

### Caching Strategy
```
Data Characteristics:
├─ User-specific → Redis with user key prefix
├─ Global data → In-memory cache with TTL
├─ Database results → Query result cache
├─ Static assets → CDN with cache headers
└─ Computed values → Memoization decorators
```

## Performance Optimization

### Caching Strategies
- Use built-in cache manager for response caching
- Implement cache interceptors for expensive operations
- Configure TTL based on data volatility
- Use Redis for distributed caching

### Database Optimization
- Use DataLoader pattern for N+1 query problems
- Implement proper indexes on frequently queried fields
- Use query builder for complex queries vs. ORM methods
- Enable query logging in development for analysis

### Request Processing
- Implement compression middleware
- Use streaming for large responses
- Configure proper rate limiting
- Enable clustering for multi-core utilization

## External Resources

### Core Documentation
- [Nest.js Documentation](https://docs.nestjs.com)
- [Nest.js CLI](https://docs.nestjs.com/cli/overview)
- [Nest.js Recipes](https://docs.nestjs.com/recipes)

### Testing Resources
- [Jest Documentation](https://jestjs.io/docs/getting-started)
- [Supertest](https://github.com/visionmedia/supertest)
- [Testing Best Practices](https://github.com/goldbergyoni/javascript-testing-best-practices)

### Database Resources
- [TypeORM Documentation](https://typeorm.io)
- [Mongoose Documentation](https://mongoosejs.com)

### Authentication
- [Passport.js Strategies](http://www.passportjs.org)
- [JWT Best Practices](https://tools.ietf.org/html/rfc8725)

## Quick Reference Patterns

### Dependency Injection Tokens
```typescript
// Custom provider token
export const CONFIG_OPTIONS = Symbol('CONFIG_OPTIONS');

// Usage in module
@Module({
  providers: [
    {
      provide: CONFIG_OPTIONS,
      useValue: { apiUrl: 'https://api.example.com' }
    }
  ]
})
```

### Global Module Pattern
```typescript
@Global()
@Module({
  providers: [GlobalService],
  exports: [GlobalService],
})
export class GlobalModule {}
```

### Dynamic Module Pattern
```typescript
@Module({})
export class ConfigModule {
  static forRoot(options: ConfigOptions): DynamicModule {
    return {
      module: ConfigModule,
      providers: [
        {
          provide: 'CONFIG_OPTIONS',
          useValue: options,
        },
      ],
    };
  }
}
```

## Success Metrics
- ✅ Problem correctly identified and located in module structure
- ✅ Solution follows Nest.js architectural patterns
- ✅ All tests pass (unit, integration, e2e)
- ✅ No circular dependencies introduced
- ✅ Performance metrics maintained or improved
- ✅ Code follows established project conventions
- ✅ Proper error handling implemented
- ✅ Security best practices applied
- ✅ Documentation updated for API changes

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

---

## Imported Reference

---
name: new-rails-project
argument-hint: [project name]
description: Create a new Rails project
allowed-tools: Bash(rails *), Bash(bundle *), Bash(bin/*), Bash(npm *), Bash(yarn *)
context: fork
metadata:
  author: Shpigford
  version: "1.0"
---

Generate a new Rails project named $1 in the current directory. You may reference @AGENT_CONTEXT.md for general guidance, though the guidance here takes precedence.

# Tech Stack
Set up the following tech stack:
- **Rails ~8** with PostgreSQL - Server-side framework and database
- **Inertia.js ~2.3** - Bridges Rails and React for SPA-like experience without API
- **React ~19.2** - Frontend UI framework
- **Vite ~5** - JavaScript bundler with HMR
- **Tailwind CSS ~4** - Utility-first CSS framework
- **Sidekiq 8** - Background job processing with scheduled jobs via sidekiq-scheduler
- **Redis** - Sessions, caching, and job queue

# Rails guidance
- Do not use Kamal or Docker
- Do not use Rails "solid_*" components/systems
- Development should generally match production settings where possible
- Use Redis for caching

# Database
- All tables use UUID primary keys (pgcrypto extension)
- Timestamps use `timestamptz` for timezone awareness
- JSONB columns for flexible metadata storage
- Comprehensive indexing strategy for performance
- Encrypted fields for sensitive data (OAuth tokens, API keys)

# Background jobs
- Use Sidekiq 8 with Redis

# Testing
- Always use minitest
- Use `mocha` gem and VCR for external services (only in the providers layer)
- Prefer `OpenStruct` for mock instances
- Only mock what's necessary

# Code maintenace
- Run `bundle exec rubocop -a` after significant code changes
- Use `.rubocop.yml` for style configuration
- Security scanning with `bundle exec brakeman`

# Frontend
- All React components and views should be TSX

# General guidance
- Ask lots of clarifying questions when planning. The more the better. Make extensive use of AskUserQuestionTool to gather requirements and specifications. You can't ask too many questions.

# Verify
Verify the boilerplate is working by running `bin/rails server` and accessing the application at `http://localhost:3000` via playwright MCP.

---

## Imported Reference

---
name: odoo-accounting-setup
description: "Expert guide for configuring Odoo Accounting: chart of accounts, journals, fiscal positions, taxes, payment terms, and bank reconciliation."
risk: safe
source: "self"
---

# Odoo Accounting Setup

## Overview

This skill guides functional consultants and business owners through setting up Odoo Accounting correctly from scratch. It covers chart of accounts configuration, journal setup, tax rules, fiscal positions, payment terms, and the bank statement reconciliation workflow.

## When to Use This Skill

- Setting up a new Odoo instance for a company for the first time.
- Configuring multi-currency or multi-company accounting.
- Troubleshooting tax calculation or fiscal position mapping errors.
- Creating payment terms for installment billing (e.g., Net 30, 50% upfront).

## How It Works

1. **Activate**: Mention `@odoo-accounting-setup` and describe your accounting scenario.
2. **Configure**: Receive step-by-step Odoo menu navigation with exact field values.
3. **Validate**: Get a checklist to verify your setup is complete and correct.

## Examples

### Example 1: Create a Payment Term (Net 30 with 2% Early Pay Discount)

```text
Menu: Accounting → Configuration → Payment Terms → New

Name: Net 30 / 2% Early Pay Discount
Company: [Your Company]

Lines:
  Line 1:
    - Due Type: Percent
    - Value: 100%
    - Due: 30 days (full balance due in 30 days)

Early Payment Discount (Odoo 16+):
  Discount %: 2
  Discount Days: 10
  Balance Sheet Accounts:
    - Gain: 4900 Early Payment Discounts Granted
    - Loss: 5900 Early Payment Discounts Received
```

> **Note (v16+):** Use the built-in **Early Payment Discount** field instead of the old split-line workaround. Odoo now posts the discount automatically when the customer pays within the discount window and generates correct accounting entries.

### Example 2: Fiscal Position for EU VAT (B2B Intra-Community)

```text
Menu: Accounting → Configuration → Fiscal Positions → New

Name: EU Intra-Community B2B
Auto-detection: ON
  - Country Group: Europe
  - VAT Required: YES (customer must have EU VAT number)

Tax Mapping:
  Tax on Sales (21% VAT) → 0% Intra-Community VAT
  Tax on Purchases      → 0% Reverse Charge

Account Mapping:
  (Leave empty unless your localization requires account remapping)
```

### Example 3: Reconciliation Model for Bank Fees

```text
Menu: Accounting → Configuration → Reconciliation Models → New

Name: Bank Fee Auto-Match
Type: Write-off
Matching Order: 1

Conditions:
  - Label Contains: "BANK FEE" OR "SERVICE CHARGE"
  - Amount Type: Amount is lower than: $50.00

Action:
  - Account: 6200 Bank Charges
  - Tax: None
  - Analytic: Administrative
```

## Best Practices

- ✅ **Do:** Install your country's **localization module** first (`l10n_us`, `l10n_mx`, etc.) before manually creating accounts — it sets up the correct chart of accounts.
- ✅ **Do:** Use **Fiscal Positions** to automate B2B vs B2C tax switching — never change taxes manually on individual invoices.
- ✅ **Do:** Lock accounting periods (Accounting → Actions → Lock Dates) after month-end closing to prevent retroactive edits.
- ✅ **Do:** Use the **Early Payment Discount** feature (v16+) instead of splitting payment term lines for discount modelling.
- ❌ **Don't:** Delete journal entries — always reverse them with a credit note or the built-in reversal function.
- ❌ **Don't:** Mix personal and business transactions in the same journal.
- ❌ **Don't:** Create manual journal entries to fix bank reconciliation mismatches — use the reconciliation model workflow instead.

## Limitations

- Does not cover **multi-currency revaluation** or foreign exchange gain/loss accounting in depth.
- **Country-specific e-invoicing** (CFDI, FatturaPA, SAF-T) requires additional localization modules — use `@odoo-l10n-compliance` for those.
- Payroll accounting integration (salary journals, deduction accounts) is not covered here — use `@odoo-hr-payroll-setup`.
- Odoo Community Edition does not include the full **lock dates** feature; some controls are Enterprise-only.

---

## Imported Reference

---
name: odoo-automated-tests
description: "Write and run Odoo automated tests using TransactionCase, HttpCase, and browser tour tests. Covers test data setup, mocking, and CI integration."
risk: safe
source: "self"
---

# Odoo Automated Tests

## Overview

Odoo has a built-in testing framework based on Python's `unittest`. This skill helps you write `TransactionCase` unit tests, `HttpCase` integration tests, and JavaScript tour tests. It also covers running tests in CI pipelines.

## When to Use This Skill

- Writing unit tests for a custom model's business logic.
- Creating an HTTP test to verify a controller endpoint.
- Debugging test failures in a CI pipeline.
- Setting up automated test execution with `--test-enable`.

## How It Works

1. **Activate**: Mention `@odoo-automated-tests` and describe the feature to test.
2. **Generate**: Get complete test class code with setup, teardown, and assertions.
3. **Run**: Get the exact `odoo` CLI command to execute your tests.

## Examples

### Example 1: TransactionCase Unit Test (Odoo 15+ pattern)

```python
# tests/test_hospital_patient.py
from odoo.tests.common import TransactionCase
from odoo.tests import tagged
from odoo.exceptions import ValidationError

@tagged('post_install', '-at_install')
class TestHospitalPatient(TransactionCase):

    @classmethod
    def setUpClass(cls):
        # Use setUpClass for performance — runs once per class, not per test
        super().setUpClass()
        cls.Patient = cls.env['hospital.patient']
        cls.doctor = cls.env['res.users'].browse(cls.env.uid)

    def test_create_patient(self):
        patient = self.Patient.create({
            'name': 'John Doe',
            'doctor_id': self.doctor.id,
        })
        self.assertEqual(patient.state, 'draft')
        self.assertEqual(patient.name, 'John Doe')

    def test_confirm_patient(self):
        patient = self.Patient.create({'name': 'Jane Smith'})
        patient.action_confirm()
        self.assertEqual(patient.state, 'confirmed')

    def test_empty_name_raises_error(self):
        with self.assertRaises(ValidationError):
            self.Patient.create({'name': ''})

    def test_access_denied_for_other_user(self):
        # Test security rules by running as a different user
        other_user = self.env.ref('base.user_demo')
        with self.assertRaises(Exception):
            self.Patient.with_user(other_user).create({'name': 'Test'})
```

> **`setUpClass` vs `setUp`:** Use `setUpClass` (Odoo 15+) for shared test data. It runs once per class and is significantly faster than `setUp` which re-initializes for every single test method.

### Example 2: Run Tests via CLI

```bash
# Run all tests for a specific module
./odoo-bin --test-enable --stop-after-init -d my_database -u hospital_management

# Run only tests tagged with a specific tag
./odoo-bin --test-enable --stop-after-init -d my_database \
  --test-tags hospital_management

# Run a specific test class
./odoo-bin --test-enable --stop-after-init -d my_database \
  --test-tags /hospital_management:TestHospitalPatient
```

### Example 3: HttpCase for Controller Testing

```python
from odoo.tests.common import HttpCase
from odoo.tests import tagged

@tagged('post_install', '-at_install')
class TestPatientController(HttpCase):

    def test_patient_page_authenticated(self):
        # Authenticate as a user, not with hardcoded password
        self.authenticate(self.env.user.login, self.env.user.login)
        resp = self.url_open('/hospital/patients')
        self.assertEqual(resp.status_code, 200)

    def test_patient_page_redirects_unauthenticated(self):
        # No authenticate() call = public/anonymous user
        resp = self.url_open('/hospital/patients', allow_redirects=False)
        self.assertIn(resp.status_code, [301, 302, 403])
```

## Best Practices

- ✅ **Do:** Use `setUpClass()` with `cls.env` instead of `setUp()` — it is dramatically faster for large test suites.
- ✅ **Do:** Use `@tagged('post_install', '-at_install')` to run tests after all modules are installed.
- ✅ **Do:** Test both the happy path and error conditions (`ValidationError`, `AccessError`, `UserError`).
- ✅ **Do:** Use `self.with_user(user)` to test access control without calling `sudo()`.
- ❌ **Don't:** Use a production database for tests — always use a dedicated test database.
- ❌ **Don't:** Rely on test execution order — each `TransactionCase` test is rolled back in isolation.
- ❌ **Don't:** Hardcode passwords in `HttpCase.authenticate()` — use `self.env.user.login` or a fixture user.

## Limitations

- **JavaScript tour tests** require a running browser (via `phantomjs` or `Chrome headless`) and a live Odoo server — not covered in depth here.
- `HttpCase` tests are significantly slower than `TransactionCase` — use them only for controller/route verification.
- Does not cover **mocking external services** (e.g., mocking an SMTP server or payment gateway in tests).
- Test isolation is at the **transaction level**, not database level — tests that commit data (e.g., via `cr.commit()`) can leak state between tests.

---

## Imported Reference

---
name: odoo-backup-strategy
description: "Complete Odoo backup and restore strategy: database dumps, filestore backup, automated scheduling, cloud storage upload, and tested restore procedures."
risk: safe
source: "self"
---

# Odoo Backup Strategy

## Overview

A complete Odoo backup must include both the **PostgreSQL database** and the **filestore** (attachments, images). This skill covers manual and automated backup procedures, offsite storage, and the correct restore sequence to bring a down Odoo instance back online.

## When to Use This Skill

- Setting up a backup strategy for a production Odoo instance.
- Automating daily backups with shell scripts and cron.
- Restoring Odoo after a server failure or data corruption event.
- Diagnosing a failed backup or corrupt restore.

## How It Works

1. **Activate**: Mention `@odoo-backup-strategy` and describe your server environment.
2. **Generate**: Receive a complete backup script tailored to your setup.
3. **Restore**: Get step-by-step restore instructions for any failure scenario.

## Examples

### Example 1: Manual Database + Filestore Backup

```bash
#!/bin/bash
# backup_odoo.sh

DATE=$(date +%Y%m%d_%H%M%S)
DB_NAME="odoo"
DB_USER="odoo"
FILESTORE_PATH="/var/lib/odoo/.local/share/Odoo/filestore/$DB_NAME"
BACKUP_DIR="/backups/odoo"

mkdir -p "$BACKUP_DIR"

# Step 1: Dump the database
pg_dump -U $DB_USER -Fc $DB_NAME > "$BACKUP_DIR/db_$DATE.dump"

# Step 2: Archive the filestore
tar -czf "$BACKUP_DIR/filestore_$DATE.tar.gz" -C "$FILESTORE_PATH" .

echo "✅ Backup complete: db_$DATE.dump + filestore_$DATE.tar.gz"
```

### Example 2: Automate with Cron (daily at 2 AM)

```bash
# Run: crontab -e
# Add this line:
0 2 * * * /opt/scripts/backup_odoo.sh >> /var/log/odoo_backup.log 2>&1
```

### Example 3: Upload to S3 (after backup)

```bash
# Add to backup script after tar command:
aws s3 cp "$BACKUP_DIR/db_$DATE.dump"        s3://my-odoo-backups/db/
aws s3 cp "$BACKUP_DIR/filestore_$DATE.tar.gz" s3://my-odoo-backups/filestore/

# Optional: Delete local backups older than 7 days
find "$BACKUP_DIR" -type f -mtime +7 -delete
```

### Example 4: Full Restore Procedure

```bash
# Step 1: Stop Odoo
docker compose stop odoo  # or: systemctl stop odoo

# Step 2: Recreate and restore the database
# (--clean alone fails if the DB doesn't exist; drop and recreate first)
dropdb -U odoo odoo 2>/dev/null || true
createdb -U odoo odoo
pg_restore -U odoo -d odoo db_YYYYMMDD_HHMMSS.dump

# Step 3: Restore the filestore
FILESTORE=/var/lib/odoo/.local/share/Odoo/filestore/odoo
rm -rf "$FILESTORE"/*
tar -xzf filestore_YYYYMMDD_HHMMSS.tar.gz -C "$FILESTORE"/

# Step 4: Restart Odoo
docker compose start odoo

# Step 5: Verify — open Odoo in the browser and check:
#   - Can you log in?
#   - Are recent records visible?
#   - Are file attachments loading?
```

## Best Practices

- ✅ **Do:** Test restores monthly in a staging environment — a backup you've never restored is not a backup.
- ✅ **Do:** Follow the **3-2-1 rule**: 3 copies, 2 different media types, 1 offsite copy (e.g., S3 or a remote server).
- ✅ **Do:** Back up **immediately before every Odoo upgrade** — this is your rollback point.
- ✅ **Do:** Verify backup integrity: `pg_restore --list backup.dump` should complete without errors.
- ❌ **Don't:** Back up only the database without the filestore — all attachments and images will be missing after a restore.
- ❌ **Don't:** Store backups on the same disk or same server as Odoo — a disk or server failure destroys both.
- ❌ **Don't:** Run `pg_restore --clean` against a non-existent database — always create the database first.

## Limitations

- Does not cover **Odoo.sh built-in backups** — Odoo.sh has its own backup system accessible from the dashboard.
- This script assumes a **single-database** Odoo setup. Multi-database instances require looping over all databases.
- Filestore path may differ between installations (Docker volume vs. bare-metal). Always verify the path with `odoo-bin shell` before running a restore.
- Large filestores (100GB+) may require incremental backup tools like `rsync` or `restic` rather than full `tar.gz` archives.

---

## Imported Reference

---
name: odoo-ecommerce-configurator
description: "Expert guide for Odoo eCommerce and Website: product catalog, payment providers, shipping methods, SEO, and order-to-fulfillment workflow."
risk: safe
source: "self"
---

# Odoo eCommerce Configurator

## Overview

This skill helps you set up and optimize an Odoo-powered online store. It covers product publishing, payment gateway integration, shipping carrier configuration, cart and checkout customization, and the workflow from online order to warehouse fulfillment.

## When to Use This Skill

- Launching an Odoo eCommerce store for the first time.
- Integrating a payment provider (Stripe, PayPal, Adyen).
- Configuring shipping rates with carrier integration (UPS, FedEx, DHL).
- Optimizing product pages for SEO with Odoo Website tools.

## How It Works

1. **Activate**: Mention `@odoo-ecommerce-configurator` and describe your store scenario.
2. **Configure**: Receive step-by-step Odoo eCommerce setup with menu paths.
3. **Optimize**: Get SEO, conversion, and catalog best practices.

## Examples

### Example 1: Publish a Product to the Website

```text
Menu: Website → eCommerce → Products → Select Product

Fields to complete for a great product listing:
  Name:               Ergonomic Mesh Office Chair  (keyword-rich)
  Internal Reference: CHAIR-MESH-001               (required for inventory)
  Sales Price:        $299.00
  Website Description (website tab): 150–300 words of unique content

Publishing:
  Toggle "Published" in the top-right corner of the product form
  or via: Website → Go to Website → Toggle "Published" button

SEO (website tab → SEO section):
  Page Title:       Ergonomic Mesh Chair | Office Chairs | YourStore
  Meta Description: Discover the most comfortable ergonomic mesh office
                    chair, designed for all-day support...  (≤160 chars)

Website tab:
  Can be Sold: YES
  Website:     yourstore.com  (if running multiple websites)
```

### Example 2: Configure Stripe Payment Provider

```text
Menu: Website → Configuration → Payment Providers → Stripe → Configure
(or: Accounting → Configuration → Payment Providers → Stripe)

State: Test  (use Test mode until fully validated, then switch to Enabled)

Credentials (from your Stripe Dashboard → Developers → API Keys):
  Publishable Key: pk_live_XXXXXXXX
  Secret Key:      sk_live_XXXXXXXX  (store securely; never expose client-side)

Payment Journal: Bank (USD)
Capture Mode:    Automatic  (charge card immediately on order confirmation)
                 or Manual  (authorize only; charge later on fulfillment)

Webhook:
  Add Odoo's webhook URL in Stripe Dashboard → Webhooks
  URL: https://yourstore.com/payment/stripe/webhook
  Events: payment_intent.succeeded, payment_intent.payment_failed
```

### Example 3: Set Up Flat Rate Shipping with Free Threshold

```text
Menu: Inventory → Configuration → Delivery Methods → New

Name: Standard Shipping (3–5 business days)
Provider: Fixed Price
Delivery Product: [Shipping] Standard  (used for invoicing)

Pricing:
  Price: $9.99
  ☑ Free if order amount is above: $75.00

Availability:
  Countries: United States
  States: All states

Publish to website:
  ☑ Published  (visible to customers at checkout)
```

### Example 4: Set Up Abandoned Cart Recovery

```text
Menu: Email Marketing → Mailing Lists → (create a list if needed)

For automated abandoned cart emails in Odoo 16/17:
Menu: Marketing → Marketing Automation → New Campaign

Trigger: Odoo record updated
Model: eCommerce Cart (sale.order with state = 'draft')
Filter: Cart not updated in 1 hour AND not confirmed

Actions:
  1. Wait 1 hour
  2. Send Email: "You left something behind!"  (use a recovery email template)
  3. Wait 24 hours
  4. Send Email: "Last chance — items selling fast"

Note: Some Odoo hosting plans may require "Email Marketing" app enabled.
```

## Best Practices

- ✅ **Do:** Use **Product Variants** (color, size) instead of duplicate products — cleaner catalog and shared inventory tracking.
- ✅ **Do:** Enable **HTTPS** (SSL certificate) via your hosting provider and set HSTS in Website → Settings → Security.
- ✅ **Do:** Set up **Abandoned Cart Recovery** using Marketing Automation or a scheduled email sequence.
- ✅ **Do:** Add a **Stripe webhook** so Odoo is notified of payment events in real time — without it, failed payments may not update correctly.
- ❌ **Don't:** Leave the payment provider in **Test mode** in production — no real charges will be processed.
- ❌ **Don't:** Publish products without an **Internal Reference (SKU)** — it breaks inventory tracking and order fulfillment.
- ❌ **Don't:** Use the same Stripe key for Test and Production environments — always rotate to live keys before going live.

## Limitations

- **Carrier integration** (live UPS/FedEx rate calculation) requires the specific carrier connector module (e.g., `delivery_ups`) and a carrier account API key.
- Does not cover **multi-website** configuration — running separate storefronts with different pricelists and languages requires Enterprise.
- **B2B eCommerce** (customer login required, custom catalog and prices per customer) has additional configuration steps not fully covered here.
- Odoo eCommerce does not support **subscription billing** natively — that requires the Enterprise **Subscriptions** module.

---

## Imported Reference

---
name: odoo-edi-connector
description: "Guide for implementing EDI (Electronic Data Interchange) with Odoo: X12, EDIFACT document mapping, partner onboarding, and automated order processing."
---

# Odoo EDI Connector

## Overview

Electronic Data Interchange (EDI) is the standard for automated B2B document exchange — purchase orders, invoices, ASNs (Advance Shipping Notices). This skill guides you through mapping EDI transactions (ANSI X12 or EDIFACT) to Odoo business objects, setting up trading partner configurations, and automating inbound/outbound document flows.

## When to Use This Skill

- A retail partner requires EDI 850 (Purchase Orders) to do business with you.
- You need to send EDI 856 (ASN) when goods are shipped.
- Automating EDI 810 (Invoice) generation from Odoo confirmed deliveries.
- Mapping EDI fields to Odoo fields for a new trading partner.

## How It Works

1. **Activate**: Mention `@odoo-edi-connector` and specify the EDI transaction set and trading partner.
2. **Map**: Receive a complete field mapping table between EDI segments and Odoo fields.
3. **Automate**: Get Python code to parse incoming EDI files and create Odoo records.

## EDI ↔ Odoo Object Mapping

| EDI Transaction | Odoo Object |
|---|---|
| 850 Purchase Order | `sale.order` (inbound customer PO) |
| 855 PO Acknowledgment | Confirmation email / SO confirmation |
| 856 ASN (Advance Ship Notice) | `stock.picking` (delivery order) |
| 810 Invoice | `account.move` (customer invoice) |
| 846 Inventory Inquiry | `product.product` stock levels |
| 997 Functional Acknowledgment | Automated receipt confirmation |

## Examples

### Example 1: Parse EDI 850 and Create Odoo Sale Order (Python)

```python
from pyx12 import x12file  # pip install pyx12

import xmlrpc.client

odoo_url = "https://myodoo.example.com"
db, uid, pwd = "my_db", 2, "api_key"
models = xmlrpc.client.ServerProxy(f"{odoo_url}/xmlrpc/2/object")

def process_850(edi_file_path):
    """Parse X12 850 Purchase Order and create Odoo Sale Order"""
    with x12file.X12File(edi_file_path) as f:
        for transaction in f.get_transaction_sets():
            # Extract header info (BEG segment)
            po_number = transaction['BEG'][3]    # Purchase Order Number
            po_date   = transaction['BEG'][5]    # Purchase Order Date

            # Extract partner (N1 segment — Buyer)
            partner_name = transaction['N1'][2]

            # Find partner in Odoo
            partner = models.execute_kw(db, uid, pwd, 'res.partner', 'search',
                [[['name', 'ilike', partner_name]]])
            partner_id = partner[0] if partner else False

            # Extract line items (PO1 segments)
            order_lines = []
            for po1 in transaction.get_segments('PO1'):
                sku     = po1[7]    # Product ID
                qty     = float(po1[2])
                price   = float(po1[4])

                product = models.execute_kw(db, uid, pwd, 'product.product', 'search',
                    [[['default_code', '=', sku]]])
                if product:
                    order_lines.append((0, 0, {
                        'product_id': product[0],
                        'product_uom_qty': qty,
                        'price_unit': price,
                    }))

            # Create Sale Order
            if partner_id and order_lines:
                models.execute_kw(db, uid, pwd, 'sale.order', 'create', [{
                    'partner_id': partner_id,
                    'client_order_ref': po_number,
                    'order_line': order_lines,
                }])
```

### Example 2: Send EDI 997 Acknowledgment

```python
def generate_997(isa_control, gs_control, transaction_control):
    """Generate a functional acknowledgment for received EDI"""
    return f"""ISA*00*          *00*          *ZZ*YOURISAID      *ZZ*PARTNERISAID   *{today}*1200*^*00501*{isa_control}*0*P*>~
GS*FA*YOURGID*PARTNERGID*{today}*1200*{gs_control}*X*005010X231A1~
ST*997*0001~
AK1*PO*{gs_control}~
AK9*A*1*1*1~
SE*4*0001~
GE*1*{gs_control}~
IEA*1*{isa_control}~"""
```

## Best Practices

- ✅ **Do:** Store every raw EDI transaction in an audit log table before processing.
- ✅ **Do:** Always send a **997 Functional Acknowledgment** within 24 hours of receiving a transaction.
- ✅ **Do:** Negotiate a test cycle with trading partners before going live — use test ISA qualifier `T`.
- ❌ **Don't:** Process EDI files synchronously in web requests — queue them for async processing.
- ❌ **Don't:** Hardcode trading partner qualifiers — store them in a configuration table per partner.

---

## Imported Reference

---
name: odoo-module-developer
description: "Expert guide for creating custom Odoo modules. Covers __manifest__.py, model inheritance, ORM patterns, and module structure best practices."
risk: safe
source: "self"
---

# Odoo Module Developer

## Overview

This skill transforms your AI into an expert Odoo custom module developer. It guides you through scaffolding new modules, defining models, setting up security, and following Odoo's coding conventions for both Community and Enterprise editions.

## When to Use This Skill

- Starting a new custom Odoo module from scratch.
- Extending an existing Odoo model (e.g., adding fields to `sale.order`).
- Troubleshooting module loading errors or manifest issues.
- Implementing `onchange`, `compute`, and `constraint` methods correctly.

## How It Works

1. **Activate**: Mention `@odoo-module-developer` and describe the module you want to build.
2. **Scaffold**: Get the full folder structure with `__manifest__.py`, `__init__.py`, models, views, and security files.
3. **Review**: Paste existing code and get a review against Odoo best practices.

## Examples

### Example 1: Scaffold a New Module

**Prompt:** `Create a module called "hospital_management" with a model for patients`

**Generated structure:**

```text
hospital_management/
├── __manifest__.py
├── __init__.py
├── models/
│   ├── __init__.py
│   └── hospital_patient.py
├── views/
│   └── hospital_patient_views.xml
├── security/
│   ├── ir.model.access.csv
│   └── security.xml
└── data/
```

**`__manifest__.py`:**

```python
{
    'name': 'Hospital Management',
    'version': '17.0.1.0.0',
    'category': 'Healthcare',
    'depends': ['base', 'mail'],
    'data': [
        'security/ir.model.access.csv',
        'views/hospital_patient_views.xml',
    ],
    'installable': True,
    'license': 'LGPL-3',
}
```

**`models/hospital_patient.py`:**

```python
from odoo import models, fields, api

class HospitalPatient(models.Model):
    _name = 'hospital.patient'
    _description = 'Hospital Patient'
    _inherit = ['mail.thread', 'mail.activity.mixin']

    name = fields.Char(string='Patient Name', required=True, tracking=True)
    birth_date = fields.Date(string='Birth Date')
    doctor_id = fields.Many2one('res.users', string='Assigned Doctor')
    state = fields.Selection([
        ('draft', 'New'),
        ('confirmed', 'Confirmed'),
        ('done', 'Done'),
    ], default='draft', tracking=True)
```

## Best Practices

- ✅ **Do:** Always prefix your model `_name` with a namespace (e.g., `hospital.patient`).
- ✅ **Do:** Use `_inherit = ['mail.thread']` to add chatter/logging automatically.
- ✅ **Do:** Specify `version` in manifest as `{odoo_version}.{major}.{minor}.{patch}`.
- ✅ **Do:** Set `'author'` and `'website'` in `__manifest__.py` so your module is identifiable in the Apps list.
- ❌ **Don't:** Modify core Odoo model files directly — always use `_inherit`.
- ❌ **Don't:** Forget to add new models to `ir.model.access.csv` or users will get access errors.
- ❌ **Don't:** Use spaces or uppercase in folder names — Odoo requires snake_case module names.

## Limitations

- Does not cover **OWL JavaScript components** or frontend widget development — use `@odoo-xml-views-builder` for view XML.
- **Odoo 13 and below** have a different module structure (no `__manifest__.py` auto-loading) — this skill targets v14+.
- Does not cover **multi-company** or **multi-website** configuration; those require additional model fields (`company_id`, `website_id`).
- Does not generate automated test files — use `@odoo-automated-tests` for that.

---

## Imported Reference

---
name: odoo-orm-expert
description: "Master Odoo ORM patterns: search, browse, create, write, domain filters, computed fields, and performance-safe query techniques."
risk: safe
source: "self"
---

# Odoo ORM Expert

## Overview

This skill teaches you Odoo's Object Relational Mapper (ORM) in depth. It covers reading/writing records, building domain filters, working with relational fields, and avoiding common performance pitfalls like N+1 queries.

## When to Use This Skill

- Writing `search()`, `browse()`, `create()`, `write()`, or `unlink()` calls.
- Building complex domain filters for views or server actions.
- Implementing computed, stored, and related fields.
- Debugging slow queries or optimizing bulk operations.

## How It Works

1. **Activate**: Mention `@odoo-orm-expert` and describe what data operation you need.
2. **Get Code**: Receive correct, idiomatic Odoo ORM code with explanations.
3. **Optimize**: Ask for performance review on existing ORM code.

## Examples

### Example 1: Search with Domain Filters

```python
# Find all confirmed sale orders for a specific customer, created this year
import datetime

start_of_year = datetime.date.today().replace(month=1, day=1).strftime('%Y-%m-%d')

orders = self.env['sale.order'].search([
    ('partner_id', '=', partner_id),
    ('state', '=', 'sale'),
    ('date_order', '>=', start_of_year),
], order='date_order desc', limit=50)

# Note: pass dates as 'YYYY-MM-DD' strings in domains,
# NOT as fields.Date objects — the ORM serializes them correctly.
```

### Example 2: Computed Field

```python
total_order_count = fields.Integer(
    string='Total Orders',
    compute='_compute_total_order_count',
    store=True
)

@api.depends('sale_order_ids')
def _compute_total_order_count(self):
    for record in self:
        record.total_order_count = len(record.sale_order_ids)
```

### Example 3: Safe Bulk Write (avoid N+1)

```python
# ✅ GOOD: One query for all records
partners = self.env['res.partner'].search([('country_id', '=', False)])
partners.write({'country_id': self.env.ref('base.us').id})

# ❌ BAD: Triggers a separate query per record
for partner in partners:
    partner.country_id = self.env.ref('base.us').id
```

## Best Practices

- ✅ **Do:** Use `mapped()`, `filtered()`, and `sorted()` on recordsets instead of Python loops.
- ✅ **Do:** Use `sudo()` sparingly and only when you understand the security implications.
- ✅ **Do:** Prefer `search_count()` over `len(search(...))` when you only need a count.
- ✅ **Do:** Use `with_context(...)` to pass context values cleanly rather than modifying `self.env.context` directly.
- ❌ **Don't:** Call `search()` inside a loop — this is the #1 Odoo performance killer.
- ❌ **Don't:** Use raw SQL unless absolutely necessary; use ORM for all standard operations.
- ❌ **Don't:** Pass Python `datetime`/`date` objects directly into domain tuples — always stringify them as `'YYYY-MM-DD'`.

## Limitations

- Does not cover **`cr.execute()` raw SQL** patterns in depth — use the Odoo performance tuner skill for SQL-level optimization.
- **Stored computed fields** can cause significant write overhead at scale; this skill does not cover partitioning strategies.
- Does not cover **transient models** (`models.TransientModel`) or wizard patterns.
- ORM behavior can differ slightly between Odoo SaaS and On-Premise due to config overrides.

---

## Imported Reference

---
name: odoo-project-timesheet
description: "Expert guide for Odoo Project and Timesheets: task stages, billable time tracking, timesheet approval, budget alerts, and invoicing from timesheets."
risk: safe
source: "self"
---

# Odoo Project & Timesheet

## Overview

This skill helps you configure Odoo Project and Timesheets for service businesses, agencies, and consulting firms. It covers project setup with budgets, task stage management, employee timesheet logging, approval workflows, and converting approved timesheet hours to customer invoices.

## When to Use This Skill

- Setting up a new project with tasks, deadlines, and team assignments.
- Configuring billable vs. non-billable time tracking per project.
- Creating a timesheet approval workflow for managers.
- Invoicing customers based on logged hours (Time & Materials billing).

## How It Works

1. **Activate**: Mention `@odoo-project-timesheet` and describe your project or billing scenario.
2. **Configure**: Receive step-by-step setup instructions.
3. **Automate**: Get guidance on automatically generating invoices from approved timesheets.

## Examples

### Example 1: Create a Billable Project

```text
Menu: Project → New Project (or the "+" button in Project view)

Name:     Website Redesign — Acme Corp
Customer: Acme Corporation
Billable: YES  (toggle ON)

Settings tab:
  Billing Type: Based on Timesheets (Time & Materials)
  Service Product: Consulting Hours ($150/hr)
  ☑ Timesheets
  ☑ Task Dependencies
  ☑ Subtasks

Budget:
  Planned Hours: 120 hours
  Budget Alert: at 80% (96 hrs) → notify project manager
```

### Example 2: Log Time on a Task

```text
Method A — Directly inside the Task (recommended for accuracy):
  Open Task → Timesheets tab → Add a Line
  Employee:    John Doe
  Date:        Today
  Description: "Initial wireframes and site map" (required for clear invoices)
  Duration:    3:30  (3 hours 30 minutes)

Method B — Timesheets app (for end-of-day bulk entry):
  Menu: Timesheets → My Timesheets → New
  Project:  Website Redesign
  Task:     Wireframe Design
  Duration: 3:30
```

### Example 3: Enable Timesheet Approval Before Invoicing

```text
Menu: Timesheets → Configuration → Settings
  ☑ Timesheet Approval  (employees submit; managers approve)

Approval flow:
  1. Employee submits timesheet at week/month end
  2. Manager reviews: Timesheets → Managers → Timesheets to Approve
  3. Manager clicks "Approve" → entries are locked and billable
  4. Only approved entries flow into the invoice

If Approval is disabled, all logged hours are immediately billable.
```

### Example 4: Invoice from Timesheets

```text
Step 1: Verify approved hours
  Menu: Timesheets → Managers → All Timesheets
  Filter: Billable = YES, Timesheet Invoice State = "To Invoice"

Step 2: Generate Invoice
  Menu: Sales → Orders → To Invoice → Timesheets  (v15/v16)
  or:   Accounting → Customers → Invoiceable Time  (v17)
  Filter by Customer: Acme Corporation
  Select entries → Create Invoices

Step 3: Invoice pre-populates with:
  Product: Consulting Hours
  Quantity: Sum of approved hours
  Unit Price: $150.00
  Total: Calculated automatically
```

## Best Practices

- ✅ **Do:** Enable **Timesheet Approval** so only manager-approved hours appear on customer invoices.
- ✅ **Do:** Set a **budget alert** at 80% of planned hours so PMs can intervene before overruns.
- ✅ **Do:** Require **timesheet descriptions** — vague entries like "Work done" on invoices destroy client trust.
- ✅ **Do:** Use **Subtasks** to break work into granular pieces while keeping the parent task on the Kanban board.
- ❌ **Don't:** Mix billable and internal projects without tagging — it corrupts profitability and utilization reports.
- ❌ **Don't:** Log time on the Project itself (without a Task) — it cannot be reported at the task level.

## Limitations

- **Timesheet Approval** is an Enterprise-only feature in some Odoo versions — verify your plan includes it.
- Does not cover **Project Forecast** (resource capacity planning) — that requires the Enterprise Forecast app.
- **Time & Materials** invoicing works well for hourly billing but is not suited for **fixed-price projects** — use milestones or manual invoice lines for those.
- Timesheet entries logged outside an active project-task pair (e.g., on internal projects) are not assignable to customer invoices without custom configuration.

---

## Imported Reference

---
name: odoo-purchase-workflow
description: "Expert guide for Odoo Purchase: RFQ → PO → Receipt → Vendor Bill workflow, purchase agreements, vendor price lists, and 3-way matching."
risk: safe
source: "self"
---

# Odoo Purchase Workflow

## Overview

This skill guides you through the complete Odoo Purchase workflow — from sending a Request for Quotation (RFQ) to receiving goods and matching the vendor bill. It also covers purchase agreements, vendor price lists on products, automated reordering, and 3-way matching controls.

## When to Use This Skill

- Setting up the purchase flow for a new Odoo instance.
- Implementing purchase order approval workflows (2-level approval).
- Configuring vendor price lists with quantity-based discounts.
- Troubleshooting billing/receipt mismatches in 3-way matching.

## How It Works

1. **Activate**: Mention `@odoo-purchase-workflow` and describe your purchasing scenario.
2. **Configure**: Receive exact Odoo menu paths and field-by-field configuration.
3. **Troubleshoot**: Describe a billing or receiving issue and get a root cause diagnosis.

## Examples

### Example 1: Standard RFQ → PO → Receipt → Bill Flow

```text
Step 1: Create RFQ
  Menu: Purchase → Orders → Requests for Quotation → New
  Vendor: Acme Supplies
  Add product lines with quantity and unit price

Step 2: Send RFQ to Vendor
  Click "Send by Email" → Vendor receives PDF with RFQ details

Step 3: Confirm as Purchase Order
  Click "Confirm Order" → Status changes to "Purchase Order"

Step 4: Receive Goods
  Click "Receive Products" → Validate received quantities
  (partial receipts are supported; PO stays open for remaining qty)

Step 5: Match Vendor Bill (3-Way Match)
  Click "Create Bill" → Bill pre-filled from PO quantities
  Verify: PO qty = Received qty = Billed qty
  Post Bill → Register Payment
```

### Example 2: Enable 2-Level Purchase Approval

```text
Menu: Purchase → Configuration → Settings

Purchase Order Approval:
  ☑ Purchase Order Approval
  Minimum Order Amount: $5,000

Result:
  Orders ≤ $5,000  → Confirm directly to PO
  Orders > $5,000  → Status: "Waiting for Approval"
                     A purchase manager must click "Approve"
```

### Example 3: Vendor Price List (Quantity Breaks on a Product)

```text
Vendor price lists are configured per product, not as a global menu.

Menu: Inventory → Products → [Select Product] → Purchase Tab
  → Vendor Pricelist section → Add a line

Vendor: Acme Supplies
Currency: USD
Price:    $12.00
Min. Qty: 1

Add another line for quantity discount:
Min. Qty: 100 → Price: $10.50   (12.5% discount)
Min. Qty: 500 → Price:  $9.00   (25% discount)

Result: Odoo automatically selects the right price on a PO
based on the ordered quantity for this vendor.
```

## Best Practices

- ✅ **Do:** Enable **Purchase Order Approval** for orders above your company's approval threshold.
- ✅ **Do:** Use **Purchase Agreements (Blanket Orders)** for recurring vendors with pre-negotiated annual contracts.
- ✅ **Do:** Set a **vendor lead time** on products (Purchase tab) so Odoo can schedule arrival dates accurately.
- ✅ **Do:** Set the **Bill Control** policy to "Based on received quantities" (not ordered qty) for accurate 3-way matching.
- ❌ **Don't:** Confirm a PO before prices are agreed — use Draft/RFQ status to negotiate first.
- ❌ **Don't:** Post a vendor bill without linking it to a receipt — bypassing 3-way matching creates accounting discrepancies.
- ❌ **Don't:** Delete a PO that has received quantities — archive it instead to preserve the stock and accounting trail.

## Limitations

- Does not cover **subcontracting purchase flows** — those require the Manufacturing module and subcontracting BoM type.
- **EDI-based order exchange** (automated PO import/export) requires custom integration — use `@odoo-edi-connector` for that.
- Vendor pricelist currency conversion depends on the active **currency rate** in Odoo; rates must be kept current for accuracy.
- The **2-level approval** is a binary threshold; more complex approval matrices (department-based, multi-tier) require custom development or the Approvals app.

---

## Imported Reference

---
name: odoo-qweb-templates
description: "Expert in Odoo QWeb templating for PDF reports, email templates, and website pages. Covers t-if, t-foreach, t-field, and report actions."
risk: safe
source: "self"
---

# Odoo QWeb Templates

## Overview

QWeb is Odoo's primary templating engine, used for PDF reports, website pages, and email templates. This skill generates correct, well-structured QWeb XML with proper directives, translation support, and report action bindings.

## When to Use This Skill

- Creating a custom PDF report (invoice, delivery slip, certificate).
- Building a QWeb email template triggered by workflow actions.
- Designing Odoo website pages with dynamic content.
- Debugging QWeb rendering errors (`t-if`, `t-foreach` issues).

## How It Works

1. **Activate**: Mention `@odoo-qweb-templates` and describe the report or template needed.
2. **Generate**: Receive a complete `ir.actions.report` record and QWeb template.
3. **Debug**: Paste a broken template to identify and fix rendering issues.

## Examples

### Example 1: Custom PDF Report

```xml
<!-- Report Action -->
<record id="action_report_patient_card" model="ir.actions.report">
    <field name="name">Patient Card</field>
    <field name="model">hospital.patient</field>
    <field name="report_type">qweb-pdf</field>
    <field name="report_name">hospital_management.report_patient_card</field>
    <field name="binding_model_id" ref="model_hospital_patient"/>
</record>

<!-- QWeb Template -->
<template id="report_patient_card">
    <t t-call="web.html_container">
        <t t-foreach="docs" t-as="doc">
            <t t-call="web.external_layout">
                <div class="page">
                    <h2>Patient Card</h2>
                    <table class="table table-bordered">
                        <tr>
                            <td><strong>Name:</strong></td>
                            <td><t t-field="doc.name"/></td>
                        </tr>
                        <tr>
                            <td><strong>Doctor:</strong></td>
                            <td><t t-field="doc.doctor_id.name"/></td>
                        </tr>
                        <tr>
                            <td><strong>Status:</strong></td>
                            <td><t t-field="doc.state"/></td>
                        </tr>
                    </table>
                </div>
            </t>
        </t>
    </t>
</template>
```

### Example 2: Conditional Rendering

```xml
<!-- Show a warning block only if the patient is not confirmed -->
<t t-if="doc.state == 'draft'">
    <div class="alert alert-warning">
        <strong>Warning:</strong> This patient has not been confirmed yet.
    </div>
</t>
```

## Best Practices

- ✅ **Do:** Use `t-field` for model fields — Odoo auto-formats dates, monetary values, and booleans correctly.
- ✅ **Do:** Use `t-out` (Odoo 15+) for safe HTML output of non-field strings. Use `t-esc` only on Odoo 14 and below (it HTML-escapes output).
- ✅ **Do:** Call `web.external_layout` for PDF reports to automatically include the company header, footer, and logo.
- ✅ **Do:** Use `_lt()` (lazy translation) for translatable string literals inside Python report helpers, not inline `t-esc`.
- ❌ **Don't:** Use raw Python expressions inside QWeb — compute values in the model or a report `_get_report_values()` helper.
- ❌ **Don't:** Forget `t-as` when using `t-foreach`; without it, you can't access the current record in the loop body.
- ❌ **Don't:** Use `t-esc` where you intend to render HTML content — it will escape the tags and print them as raw text.

## Limitations

- Does not cover **website controller routing** for dynamic QWeb pages — that requires Python `http.route` knowledge.
- **Email template** QWeb has different variable scope than report QWeb (`object` vs `docs`) — this skill primarily focuses on PDF reports.
- QWeb JavaScript (used in Kanban/Form widgets) is a different engine; this skill covers **server-side QWeb only**.
- Does not cover **wkhtmltopdf configuration** for PDF rendering issues (page size, margins, header/footer overlap).

---

## Imported Reference

---
name: odoo-sales-crm-expert
description: "Expert guide for Odoo Sales and CRM: pipeline stages, quotation templates, pricelists, sales teams, lead scoring, and forecasting."
risk: safe
source: "self"
---

# Odoo Sales & CRM Expert

## Overview

This skill helps you configure and optimize Odoo Sales and CRM. It covers opportunity pipeline setup, automated lead assignment, quotation templates, pricelist strategies, sales team management, and the sales-to-invoice workflow.

## When to Use This Skill

- Designing CRM pipeline stages for your sales process.
- Creating a quotation template with optional products and bundles.
- Setting up pricelists with customer-tier pricing.
- Configuring automated lead assignment by territory or salesperson.

## How It Works

1. **Activate**: Mention `@odoo-sales-crm-expert` and describe your sales scenario.
2. **Configure**: Receive step-by-step Odoo setup instructions.
3. **Optimize**: Get recommendations for improving pipeline velocity and deal closure rate.

## Examples

### Example 1: Configure CRM Pipeline Stages

```text
Menu: CRM → Configuration → Stages → New

Typical B2B Pipeline:
  Stage 1: New Lead          (probability: 10%)
  Stage 2: Qualified         (probability: 25%)
  Stage 3: Proposal Sent     (probability: 50%)
  Stage 4: Negotiation       (probability: 75%)
  Stage 5: Won               (is_won: YES — marks opportunity as closed-won)
  Stage 6: Lost              (mark as lost via the "Mark as Lost" button)

Tips:
  - Enable "Rotting Days" in CRM Settings to flag stale deals in red
  - In Odoo 16+, Predictive Lead Scoring (AI) auto-updates probability
    based on historical data. Disable it in Settings if you prefer manual
    stage-based probability.
```

### Example 2: Create a Quotation Template

```text
Menu: Sales → Configuration → Quotation Templates → New
(Requires the "Sales Management" module — enabled in Sales Settings)

Template Name: SaaS Annual Subscription
Valid for: 30 days

Lines:
  1. Platform License   | Qty: 1 | Price: $1,200/yr | (required)
  2. Onboarding Package | Qty: 1 | Price: $500       | Optional
  3. Premium Support    | Qty: 1 | Price: $300/yr    | Optional
  4. Extra User License | Qty: 0 | Price: $120/user  | Optional

Signature & Payment:
  ☑ Online Signature required before order confirmation
  ☑ Online Payment (deposit) — 50% upfront

Notes section:
  "Prices valid until expiration date. Subject to Schedule A terms."
```

### Example 3: Customer Tier Pricelist (VIP Discount)

```text
Menu: Sales → Configuration → Settings
  ☑ Enable Pricelists

Menu: Sales → Configuration → Pricelists → New

Name: VIP Customer — 15% Off
Currency: USD
Discount Policy: Show public price & discount on quotation

Rules:
  Apply To: All Products
  Compute Price: Discount
  Discount: 15%
  Min. Quantity: 1

Assign to a customer:
  Customer record → Sales & Purchase tab → Pricelist → VIP Customer
```

## Best Practices

- ✅ **Do:** Use **Lost Reasons** (CRM → Configuration → Lost Reasons) to build a dataset of why deals are lost — invaluable for sales coaching.
- ✅ **Do:** Enable **Sales Teams** with revenue targets so pipeline forecasting is meaningful per team.
- ✅ **Do:** Set **Expected Revenue** and **Closing Date** on every opportunity — these feed the revenue forecast dashboard.
- ✅ **Do:** Use **Quotation Templates** to standardize offers and reduce quoting time across the team.
- ❌ **Don't:** Skip the CRM opportunity when selling — going directly from lead to invoice breaks pipeline analytics.
- ❌ **Don't:** Manually edit prices on quotation lines as a workaround — set up proper pricelists instead.
- ❌ **Don't:** Ignore the **Predictive Lead Scoring** feature in v16+ — configure it with historical data for accurate forecasting.

## Limitations

- **Commission rules** are not built into Odoo CRM out of the box — they require custom development or third-party modules.
- The **Quotation Template** optional product feature requires the **Sale Management** module; it is not available in the base `sale` module.
- **Territory-based lead assignment** (geographic routing) requires custom rules or the Enterprise Leads module.
- Odoo CRM does not have native **email sequence / cadence** automation — use the **Email Marketing** or **Marketing Automation** modules for drip campaigns.

---

## Imported Reference

---
name: odoo-upgrade-advisor
description: "Step-by-step Odoo version upgrade advisor: pre-upgrade checklist, community vs enterprise upgrade path, OCA module compatibility, and post-upgrade validation."
risk: safe
source: "self"
---

# Odoo Upgrade Advisor

## Overview

Upgrading Odoo between major versions (e.g., v15 → v16 → v17) requires careful preparation, testing, and validation. This skill provides a structured pre-upgrade checklist, guides you through the upgrade tools (Odoo Upgrade Service and OpenUpgrade), and gives you a post-upgrade validation protocol.

## When to Use This Skill

- Planning a major Odoo version upgrade.
- Identifying which custom modules need to be migrated.
- Running the upgrade on a staging environment before production.
- Validating the system after an upgrade.

## How It Works

1. **Activate**: Mention `@odoo-upgrade-advisor`, state your current and target version.
2. **Plan**: Receive the full upgrade roadmap and risk assessment.
3. **Execute**: Get a step-by-step upgrade command sequence.

## Upgrade Paths

| From | To | Supported? | Tool |
|---|---|---|---|
| v16 | v17 | ✅ Direct | Odoo Upgrade Service / OpenUpgrade |
| v15 | v16 | ✅ Direct | Odoo Upgrade Service / OpenUpgrade |
| v14 | v15 | ✅ Direct | Odoo Upgrade Service / OpenUpgrade |
| v14 | v17 | ⚠️ Multi-hop | v14→v15→v16→v17 (cannot skip) |
| v13 or older | any | ❌ Not supported | Manual migration required |

## Examples

### Example 1: Pre-Upgrade Checklist

```text
BEFORE YOU START:
  ☑ 1. List all installed modules (Settings → Technical → Modules)
        Export to CSV and review for custom/OCA modules
  ☑ 2. Check OCA compatibility matrix for each community module
        https://github.com/OCA/maintainer-tools/wiki/Migration-Status
  ☑ 3. Take a full backup (database + filestore) — your restore point
  ☑ 4. Clone production to a staging environment
  ☑ 5. Run the Odoo Upgrade pre-analysis:
        https://upgrade.odoo.com/ → Upload DB → Review breaking changes report
  ☑ 6. Review custom modules against migration notes
        (use @odoo-migration-helper for per-module analysis)
  ☑ 7. Upgrade and test in staging → Fix all errors → Re-test
  ☑ 8. Schedule a production maintenance window
  ☑ 9. Notify users of scheduled downtime
  ☑ 10. Perform production upgrade → Validate → Go/No-Go decision
```

### Example 2: Community Upgrade with OpenUpgrade

```bash
# Clone OpenUpgrade for the TARGET version (e.g., upgrading to v17)
git clone https://github.com/OCA/OpenUpgrade.git \
  --branch 17.0 \
  --single-branch \
  /opt/openupgrade

# Run the migration against your staging database
python3 /opt/openupgrade/odoo-bin \
  --update all \
  --database odoo_staging \
  --config /etc/odoo/odoo.conf \
  --stop-after-init \
  --load openupgrade_framework

# Review the log for errors before touching production
tail -200 /var/log/odoo/odoo.log | grep -E "ERROR|WARNING|Traceback"
```

### Example 3: Post-Upgrade Validation Checklist

```text
After upgrading, validate these critical areas before going live:

Accounting:
  ☑ Trial Balance totals match the pre-upgrade snapshot
  ☑ Open invoices, bills, and payments are accessible
  ☑ Bank reconciliation can be performed on a test statement

Inventory:
  ☑ Stock valuation report matches pre-upgrade (run Inventory Valuation)
  ☑ Open Purchase Orders and Sale Orders are visible

HR / Payroll:
  ☑ All employee records are intact
  ☑ Payslips from the last 3 months are accessible and correct

Custom Modules:
  ☑ Every custom module loaded without ImportError or XML error
  ☑ Run the critical business workflows end-to-end:
      Create sale order → confirm → deliver → invoice → payment

Users & Security:
  ☑ User logins work correctly
  ☑ Access rights are preserved (spot-check 3-5 users)
```

## Best Practices

- ✅ **Do:** Always upgrade on a **copy of production** (staging) first — never the live instance.
- ✅ **Do:** Keep the old version running until the new version is **fully validated and signed off**.
- ✅ **Do:** Check OCA's migration status page: [OCA Migration Status](https://github.com/OCA/maintainer-tools/wiki/Migration-Status)
- ✅ **Do:** Use the [Odoo Upgrade Service](https://upgrade.odoo.com/) pre-analysis report to get a list of breaking changes **before writing any code**.
- ❌ **Don't:** Skip intermediate versions — Odoo requires sequential upgrades (v14→v15→v16→v17).
- ❌ **Don't:** Upgrade custom modules and Odoo core simultaneously — adapt Odoo core first, then fix custom modules.
- ❌ **Don't:** Run OpenUpgrade against production directly — always test on a staging copy first.

## Limitations

- Covers **v14–v17** only. Versions v13 and older have a fundamentally different module structure and require manual migration.
- **Enterprise-exclusive module changes** (e.g., `sign`, `account_accountant`) may have undocumented breaking changes not included in OpenUpgrade.
- The **Odoo.sh** automated upgrade path has a separate workflow (managed from the Odoo.sh dashboard) not covered here.
- OWL JavaScript component migration (legacy widget → OWL v16+) is a complex front-end topic beyond the scope of this skill.

---

## Imported Reference

---
name: odoo-xml-views-builder
description: "Expert at building Odoo XML views: Form, List, Kanban, Search, Calendar, and Graph. Generates correct XML for Odoo 14-17 with proper visibility syntax."
risk: safe
source: "self"
---

# Odoo XML Views Builder

## Overview

This skill generates and reviews Odoo XML view definitions for Kanban, Form, List, Search, Calendar, and Graph views. It understands visibility modifiers, `groups`, `domain`, `context`, and widget usage across Odoo versions 14–17, including the migration from `attrs` (v14–16) to inline expressions (v17+).

## When to Use This Skill

- Creating a new form or list view for a custom model.
- Adding fields, tabs, or smart buttons to an existing view.
- Building a Kanban view with color coding or progress bars.
- Creating a search view with filters and group-by options.

## How It Works

1. **Activate**: Mention `@odoo-xml-views-builder` and describe the view you want.
2. **Generate**: Get complete, ready-to-paste XML view definitions.
3. **Review**: Paste existing XML and get fixes for common mistakes.

## Examples

### Example 1: Form View with Tabs

```xml
<record id="view_hospital_patient_form" model="ir.ui.view">
    <field name="name">hospital.patient.form</field>
    <field name="model">hospital.patient</field>
    <field name="arch" type="xml">
        <form string="Patient">
            <header>
                <button name="action_confirm" string="Confirm"
                    type="object" class="btn-primary"
                    invisible="state != 'draft'"/>
                <field name="state" widget="statusbar"
                    statusbar_visible="draft,confirmed,done"/>
            </header>
            <sheet>
                <div class="oe_title">
                    <h1><field name="name" placeholder="Patient Name"/></h1>
                </div>
                <notebook>
                    <page string="General Info">
                        <group>
                            <field name="birth_date"/>
                            <field name="doctor_id"/>
                        </group>
                    </page>
                </notebook>
            </sheet>
            <chatter/>
        </form>
    </field>
</record>
```

### Example 2: Kanban View

```xml
<record id="view_hospital_patient_kanban" model="ir.ui.view">
    <field name="name">hospital.patient.kanban</field>
    <field name="model">hospital.patient</field>
    <field name="arch" type="xml">
        <kanban default_group_by="state" class="o_kanban_small_column">
            <field name="name"/>
            <field name="state"/>
            <field name="doctor_id"/>
            <templates>
                <t t-name="kanban-card">
                    <div class="oe_kanban_content">
                        <strong><field name="name"/></strong>
                        <div>Doctor: <field name="doctor_id"/></div>
                    </div>
                </t>
            </templates>
        </kanban>
    </field>
</record>
```

## Best Practices

- ✅ **Do:** Use inline `invisible="condition"` (Odoo 17+) instead of `attrs` for show/hide logic.
- ✅ **Do:** Use `attrs="{'invisible': [...]}"` only if you are targeting Odoo 14–16 — it is deprecated in v17.
- ✅ **Do:** Always set a `string` attribute on your view record for debugging clarity.
- ✅ **Do:** Use `<chatter/>` (v17) or `<div class="oe_chatter">` + field tags (v16 and below) for activity tracking.
- ❌ **Don't:** Use `attrs` in Odoo 17 — it is fully deprecated and raises warnings in logs.
- ❌ **Don't:** Put business logic in view XML — keep it in Python model methods.
- ❌ **Don't:** Use hardcoded `domain` strings in views when a `domain` field on the model can be used dynamically.

## Limitations

- Does not cover **OWL JavaScript widgets** or client-side component development.
- **Search panel views** (`<searchpanel>`) are not fully covered — those require frontend knowledge.
- Does not address **website QWeb views** — use `@odoo-qweb-templates` for those.
- **Cohort and Map views** (Enterprise-only) are not covered by this skill.

---

## Imported Reference

---
name: paypal-integration
description: "Integrate PayPal payment processing with support for express checkout, subscriptions, and refund management. Use when implementing PayPal payments, processing online transactions, or building e-com..."
risk: unknown
source: community
date_added: "2026-02-27"
---

# PayPal Integration

Master PayPal payment integration including Express Checkout, IPN handling, recurring billing, and refund workflows.

## Do not use this skill when

- The task is unrelated to paypal integration
- You need a different domain or tool outside this scope

## Instructions

- Clarify goals, constraints, and required inputs.
- Apply relevant best practices and validate outcomes.
- Provide actionable steps and verification.
- If detailed examples are required, open `resources/implementation-playbook.md`.

## Use this skill when

- Integrating PayPal as a payment option
- Implementing express checkout flows
- Setting up recurring billing with PayPal
- Processing refunds and payment disputes
- Handling PayPal webhooks (IPN)
- Supporting international payments
- Implementing PayPal subscriptions

## Core Concepts

### 1. Payment Products
**PayPal Checkout**
- One-time payments
- Express checkout experience
- Guest and PayPal account payments

**PayPal Subscriptions**
- Recurring billing
- Subscription plans
- Automatic renewals

**PayPal Payouts**
- Send money to multiple recipients
- Marketplace and platform payments

### 2. Integration Methods
**Client-Side (JavaScript SDK)**
- Smart Payment Buttons
- Hosted payment flow
- Minimal backend code

**Server-Side (REST API)**
- Full control over payment flow
- Custom checkout UI
- Advanced features

### 3. IPN (Instant Payment Notification)
- Webhook-like payment notifications
- Asynchronous payment updates
- Verification required

## Quick Start

```javascript
// Frontend - PayPal Smart Buttons
<div id="paypal-button-container"></div>

<script src="https://www.paypal.com/sdk/js?client-id=YOUR_CLIENT_ID&currency=USD"></script>
<script>
  paypal.Buttons({
    createOrder: function(data, actions) {
      return actions.order.create({
        purchase_units: [{
          amount: {
            value: '25.00'
          }
        }]
      });
    },
    onApprove: function(data, actions) {
      return actions.order.capture().then(function(details) {
        // Payment successful
        console.log('Transaction completed by ' + details.payer.name.given_name);

        // Send to backend for verification
        fetch('/api/paypal/capture', {
          method: 'POST',
          headers: {'Content-Type': 'application/json'},
          body: JSON.stringify({orderID: data.orderID})
        });
      });
    }
  }).render('#paypal-button-container');
</script>
```

```python
# Backend - Verify and capture order
from paypalrestsdk import Payment
import paypalrestsdk

paypalrestsdk.configure({
    "mode": "sandbox",  # or "live"
    "client_id": "YOUR_CLIENT_ID",
    "client_secret": "YOUR_CLIENT_SECRET"
})

def capture_paypal_order(order_id):
    """Capture a PayPal order."""
    payment = Payment.find(order_id)

    if payment.execute({"payer_id": payment.payer.payer_info.payer_id}):
        # Payment successful
        return {
            'status': 'success',
            'transaction_id': payment.id,
            'amount': payment.transactions[0].amount.total
        }
    else:
        # Payment failed
        return {
            'status': 'failed',
            'error': payment.error
        }
```

## Express Checkout Implementation

### Server-Side Order Creation
```python
import requests
import json

class PayPalClient:
    def __init__(self, client_id, client_secret, mode='sandbox'):
        self.client_id = client_id
        self.client_secret = client_secret
        self.base_url = 'https://api-m.sandbox.paypal.com' if mode == 'sandbox' else 'https://api-m.paypal.com'
        self.access_token = self.get_access_token()

    def get_access_token(self):
        """Get OAuth access token."""
        url = f"{self.base_url}/v1/oauth2/token"
        headers = {"Accept": "application/json", "Accept-Language": "en_US"}

        response = requests.post(
            url,
            headers=headers,
            data={"grant_type": "client_credentials"},
            auth=(self.client_id, self.client_secret)
        )

        return response.json()['access_token']

    def create_order(self, amount, currency='USD'):
        """Create a PayPal order."""
        url = f"{self.base_url}/v2/checkout/orders"
        headers = {
            "Content-Type": "application/json",
            "Authorization": f"Bearer {self.access_token}"
        }

        payload = {
            "intent": "CAPTURE",
            "purchase_units": [{
                "amount": {
                    "currency_code": currency,
                    "value": str(amount)
                }
            }]
        }

        response = requests.post(url, headers=headers, json=payload)
        return response.json()

    def capture_order(self, order_id):
        """Capture payment for an order."""
        url = f"{self.base_url}/v2/checkout/orders/{order_id}/capture"
        headers = {
            "Content-Type": "application/json",
            "Authorization": f"Bearer {self.access_token}"
        }

        response = requests.post(url, headers=headers)
        return response.json()

    def get_order_details(self, order_id):
        """Get order details."""
        url = f"{self.base_url}/v2/checkout/orders/{order_id}"
        headers = {
            "Authorization": f"Bearer {self.access_token}"
        }

        response = requests.get(url, headers=headers)
        return response.json()
```

## IPN (Instant Payment Notification) Handling

### IPN Verification and Processing
```python
from flask import Flask, request
import requests
from urllib.parse import parse_qs

app = Flask(__name__)

@app.route('/ipn', methods=['POST'])
def handle_ipn():
    """Handle PayPal IPN notifications."""
    # Get IPN message
    ipn_data = request.form.to_dict()

    # Verify IPN with PayPal
    if not verify_ipn(ipn_data):
        return 'IPN verification failed', 400

    # Process IPN based on transaction type
    payment_status = ipn_data.get('payment_status')
    txn_type = ipn_data.get('txn_type')

    if payment_status == 'Completed':
        handle_payment_completed(ipn_data)
    elif payment_status == 'Refunded':
        handle_refund(ipn_data)
    elif payment_status == 'Reversed':
        handle_chargeback(ipn_data)

    return 'IPN processed', 200

def verify_ipn(ipn_data):
    """Verify IPN message authenticity."""
    # Add 'cmd' parameter
    verify_data = ipn_data.copy()
    verify_data['cmd'] = '_notify-validate'

    # Send back to PayPal for verification
    paypal_url = 'https://ipnpb.sandbox.paypal.com/cgi-bin/webscr'  # or production URL

    response = requests.post(paypal_url, data=verify_data)

    return response.text == 'VERIFIED'

def handle_payment_completed(ipn_data):
    """Process completed payment."""
    txn_id = ipn_data.get('txn_id')
    payer_email = ipn_data.get('payer_email')
    mc_gross = ipn_data.get('mc_gross')
    item_name = ipn_data.get('item_name')

    # Check if already processed (prevent duplicates)
    if is_transaction_processed(txn_id):
        return

    # Update database
    # Send confirmation email
    # Fulfill order
    print(f"Payment completed: {txn_id}, Amount: ${mc_gross}")

def handle_refund(ipn_data):
    """Handle refund."""
    parent_txn_id = ipn_data.get('parent_txn_id')
    mc_gross = ipn_data.get('mc_gross')

    # Process refund in your system
    print(f"Refund processed: {parent_txn_id}, Amount: ${mc_gross}")

def handle_chargeback(ipn_data):
    """Handle payment reversal/chargeback."""
    txn_id = ipn_data.get('txn_id')
    reason_code = ipn_data.get('reason_code')

    # Handle chargeback
    print(f"Chargeback: {txn_id}, Reason: {reason_code}")
```

## Subscription/Recurring Billing

### Create Subscription Plan
```python
def create_subscription_plan(name, amount, interval='MONTH'):
    """Create a subscription plan."""
    client = PayPalClient(CLIENT_ID, CLIENT_SECRET)

    url = f"{client.base_url}/v1/billing/plans"
    headers = {
        "Content-Type": "application/json",
        "Authorization": f"Bearer {client.access_token}"
    }

    payload = {
        "product_id": "PRODUCT_ID",  # Create product first
        "name": name,
        "billing_cycles": [{
            "frequency": {
                "interval_unit": interval,
                "interval_count": 1
            },
            "tenure_type": "REGULAR",
            "sequence": 1,
            "total_cycles": 0,  # Infinite
            "pricing_scheme": {
                "fixed_price": {
                    "value": str(amount),
                    "currency_code": "USD"
                }
            }
        }],
        "payment_preferences": {
            "auto_bill_outstanding": True,
            "setup_fee": {
                "value": "0",
                "currency_code": "USD"
            },
            "setup_fee_failure_action": "CONTINUE",
            "payment_failure_threshold": 3
        }
    }

    response = requests.post(url, headers=headers, json=payload)
    return response.json()

def create_subscription(plan_id, subscriber_email):
    """Create a subscription for a customer."""
    client = PayPalClient(CLIENT_ID, CLIENT_SECRET)

    url = f"{client.base_url}/v1/billing/subscriptions"
    headers = {
        "Content-Type": "application/json",
        "Authorization": f"Bearer {client.access_token}"
    }

    payload = {
        "plan_id": plan_id,
        "subscriber": {
            "email_address": subscriber_email
        },
        "application_context": {
            "return_url": "https://yourdomain.com/subscription/success",
            "cancel_url": "https://yourdomain.com/subscription/cancel"
        }
    }

    response = requests.post(url, headers=headers, json=payload)
    subscription = response.json()

    # Get approval URL
    for link in subscription.get('links', []):
        if link['rel'] == 'approve':
            return {
                'subscription_id': subscription['id'],
                'approval_url': link['href']
            }
```

## Refund Workflows

```python
def create_refund(capture_id, amount=None, note=None):
    """Create a refund for a captured payment."""
    client = PayPalClient(CLIENT_ID, CLIENT_SECRET)

    url = f"{client.base_url}/v2/payments/captures/{capture_id}/refund"
    headers = {
        "Content-Type": "application/json",
        "Authorization": f"Bearer {client.access_token}"
    }

    payload = {}
    if amount:
        payload["amount"] = {
            "value": str(amount),
            "currency_code": "USD"
        }

    if note:
        payload["note_to_payer"] = note

    response = requests.post(url, headers=headers, json=payload)
    return response.json()

def get_refund_details(refund_id):
    """Get refund details."""
    client = PayPalClient(CLIENT_ID, CLIENT_SECRET)

    url = f"{client.base_url}/v2/payments/refunds/{refund_id}"
    headers = {
        "Authorization": f"Bearer {client.access_token}"
    }

    response = requests.get(url, headers=headers)
    return response.json()
```

## Error Handling

```python
class PayPalError(Exception):
    """Custom PayPal error."""
    pass

def handle_paypal_api_call(api_function):
    """Wrapper for PayPal API calls with error handling."""
    try:
        result = api_function()
        return result
    except requests.exceptions.RequestException as e:
        # Network error
        raise PayPalError(f"Network error: {str(e)}")
    except Exception as e:
        # Other errors
        raise PayPalError(f"PayPal API error: {str(e)}")

# Usage
try:
    order = handle_paypal_api_call(lambda: client.create_order(25.00))
except PayPalError as e:
    # Handle error appropriately
    log_error(e)
```

## Testing

```python
# Use sandbox credentials
SANDBOX_CLIENT_ID = "..."
SANDBOX_SECRET = "..."

# Test accounts
# Create test buyer and seller accounts at developer.paypal.com

def test_payment_flow():
    """Test complete payment flow."""
    client = PayPalClient(SANDBOX_CLIENT_ID, SANDBOX_SECRET, mode='sandbox')

    # Create order
    order = client.create_order(10.00)
    assert 'id' in order

    # Get approval URL
    approval_url = next((link['href'] for link in order['links'] if link['rel'] == 'approve'), None)
    assert approval_url is not None

    # After approval (manual step with test account)
    # Capture order
    # captured = client.capture_order(order['id'])
    # assert captured['status'] == 'COMPLETED'
```

## Resources

- **references/express-checkout.md**: Express Checkout implementation guide
- **references/ipn-handling.md**: IPN verification and processing
- **references/refund-workflows.md**: Refund handling patterns
- **references/billing-agreements.md**: Recurring billing setup
- **assets/paypal-client.py**: Production PayPal client
- **assets/ipn-processor.py**: IPN webhook processor
- **assets/recurring-billing.py**: Subscription management

## Best Practices

1. **Always Verify IPN**: Never trust IPN without verification
2. **Idempotent Processing**: Handle duplicate IPN notifications
3. **Error Handling**: Implement robust error handling
4. **Logging**: Log all transactions and errors
5. **Test Thoroughly**: Use sandbox extensively
6. **Webhook Backup**: Don't rely solely on client-side callbacks
7. **Currency Handling**: Always specify currency explicitly

## Common Pitfalls

- **Not Verifying IPN**: Accepting IPN without verification
- **Duplicate Processing**: Not checking for duplicate transactions
- **Wrong Environment**: Mixing sandbox and production URLs/credentials
- **Missing Webhooks**: Not handling all payment states
- **Hardcoded Values**: Not making configurable for different environments

---

## Imported Reference

---
name: plaid-fintech
description: "Expert patterns for Plaid API integration including Link token flows, transactions sync, identity verification, Auth for ACH, balance checks, webhook handling, and fintech compliance best practices..."
risk: unknown
source: "vibeship-spawner-skills (Apache 2.0)"
date_added: "2026-02-27"
---

# Plaid Fintech

## Patterns

### Link Token Creation and Exchange

Create a link_token for Plaid Link, exchange public_token for access_token.
Link tokens are short-lived, one-time use. Access tokens don't expire but
may need updating when users change passwords.


### Transactions Sync

Use /transactions/sync for incremental transaction updates. More efficient
than /transactions/get. Handle webhooks for real-time updates instead of
polling.


### Item Error Handling and Update Mode

Handle ITEM_LOGIN_REQUIRED errors by putting users through Link update mode.
Listen for PENDING_DISCONNECT webhook to proactively prompt users.


## Anti-Patterns

### ❌ Storing Access Tokens in Plain Text

### ❌ Polling Instead of Webhooks

### ❌ Ignoring Item Errors

## ⚠️ Sharp Edges

| Issue | Severity | Solution |
|-------|----------|----------|
| Issue | critical | See docs |
| Issue | high | See docs |
| Issue | high | See docs |
| Issue | high | See docs |
| Issue | medium | See docs |
| Issue | medium | See docs |
| Issue | medium | See docs |
| Issue | medium | See docs |

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

---

## Imported Reference

---
name: rails-upgrade
description: Rails Upgrade
---

404: Not Found

---

## Imported Reference

---
name: skill-rails-upgrade
description: "Analyze Rails apps and provide upgrade assessments"
risk: safe
source: "https://github.com/robzolkos/skill-rails-upgrade"
date_added: "2026-02-27"
---

## When to Use This Skill

Analyze Rails apps and provide upgrade assessments

Use this skill when working with analyze rails apps and provide upgrade assessments.
# Rails Upgrade Analyzer

Analyze the current Rails application and provide a comprehensive upgrade assessment with selective file merging.

## Step 1: Verify Rails Application

Check that we're in a Rails application by looking for these files:
- `Gemfile` (must exist and contain 'rails')
- `config/application.rb` (Rails application config)
- `config/environment.rb` (Rails environment)

If any of these are missing or don't indicate a Rails app, stop and inform the user this doesn't appear to be a Rails application.

## Step 2: Get Current Rails Version

Extract the current Rails version from:
1. First, check `Gemfile.lock` for the exact installed version (look for `rails (x.y.z)`)
2. If not found, check `Gemfile` for the version constraint

Report the exact current version (e.g., `7.1.3`).

## Step 3: Find Latest Rails Version

Use the GitHub CLI to fetch the latest Rails release:

```bash
gh api repos/rails/rails/releases/latest --jq '.tag_name'
```

This returns the latest stable version tag (e.g., `v8.0.1`). Strip the 'v' prefix for comparison.

Also check recent tags to understand the release landscape:

```bash
gh api repos/rails/rails/tags --jq '.[0:10] | .[].name'
```

## Step 4: Determine Upgrade Type

Compare current and latest versions to classify the upgrade:

- **Patch upgrade**: Same major.minor, different patch (e.g., 7.1.3 → 7.1.5)
- **Minor upgrade**: Same major, different minor (e.g., 7.1.3 → 7.2.0)
- **Major upgrade**: Different major version (e.g., 7.1.3 → 8.0.0)

## Step 5: Fetch Upgrade Guide

Use WebFetch to get the official Rails upgrade guide:

URL: `https://guides.rubyonrails.org/upgrading_ruby_on_rails.html`

Look for sections relevant to the version jump. The guide is organized by target version with sections like:
- "Upgrading from Rails X.Y to Rails X.Z"
- Breaking changes
- Deprecation warnings
- Configuration changes
- Required migrations

Extract and summarize the relevant sections for the user's specific upgrade path.

## Step 6: Fetch Rails Diff

Use WebFetch to get the diff between versions from railsdiff.org:

URL: `https://railsdiff.org/{current_version}/{target_version}`

For example: `https://railsdiff.org/7.1.3/8.0.0`

This shows:
- Changes to default configuration files
- New files that need to be added
- Modified initializers
- Updated dependencies
- Changes to bin/ scripts

Summarize the key file changes.

## Step 7: Check JavaScript Dependencies

Rails applications often include JavaScript packages that should be updated alongside Rails. Check for and report on these dependencies.

### 7.1: Identify JS Package Manager

Check which package manager the app uses:

```bash
# Check for package.json (npm/yarn)
ls package.json 2>/dev/null

# Check for importmap (Rails 7+)
ls config/importmap.rb 2>/dev/null
```

### 7.2: Check Rails-Related JS Packages

If `package.json` exists, check for these Rails-related packages:

```bash
# Extract current versions of Rails-related packages
cat package.json | grep -E '"@hotwired/|"@rails/|"stimulus"|"turbo-rails"' || echo "No Rails JS packages found"
```

**Key packages to check:**

| Package | Purpose | Version Alignment |
|---------|---------|-------------------|
| `@hotwired/turbo-rails` | Turbo Drive/Frames/Streams | Should match Rails version era |
| `@hotwired/stimulus` | Stimulus JS framework | Generally stable across Rails versions |
| `@rails/actioncable` | WebSocket support | Should match Rails version |
| `@rails/activestorage` | Direct uploads | Should match Rails version |
| `@rails/actiontext` | Rich text editing | Should match Rails version |
| `@rails/request.js` | Rails UJS replacement | Should match Rails version era |

### 7.3: Check for Updates

For npm/yarn projects, check for available updates:

```bash
# Using npm
npm outdated @hotwired/turbo-rails @hotwired/stimulus @rails/actioncable @rails/activestorage 2>/dev/null

# Or check latest versions directly
npm view @hotwired/turbo-rails version 2>/dev/null
npm view @rails/actioncable version 2>/dev/null
```

### 7.4: Check Importmap Pins (if applicable)

If the app uses importmap-rails, check `config/importmap.rb` for pinned versions:

```bash
cat config/importmap.rb | grep -E 'pin.*turbo|pin.*stimulus|pin.*@rails' || echo "No importmap pins found"
```

To update importmap pins:
```bash
bin/importmap pin @hotwired/turbo-rails
bin/importmap pin @hotwired/stimulus
```

### 7.5: JS Dependency Summary

Include in the upgrade summary:

```
### JavaScript Dependencies

**Package Manager**: [npm/yarn/importmap/none]

| Package | Current | Latest | Action |
|---------|---------|--------|--------|
| @hotwired/turbo-rails | 8.0.4 | 8.0.12 | Update recommended |
| @rails/actioncable | 7.1.0 | 8.0.0 | Update with Rails |
| ... | ... | ... | ... |

**Recommended JS Updates:**
- Run `npm update @hotwired/turbo-rails` (or yarn equivalent)
- Run `npm update @rails/actioncable @rails/activestorage` to match Rails version
```

---

## Step 8: Generate Upgrade Summary

Provide a comprehensive summary including all findings from Steps 1-7:

### Version Information
- Current version: X.Y.Z
- Latest version: A.B.C
- Upgrade type: [Patch/Minor/Major]

### Upgrade Complexity Assessment

Rate the upgrade as **Small**, **Medium**, or **Large** based on:

| Factor | Small | Medium | Large |
|--------|-------|--------|-------|
| Version jump | Patch only | Minor version | Major version |
| Breaking changes | None | Few, well-documented | Many, significant |
| Config changes | Minimal | Moderate | Extensive |
| Deprecations | None active | Some to address | Many requiring refactoring |
| Dependencies | Compatible | Some updates needed | Major dependency updates |

### Key Changes to Address

List the most important changes the user needs to handle:
1. Configuration file updates
2. Deprecated methods/features to update
3. New required dependencies
4. Database migrations needed
5. Breaking API changes

### Recommended Upgrade Steps

1. Update test suite and ensure passing
2. Review deprecation warnings in current version
3. Update Gemfile with new Rails version
4. Run `bundle update rails`
5. Update JavaScript dependencies (see JS Dependencies section)
6. **DO NOT run `rails app:update` directly** - use the selective merge process below
7. Run database migrations
8. Run test suite
9. Review and update deprecated code

### Resources

- Rails Upgrade Guide: https://guides.rubyonrails.org/upgrading_ruby_on_rails.html
- Rails Diff: https://railsdiff.org/{current}/{target}
- Release Notes: https://github.com/rails/rails/releases/tag/v{target}

---


## When to Use This Skill

Analyze Rails apps and provide upgrade assessments

Use this skill when working with analyze rails apps and provide upgrade assessments.
## Step 9: Selective File Update (replaces `rails app:update`)

**IMPORTANT:** Do NOT run `rails app:update` as it overwrites files without considering local customizations. Instead, follow this selective merge process:

### 9.1: Detect Local Customizations

Before any upgrade, identify files with local customizations:

```bash
# Check for uncommitted changes
git status

# List config files that differ from a fresh Rails app
# These are the files we need to be careful with
git diff HEAD --name-only -- config/ bin/ public/
```

Create a mental list of files in these categories:
- **Custom config files**: Files with project-specific settings (i18n, mailer, etc.)
- **Modified bin scripts**: Scripts with custom behavior (bin/dev with foreman, etc.)
- **Standard files**: Files that haven't been customized

### 9.2: Analyze Required Changes from Railsdiff

Based on the railsdiff output from Step 6, categorize each changed file:

| Category | Action | Example |
|----------|--------|---------|
| **New files** | Create directly | `config/initializers/new_framework_defaults_X_Y.rb` |
| **Unchanged locally** | Safe to overwrite | `public/404.html` (if not customized) |
| **Customized locally** | Manual merge needed | `config/application.rb`, `bin/dev` |
| **Comment-only changes** | Usually skip | Minor comment updates in config files |

### 9.3: Create Upgrade Plan

Present the user with a clear upgrade plan:

```
## Upgrade Plan: Rails X.Y.Z → A.B.C

### New Files (will be created):
- config/initializers/new_framework_defaults_A_B.rb
- bin/ci (new CI script)

### Safe to Update (no local customizations):
- public/400.html
- public/404.html
- public/500.html

### Needs Manual Merge (local customizations detected):
- config/application.rb
  └─ Local: i18n configuration
  └─ Rails: [describe new Rails changes if any]

- config/environments/development.rb
  └─ Local: letter_opener mailer config
  └─ Rails: [describe new Rails changes]

- bin/dev
  └─ Local: foreman + Procfile.dev setup
  └─ Rails: changed to simple ruby script

### Skip (comment-only or irrelevant changes):
- config/puma.rb (only comment changes)
```

### 9.4: Execute Upgrade Plan

After user confirms the plan:

#### For New Files:
Create them directly using the content from railsdiff or by extracting from a fresh Rails app:

```bash
# Generate a temporary fresh Rails app to extract new files
cd /tmp && rails new rails_template --skip-git --skip-bundle
# Then copy needed files
```

Or use the Rails generator for specific files:
```bash
bin/rails app:update:configs  # Only updates config files, still interactive
```

#### For Safe Updates:
Overwrite these files as they have no local customizations.

#### For Manual Merges:
For each file needing merge, show the user:

1. **Current local version** (their customizations)
2. **New Rails default** (from railsdiff)
3. **Suggested merged version** that:
   - Keeps all local customizations
   - Adds only essential new Rails functionality
   - Removes deprecated settings

Example merge for `config/application.rb`:
```ruby
# KEEP local customizations:
config.i18n.available_locales = [:de, :en]
config.i18n.default_locale = :de
config.i18n.fallbacks = [:en]

# ADD new Rails 8.1 settings if needed:
# (usually none required - new defaults come via new_framework_defaults file)
```

### 9.5: Handle Active Storage Migrations

After file updates, run any new migrations:

```bash
bin/rails db:migrate
```

Check for new migrations that were added:
```bash
ls -la db/migrate/ | tail -10
```

### 9.6: Verify Upgrade

After completing the merge:

1. Start the Rails server and check for errors:
   ```bash
   bin/dev  # or bin/rails server
   ```

2. Check the Rails console:
   ```bash
   bin/rails console
   ```

3. Run the test suite:
   ```bash
   bin/rails test
   ```

4. Review deprecation warnings in logs

---

## Step 10: Finalize Framework Defaults

After verifying the app works:

1. Review `config/initializers/new_framework_defaults_X_Y.rb`
2. Enable each new default one by one, testing after each
3. Once all defaults are enabled and tested, update `config/application.rb`:
   ```ruby
   config.load_defaults X.Y  # Update to new version
   ```
4. Delete the `new_framework_defaults_X_Y.rb` file

---


## When to Use This Skill

Analyze Rails apps and provide upgrade assessments

Use this skill when working with analyze rails apps and provide upgrade assessments.
## Error Handling

- If `gh` CLI is not authenticated, instruct the user to run `gh auth login`
- If railsdiff.org doesn't have the exact versions, try with major.minor.0 versions
- If the app is already on the latest version, congratulate the user and note any upcoming releases
- If local customizations would be lost, ALWAYS stop and show the user what would be overwritten before proceeding

## Key Principles

1. **Never overwrite without checking** - Always check for local customizations first
2. **Preserve user intent** - Local customizations exist for a reason
3. **Minimal changes** - Only add what's necessary for the new Rails version
4. **Transparency** - Show the user exactly what will change before doing it
5. **Reversibility** - User should be able to `git checkout` to restore if needed

---

## Imported Reference

---
name: stripe-integration
description: "Implement Stripe payment processing for robust, PCI-compliant payment flows including checkout, subscriptions, and webhooks. Use when integrating Stripe payments, building subscription systems, or ..."
risk: unknown
source: community
date_added: "2026-02-27"
---

# Stripe Integration

Master Stripe payment processing integration for robust, PCI-compliant payment flows including checkout, subscriptions, webhooks, and refunds.

## Do not use this skill when

- The task is unrelated to stripe integration
- You need a different domain or tool outside this scope

## Instructions

- Clarify goals, constraints, and required inputs.
- Apply relevant best practices and validate outcomes.
- Provide actionable steps and verification.
- If detailed examples are required, open `resources/implementation-playbook.md`.

## Use this skill when

- Implementing payment processing in web/mobile applications
- Setting up subscription billing systems
- Handling one-time payments and recurring charges
- Processing refunds and disputes
- Managing customer payment methods
- Implementing SCA (Strong Customer Authentication) for European payments
- Building marketplace payment flows with Stripe Connect

## Core Concepts

### 1. Payment Flows
**Checkout Session (Hosted)**
- Stripe-hosted payment page
- Minimal PCI compliance burden
- Fastest implementation
- Supports one-time and recurring payments

**Payment Intents (Custom UI)**
- Full control over payment UI
- Requires Stripe.js for PCI compliance
- More complex implementation
- Better customization options

**Setup Intents (Save Payment Methods)**
- Collect payment method without charging
- Used for subscriptions and future payments
- Requires customer confirmation

### 2. Webhooks
**Critical Events:**
- `payment_intent.succeeded`: Payment completed
- `payment_intent.payment_failed`: Payment failed
- `customer.subscription.updated`: Subscription changed
- `customer.subscription.deleted`: Subscription canceled
- `charge.refunded`: Refund processed
- `invoice.payment_succeeded`: Subscription payment successful

### 3. Subscriptions
**Components:**
- **Product**: What you're selling
- **Price**: How much and how often
- **Subscription**: Customer's recurring payment
- **Invoice**: Generated for each billing cycle

### 4. Customer Management
- Create and manage customer records
- Store multiple payment methods
- Track customer metadata
- Manage billing details

## Quick Start

```python
import stripe

stripe.api_key = "sk_test_..."

# Create a checkout session
session = stripe.checkout.Session.create(
    payment_method_types=['card'],
    line_items=[{
        'price_data': {
            'currency': 'usd',
            'product_data': {
                'name': 'Premium Subscription',
            },
            'unit_amount': 2000,  # $20.00
            'recurring': {
                'interval': 'month',
            },
        },
        'quantity': 1,
    }],
    mode='subscription',
    success_url='https://yourdomain.com/success?session_id={CHECKOUT_SESSION_ID}',
    cancel_url='https://yourdomain.com/cancel',
)

# Redirect user to session.url
print(session.url)
```

## Payment Implementation Patterns

### Pattern 1: One-Time Payment (Hosted Checkout)
```python
def create_checkout_session(amount, currency='usd'):
    """Create a one-time payment checkout session."""
    try:
        session = stripe.checkout.Session.create(
            payment_method_types=['card'],
            line_items=[{
                'price_data': {
                    'currency': currency,
                    'product_data': {
                        'name': 'Purchase',
                        'images': ['https://example.com/product.jpg'],
                    },
                    'unit_amount': amount,  # Amount in cents
                },
                'quantity': 1,
            }],
            mode='payment',
            success_url='https://yourdomain.com/success?session_id={CHECKOUT_SESSION_ID}',
            cancel_url='https://yourdomain.com/cancel',
            metadata={
                'order_id': 'order_123',
                'user_id': 'user_456'
            }
        )
        return session
    except stripe.error.StripeError as e:
        # Handle error
        print(f"Stripe error: {e.user_message}")
        raise
```

### Pattern 2: Custom Payment Intent Flow
```python
def create_payment_intent(amount, currency='usd', customer_id=None):
    """Create a payment intent for custom checkout UI."""
    intent = stripe.PaymentIntent.create(
        amount=amount,
        currency=currency,
        customer=customer_id,
        automatic_payment_methods={
            'enabled': True,
        },
        metadata={
            'integration_check': 'accept_a_payment'
        }
    )
    return intent.client_secret  # Send to frontend

# Frontend (JavaScript)
"""
const stripe = Stripe('pk_test_...');
const elements = stripe.elements();
const cardElement = elements.create('card');
cardElement.mount('#card-element');

const {error, paymentIntent} = await stripe.confirmCardPayment(
    clientSecret,
    {
        payment_method: {
            card: cardElement,
            billing_details: {
                name: 'Customer Name'
            }
        }
    }
);

if (error) {
    // Handle error
} else if (paymentIntent.status === 'succeeded') {
    // Payment successful
}
"""
```

### Pattern 3: Subscription Creation
```python
def create_subscription(customer_id, price_id):
    """Create a subscription for a customer."""
    try:
        subscription = stripe.Subscription.create(
            customer=customer_id,
            items=[{'price': price_id}],
            payment_behavior='default_incomplete',
            payment_settings={'save_default_payment_method': 'on_subscription'},
            expand=['latest_invoice.payment_intent'],
        )

        return {
            'subscription_id': subscription.id,
            'client_secret': subscription.latest_invoice.payment_intent.client_secret
        }
    except stripe.error.StripeError as e:
        print(f"Subscription creation failed: {e}")
        raise
```

### Pattern 4: Customer Portal
```python
def create_customer_portal_session(customer_id):
    """Create a portal session for customers to manage subscriptions."""
    session = stripe.billing_portal.Session.create(
        customer=customer_id,
        return_url='https://yourdomain.com/account',
    )
    return session.url  # Redirect customer here
```

## Webhook Handling

### Secure Webhook Endpoint
```python
from flask import Flask, request
import stripe

app = Flask(__name__)

endpoint_secret = 'whsec_...'

@app.route('/webhook', methods=['POST'])
def webhook():
    payload = request.data
    sig_header = request.headers.get('Stripe-Signature')

    try:
        event = stripe.Webhook.construct_event(
            payload, sig_header, endpoint_secret
        )
    except ValueError:
        # Invalid payload
        return 'Invalid payload', 400
    except stripe.error.SignatureVerificationError:
        # Invalid signature
        return 'Invalid signature', 400

    # Handle the event
    if event['type'] == 'payment_intent.succeeded':
        payment_intent = event['data']['object']
        handle_successful_payment(payment_intent)
    elif event['type'] == 'payment_intent.payment_failed':
        payment_intent = event['data']['object']
        handle_failed_payment(payment_intent)
    elif event['type'] == 'customer.subscription.deleted':
        subscription = event['data']['object']
        handle_subscription_canceled(subscription)

    return 'Success', 200

def handle_successful_payment(payment_intent):
    """Process successful payment."""
    customer_id = payment_intent.get('customer')
    amount = payment_intent['amount']
    metadata = payment_intent.get('metadata', {})

    # Update your database
    # Send confirmation email
    # Fulfill order
    print(f"Payment succeeded: {payment_intent['id']}")

def handle_failed_payment(payment_intent):
    """Handle failed payment."""
    error = payment_intent.get('last_payment_error', {})
    print(f"Payment failed: {error.get('message')}")
    # Notify customer
    # Update order status

def handle_subscription_canceled(subscription):
    """Handle subscription cancellation."""
    customer_id = subscription['customer']
    # Update user access
    # Send cancellation email
    print(f"Subscription canceled: {subscription['id']}")
```

### Webhook Best Practices
```python
import hashlib
import hmac

def verify_webhook_signature(payload, signature, secret):
    """Manually verify webhook signature."""
    expected_sig = hmac.new(
        secret.encode('utf-8'),
        payload,
        hashlib.sha256
    ).hexdigest()

    return hmac.compare_digest(signature, expected_sig)

def handle_webhook_idempotently(event_id, handler):
    """Ensure webhook is processed exactly once."""
    # Check if event already processed
    if is_event_processed(event_id):
        return

    # Process event
    try:
        handler()
        mark_event_processed(event_id)
    except Exception as e:
        log_error(e)
        # Stripe will retry failed webhooks
        raise
```

## Customer Management

```python
def create_customer(email, name, payment_method_id=None):
    """Create a Stripe customer."""
    customer = stripe.Customer.create(
        email=email,
        name=name,
        payment_method=payment_method_id,
        invoice_settings={
            'default_payment_method': payment_method_id
        } if payment_method_id else None,
        metadata={
            'user_id': '12345'
        }
    )
    return customer

def attach_payment_method(customer_id, payment_method_id):
    """Attach a payment method to a customer."""
    stripe.PaymentMethod.attach(
        payment_method_id,
        customer=customer_id
    )

    # Set as default
    stripe.Customer.modify(
        customer_id,
        invoice_settings={
            'default_payment_method': payment_method_id
        }
    )

def list_customer_payment_methods(customer_id):
    """List all payment methods for a customer."""
    payment_methods = stripe.PaymentMethod.list(
        customer=customer_id,
        type='card'
    )
    return payment_methods.data
```

## Refund Handling

```python
def create_refund(payment_intent_id, amount=None, reason=None):
    """Create a refund."""
    refund_params = {
        'payment_intent': payment_intent_id
    }

    if amount:
        refund_params['amount'] = amount  # Partial refund

    if reason:
        refund_params['reason'] = reason  # 'duplicate', 'fraudulent', 'requested_by_customer'

    refund = stripe.Refund.create(**refund_params)
    return refund

def handle_dispute(charge_id, evidence):
    """Update dispute with evidence."""
    stripe.Dispute.modify(
        charge_id,
        evidence={
            'customer_name': evidence.get('customer_name'),
            'customer_email_address': evidence.get('customer_email'),
            'shipping_documentation': evidence.get('shipping_proof'),
            'customer_communication': evidence.get('communication'),
        }
    )
```

## Testing

```python
# Use test mode keys
stripe.api_key = "sk_test_..."

# Test card numbers
TEST_CARDS = {
    'success': '4242424242424242',
    'declined': '4000000000000002',
    '3d_secure': '4000002500003155',
    'insufficient_funds': '4000000000009995'
}

def test_payment_flow():
    """Test complete payment flow."""
    # Create test customer
    customer = stripe.Customer.create(
        email="test@example.com"
    )

    # Create payment intent
    intent = stripe.PaymentIntent.create(
        amount=1000,
        currency='usd',
        customer=customer.id,
        payment_method_types=['card']
    )

    # Confirm with test card
    confirmed = stripe.PaymentIntent.confirm(
        intent.id,
        payment_method='pm_card_visa'  # Test payment method
    )

    assert confirmed.status == 'succeeded'
```

## Resources

- **references/checkout-flows.md**: Detailed checkout implementation
- **references/webhook-handling.md**: Webhook security and processing
- **references/subscription-management.md**: Subscription lifecycle
- **references/customer-management.md**: Customer and payment method handling
- **references/invoice-generation.md**: Invoicing and billing
- **assets/stripe-client.py**: Production-ready Stripe client wrapper
- **assets/webhook-handler.py**: Complete webhook processor
- **assets/checkout-config.json**: Checkout configuration templates

## Best Practices

1. **Always Use Webhooks**: Don't rely solely on client-side confirmation
2. **Idempotency**: Handle webhook events idempotently
3. **Error Handling**: Gracefully handle all Stripe errors
4. **Test Mode**: Thoroughly test with test keys before production
5. **Metadata**: Use metadata to link Stripe objects to your database
6. **Monitoring**: Track payment success rates and errors
7. **PCI Compliance**: Never handle raw card data on your server
8. **SCA Ready**: Implement 3D Secure for European payments

## Common Pitfalls

- **Not Verifying Webhooks**: Always verify webhook signatures
- **Missing Webhook Events**: Handle all relevant webhook events
- **Hardcoded Amounts**: Use cents/smallest currency unit
- **No Retry Logic**: Implement retries for API calls
- **Ignoring Test Mode**: Test all edge cases with test cards

---

## Imported Reference

---
name: upgrading-expo
description: "Upgrade Expo SDK versions"
risk: safe
source: "https://github.com/expo/skills/tree/main/plugins/upgrading-expo"
date_added: "2026-02-27"
---

# Upgrading Expo

## Overview

Upgrade Expo SDK versions safely, handling breaking changes, dependencies, and configuration updates.

## When to Use This Skill

Use this skill when you need to upgrade Expo SDK versions.

Use this skill when:
- Upgrading to a new Expo SDK version
- Handling breaking changes between SDK versions
- Updating dependencies for compatibility
- Migrating deprecated APIs to new versions
- Preparing apps for new Expo features

## Instructions

This skill guides you through upgrading Expo SDK versions:

1. **Pre-Upgrade Planning**: Review release notes and breaking changes
2. **Dependency Updates**: Update packages for SDK compatibility
3. **Configuration Migration**: Update app.json and configuration files
4. **Code Updates**: Migrate deprecated APIs to new versions
5. **Testing**: Verify app functionality after upgrade

## Upgrade Process

### 1. Pre-Upgrade Checklist

- Review Expo SDK release notes
- Identify breaking changes affecting your app
- Check compatibility of third-party packages
- Backup current project state
- Create a feature branch for the upgrade

### 2. Update Expo SDK

```bash
# Update Expo CLI
npm install -g expo-cli@latest

# Upgrade Expo SDK
npx expo install expo@latest

# Update all Expo packages
npx expo install --fix
```

### 3. Handle Breaking Changes

- Review migration guides for breaking changes
- Update deprecated API calls
- Modify configuration files as needed
- Update native dependencies if required
- Test affected features thoroughly

### 4. Update Dependencies

```bash
# Check for outdated packages
npx expo-doctor

# Update packages to compatible versions
npx expo install --fix

# Verify compatibility
npx expo-doctor
```

### 5. Testing

- Test core app functionality
- Verify native modules work correctly
- Check for runtime errors
- Test on both iOS and Android
- Verify app store builds still work

## Common Issues

### Dependency Conflicts

- Use `expo install` instead of `npm install` for Expo packages
- Check package compatibility with new SDK version
- Resolve peer dependency warnings

### Configuration Changes

- Update `app.json` for new SDK requirements
- Migrate deprecated configuration options
- Update native configuration files if needed

### Breaking API Changes

- Review API migration guides
- Update code to use new APIs
- Test affected features after changes

## Best Practices

- Always upgrade in a feature branch
- Test thoroughly before merging
- Review release notes carefully
- Update dependencies incrementally
- Keep Expo CLI updated
- Use `expo-doctor` to verify setup

## Resources

For more information, see the [source repository](https://github.com/expo/skills/tree/main/plugins/upgrading-expo).

---

## Imported Reference

---
name: using-neon
description: "Guides and best practices for working with Neon Serverless Postgres. Covers getting started, local development with Neon, choosing a connection method, Neon features, authentication (@neondatabase/..."
risk: safe
source: "https://github.com/neondatabase/agent-skills/tree/main/skills/neon-postgres"
date_added: "2026-02-27"
---

# Neon Serverless Postgres

Neon is a serverless Postgres platform that separates compute and storage to offer autoscaling, branching, instant restore, and scale-to-zero. It's fully compatible with Postgres and works with any language, framework, or ORM that supports Postgres.

## When to Use This Skill

Use this skill when:
- Working with Neon Serverless Postgres
- Setting up Neon databases
- Choosing connection methods for Neon
- Using Neon features like branching or autoscaling
- Working with Neon authentication or APIs
- Questions about Neon best practices

## Neon Documentation

Always reference the Neon documentation before making Neon-related claims. The documentation is the source of truth for all Neon-related information.

Below you'll find a list of resources organized by area of concern. This is meant to support you find the right documentation pages to fetch and add a bit of additonal context.

You can use the `curl` commands to fetch the documentation page as markdown:

**Documentation:**

```bash
# Get list of all Neon docs
curl https://neon.com/llms.txt

# Fetch any doc page as markdown
curl -H "Accept: text/markdown" https://neon.com/docs/<path>
```

Don't guess docs pages. Use the `llms.txt` index to find the relevant URL or follow the links in the resources below.

## Overview of Resources

Reference the appropriate resource file based on the user's needs:

### Core Guides

| Area               | Resource                           | When to Use                                                    |
| ------------------ | ---------------------------------- | -------------------------------------------------------------- |
| What is Neon       | `references/what-is-neon.md`       | Understanding Neon concepts, architecture, core resources      |
| Referencing Docs   | `references/referencing-docs.md`   | Looking up official documentation, verifying information       |
| Features           | `references/features.md`           | Branching, autoscaling, scale-to-zero, instant restore         |
| Getting Started    | `references/getting-started.md`    | Setting up a project, connection strings, dependencies, schema |
| Connection Methods | `references/connection-methods.md` | Choosing drivers based on platform and runtime                 |
| Developer Tools    | `references/devtools.md`           | VSCode extension, MCP server, Neon CLI (`neon init`)           |

### Database Drivers & ORMs

HTTP/WebSocket queries for serverless/edge functions.

| Area              | Resource                        | When to Use                                         |
| ----------------- | ------------------------------- | --------------------------------------------------- |
| Serverless Driver | `references/neon-serverless.md` | `@neondatabase/serverless` - HTTP/WebSocket queries |
| Drizzle ORM       | `references/neon-drizzle.md`    | Drizzle ORM integration with Neon                   |

### Auth & Data API SDKs

Authentication and PostgREST-style data API for Neon.

| Area        | Resource                  | When to Use                                                         |
| ----------- | ------------------------- | ------------------------------------------------------------------- |
| Neon Auth   | `references/neon-auth.md` | `@neondatabase/auth` - Authentication only                          |
| Neon JS SDK | `references/neon-js.md`   | `@neondatabase/neon-js` - Auth + Data API (PostgREST-style queries) |

### Neon Platform API & CLI

Managing Neon resources programmatically via REST API, SDKs, or CLI.

| Area                  | Resource                            | When to Use                                  |
| --------------------- | ----------------------------------- | -------------------------------------------- |
| Platform API Overview | `references/neon-platform-api.md`   | Managing Neon resources via REST API         |
| Neon CLI              | `references/neon-cli.md`            | Terminal workflows, scripts, CI/CD pipelines |
| TypeScript SDK        | `references/neon-typescript-sdk.md` | `@neondatabase/api-client`                   |
| Python SDK            | `references/neon-python-sdk.md`     | `neon-api` package                           |

---

## Imported Reference

---
name: vercel-ai-sdk-expert
description: "Expert in the Vercel AI SDK. Covers Core API (generateText, streamText), UI hooks (useChat, useCompletion), tool calling, and streaming UI components with React and Next.js."
risk: safe
source: community
date_added: "2026-03-06"
---

# Vercel AI SDK Expert

You are a production-grade Vercel AI SDK expert. You help developers build AI-powered applications, chatbots, and generative UI experiences primarily using Next.js and React. You are an expert in both the `ai` (AI SDK Core) and `@ai-sdk/react` (AI SDK UI) packages. You understand streaming, language model integration, system prompts, tool calling (function calling), and structured data generation.

## When to Use This Skill

- Use when adding AI chat or text generation features to a React or Next.js app
- Use when streaming LLM responses to a frontend UI
- Use when implementing tool calling / function calling with an LLM
- Use when returning structured data (JSON) from an LLM using `generateObject`
- Use when building AI-powered generative UIs (streaming React components)
- Use when migrating from direct OpenAI/Anthropic API calls to the unified AI SDK
- Use when troubleshooting streaming issues with `useChat` or `streamText`

## Core Concepts

### Why Vercel AI SDK?

The Vercel AI SDK is a unified framework that abstracts away provider-specific APIs (OpenAI, Anthropic, Google Gemini, Mistral). It provides two main layers:
1. **AI SDK Core (`ai`)**: Server-side functions to interact with LLMs (`generateText`, `streamText`, `generateObject`).
2. **AI SDK UI (`@ai-sdk/react`)**: Frontend hooks to manage chat state and streaming (`useChat`, `useCompletion`).

## Server-Side Generation (Core API)

### Basic Text Generation

```typescript
import { generateText } from "ai";
import { openai } from "@ai-sdk/openai";

// Returns the full string once completion is done (no streaming)
const { text, usage } = await generateText({
  model: openai("gpt-4o"),
  system: "You are a helpful assistant evaluating code.",
  prompt: "Review the following python code...",
});

console.log(text);
console.log(`Tokens used: ${usage.totalTokens}`);
```

### Streaming Text

```typescript
// app/api/chat/route.ts (Next.js App Router API Route)
import { streamText } from 'ai';
import { openai } from '@ai-sdk/openai';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const { messages } = await req.json();

  const result = streamText({
    model: openai('gpt-4o'),
    system: 'You are a friendly customer support bot.',
    messages,
  });

  // Automatically converts the stream to a readable web stream
  return result.toDataStreamResponse();
}
```

### Structured Data (JSON) Generation

```typescript
import { generateObject } from 'ai';
import { openai } from '@ai-sdk/openai';
import { z } from 'zod';

const { object } = await generateObject({
  model: openai('gpt-4o-2024-08-06'), // Use models good at structured output
  system: 'Extract information from the receipt text.',
  prompt: receiptText,
  // Pass a Zod schema to enforce output structure
  schema: z.object({
    storeName: z.string(),
    totalAmount: z.number(),
    items: z.array(z.object({
      name: z.string(),
      price: z.number(),
    })),
    date: z.string().describe("ISO 8601 date format"),
  }),
});

// `object` is automatically fully typed according to the Zod schema!
console.log(object.totalAmount); 
```

## Frontend UI Hooks

### `useChat` (Conversational UI)

```tsx
// app/page.tsx (Next.js Client Component)
"use client";

import { useChat } from "ai/react";

export default function Chat() {
  const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat({
    api: "/api/chat", // Points to the streamText route created above
    // Optional callbacks
    onFinish: (message) => console.log("Done streaming:", message),
    onError: (error) => console.error(error)
  });

  return (
    <div className="flex flex-col h-screen max-w-md mx-auto p-4">
      <div className="flex-1 overflow-y-auto mb-4">
        {messages.map((m) => (
          <div key={m.id} className={`mb-4 ${m.role === 'user' ? 'text-right' : 'text-left'}`}>
            <span className={`p-2 rounded-lg inline-block ${m.role === 'user' ? 'bg-blue-500 text-white' : 'bg-gray-200'}`}>
              {m.target || m.content}
            </span>
          </div>
        ))}
      </div>
      
      <form onSubmit={handleSubmit} className="flex gap-2">
        <input
          value={input}
          onChange={handleInputChange}
          placeholder="Say something..."
          className="flex-1 p-2 border rounded"
          disabled={isLoading}
        />
        <button type="submit" disabled={isLoading} className="bg-black text-white p-2 rounded">
          Send
        </button>
      </form>
    </div>
  );
}
```

## Tool Calling (Function Calling)

Tools allow the LLM to interact with your code, fetching external data or performing actions before responding to the user.

### Server-Side Tool Definition

```typescript
// app/api/chat/route.ts
import { streamText, tool } from 'ai';
import { openai } from '@ai-sdk/openai';
import { z } from 'zod';

export async function POST(req: Request) {
  const { messages } = await req.json();

  const result = streamText({
    model: openai('gpt-4o'),
    messages,
    tools: {
      getWeather: tool({
        description: 'Get the current weather in a given location',
        parameters: z.object({
          location: z.string().describe('The city and state, e.g. San Francisco, CA'),
          unit: z.enum(['celsius', 'fahrenheit']).optional(),
        }),
        // Execute runs when the LLM decides to call this tool
        execute: async ({ location, unit = 'celsius' }) => {
          // Fetch from your actual weather API or database
          const temp = location.includes("San Francisco") ? 15 : 22;
          return `The weather in ${location} is ${temp}° ${unit}.`;
        },
      }),
    },
    // Allows the LLM to call tools automatically in a loop until it has the answer
    maxSteps: 5, 
  });

  return result.toDataStreamResponse();
}
```

### UI for Multi-Step Tool Calls

When using `maxSteps`, the `useChat` hook will display intermediate tool calls if you handle them in the UI.

```tsx
// Inside the `useChat` messages.map loop
{m.role === 'assistant' && m.toolInvocations?.map((toolInvocation) => (
  <div key={toolInvocation.toolCallId} className="text-sm text-gray-500">
    {toolInvocation.state === 'result' ? (
      <p>✅ Fetched weather for {toolInvocation.args.location}</p>
    ) : (
      <p>⏳ Fetching weather for {toolInvocation.args.location}...</p>
    )}
  </div>
))}
```

## Best Practices

- ✅ **Do:** Use `openai('gpt-4o')` or `anthropic('example-model')` format (from specific provider packages like `@ai-sdk/openai`) instead of the older edge runtime wrappers.
- ✅ **Do:** Provide a strict Zod `schema` and a clear `system` prompt when using `generateObject()`.
- ✅ **Do:** Set `maxDuration = 30` (or higher if on Pro) in Next.js API routes that use `streamText`, as LLMs take time to stream responses and Vercel's default is 10-15s.
- ✅ **Do:** Use `tool()` with comprehensive `description` tags on Zod parameters, as the LLM relies entirely on those strings to understand when and how to call the tool.
- ✅ **Do:** Enable `maxSteps: 5` (or similar) when providing tools, otherwise the LLM won't be able to reply to the user *after* seeing the tool result!
- ❌ **Don't:** Forget to return `result.toDataStreamResponse()` in Next.js App Router API routes when using `streamText`; standard JSON responses will break chunking.
- ❌ **Don't:** Blindly trust the output of `generateObject` without validation, even though Zod forces the shape — always handle failure states using `try/catch`.

## Troubleshooting

**Problem:** The streaming chat cuts off abruptly after 10-15 seconds.
**Solution:** The serverless function timed out. Add `export const maxDuration = 30;` (or whatever your plan limit is) to the Next.js API route file.

**Problem:** "Tool execution failed" or the LLM didn't return an answer after using a tool.
**Solution:** `streamText` stops immediately after a tool call completes unless you provide `maxSteps`. Set `maxSteps: 2` (or higher) to let the LLM see the tool result and construct a final text response.

---

## Imported Reference

---
name: wordpress
description: "Complete WordPress development workflow covering theme development, plugin creation, WooCommerce integration, performance optimization, and security hardening."
category: workflow-bundle
risk: safe
source: personal
date_added: "2026-02-27"
---

# WordPress Development Workflow Bundle

## Overview

Comprehensive WordPress development workflow covering theme development, plugin creation, WooCommerce integration, performance optimization, and security. This bundle orchestrates skills for building production-ready WordPress sites and applications.

## When to Use This Workflow

Use this workflow when:
- Building new WordPress websites
- Creating custom themes
- Developing WordPress plugins
- Setting up WooCommerce stores
- Optimizing WordPress performance
- Hardening WordPress security

## Workflow Phases

### Phase 1: WordPress Setup

#### Skills to Invoke
- `app-builder` - Project scaffolding
- `environment-setup-guide` - Development environment

#### Actions
1. Set up local development environment (LocalWP, Docker, or Valet)
2. Install WordPress
3. Configure development database
4. Set up version control
5. Configure wp-config.php for development

#### Copy-Paste Prompts
```
Use @app-builder to scaffold a new WordPress project with modern tooling
```

### Phase 2: Theme Development

#### Skills to Invoke
- `frontend-developer` - Component development
- `frontend-design` - UI implementation
- `tailwind-patterns` - Styling
- `web-performance-optimization` - Performance

#### Actions
1. Design theme architecture
2. Create theme files (style.css, functions.php, index.php)
3. Implement template hierarchy
4. Create custom page templates
5. Add custom post types and taxonomies
6. Implement theme customization options
7. Add responsive design

#### Theme Structure
```
theme-name/
├── style.css
├── functions.php
├── index.php
├── header.php
├── footer.php
├── sidebar.php
├── single.php
├── page.php
├── archive.php
├── search.php
├── 404.php
├── template-parts/
├── inc/
├── assets/
│   ├── css/
│   ├── js/
│   └── images/
└── languages/
```

#### Copy-Paste Prompts
```
Use @frontend-developer to create a custom WordPress theme with React components
```

```
Use @tailwind-patterns to style WordPress theme with modern CSS
```

### Phase 3: Plugin Development

#### Skills to Invoke
- `backend-dev-guidelines` - Backend standards
- `api-design-principles` - API design
- `auth-implementation-patterns` - Authentication

#### Actions
1. Design plugin architecture
2. Create plugin boilerplate
3. Implement hooks (actions and filters)
4. Create admin interfaces
5. Add custom database tables
6. Implement REST API endpoints
7. Add settings and options pages

#### Plugin Structure
```
plugin-name/
├── plugin-name.php
├── includes/
│   ├── class-plugin-activator.php
│   ├── class-plugin-deactivator.php
│   ├── class-plugin-loader.php
│   └── class-plugin.php
├── admin/
│   ├── class-plugin-admin.php
│   ├── css/
│   └── js/
├── public/
│   ├── class-plugin-public.php
│   ├── css/
│   └── js/
└── languages/
```

#### Copy-Paste Prompts
```
Use @backend-dev-guidelines to create a WordPress plugin with proper architecture
```

### Phase 4: WooCommerce Integration

#### Skills to Invoke
- `payment-integration` - Payment processing
- `stripe-integration` - Stripe payments
- `billing-automation` - Billing workflows

#### Actions
1. Install and configure WooCommerce
2. Create custom product types
3. Customize checkout flow
4. Integrate payment gateways
5. Set up shipping methods
6. Create custom order statuses
7. Implement subscription products
8. Add custom email templates

#### Copy-Paste Prompts
```
Use @payment-integration to set up WooCommerce with Stripe
```

```
Use @billing-automation to create subscription products in WooCommerce
```

### Phase 5: Performance Optimization

#### Skills to Invoke
- `web-performance-optimization` - Performance optimization
- `database-optimizer` - Database optimization

#### Actions
1. Implement caching (object, page, browser)
2. Optimize images (lazy loading, WebP)
3. Minify and combine assets
4. Enable CDN
5. Optimize database queries
6. Implement lazy loading
7. Configure OPcache
8. Set up Redis/Memcached

#### Performance Checklist
- [ ] Page load time < 3 seconds
- [ ] Time to First Byte < 200ms
- [ ] Largest Contentful Paint < 2.5s
- [ ] Cumulative Layout Shift < 0.1
- [ ] First Input Delay < 100ms

#### Copy-Paste Prompts
```
Use @web-performance-optimization to audit and improve WordPress performance
```

### Phase 6: Security Hardening

#### Skills to Invoke
- `security-auditor` - Security audit
- `wordpress-penetration-testing` - WordPress security testing
- `sast-configuration` - Static analysis

#### Actions
1. Update WordPress core, themes, plugins
2. Implement security headers
3. Configure file permissions
4. Set up firewall rules
5. Enable two-factor authentication
6. Implement rate limiting
7. Configure security logging
8. Set up malware scanning

#### Security Checklist
- [ ] WordPress core updated
- [ ] All plugins/themes updated
- [ ] Strong passwords enforced
- [ ] Two-factor authentication enabled
- [ ] Security headers configured
- [ ] XML-RPC disabled or protected
- [ ] File editing disabled
- [ ] Database prefix changed
- [ ] Regular backups configured

#### Copy-Paste Prompts
```
Use @wordpress-penetration-testing to audit WordPress security
```

```
Use @security-auditor to perform comprehensive security review
```

### Phase 7: Testing

#### Skills to Invoke
- `test-automator` - Test automation
- `playwright-skill` - E2E testing
- `webapp-testing` - Web app testing

#### Actions
1. Write unit tests for custom code
2. Create integration tests
3. Set up E2E tests
4. Test cross-browser compatibility
5. Test responsive design
6. Performance testing
7. Security testing

#### Copy-Paste Prompts
```
Use @playwright-skill to create E2E tests for WordPress site
```

### Phase 8: Deployment

#### Skills to Invoke
- `deployment-engineer` - Deployment
- `cicd-automation-workflow-automate` - CI/CD
- `github-actions-templates` - GitHub Actions

#### Actions
1. Set up staging environment
2. Configure deployment pipeline
3. Set up database migrations
4. Configure environment variables
5. Enable maintenance mode during deployment
6. Deploy to production
7. Verify deployment
8. Monitor post-deployment

#### Copy-Paste Prompts
```
Use @deployment-engineer to set up WordPress deployment pipeline
```

## WordPress-Specific Workflows

### Custom Post Type Development
```php
register_post_type('book', [
    'labels' => [...],
    'public' => true,
    'has_archive' => true,
    'supports' => ['title', 'editor', 'thumbnail', 'excerpt'],
    'menu_icon' => 'dashicons-book',
]);
```

### Custom REST API Endpoint
```php
add_action('rest_api_init', function() {
    register_rest_route('myplugin/v1', '/books', [
        'methods' => 'GET',
        'callback' => 'get_books',
        'permission_callback' => '__return_true',
    ]);
});
```

### WooCommerce Custom Product Type
```php
add_action('init', function() {
    class WC_Product_Custom extends WC_Product {
        // Custom product implementation
    }
});
```

## Quality Gates

Before moving to next phase, verify:
- [ ] All custom code tested
- [ ] Security scan passed
- [ ] Performance targets met
- [ ] Cross-browser tested
- [ ] Mobile responsive verified
- [ ] Accessibility checked (WCAG 2.1)

## Related Workflow Bundles

- `development` - General web development
- `security-audit` - Security testing
- `testing-qa` - Testing workflow
- `ecommerce` - E-commerce development

---

## Imported Reference

---
name: wordpress-plugin-development
description: "WordPress plugin development workflow covering plugin architecture, hooks, admin interfaces, REST API, and security best practices."
category: granular-workflow-bundle
risk: safe
source: personal
date_added: "2026-02-27"
---

# WordPress Plugin Development Workflow

## Overview

Specialized workflow for creating WordPress plugins with proper architecture, hooks system, admin interfaces, REST API endpoints, and security practices.

## When to Use This Workflow

Use this workflow when:
- Creating custom WordPress plugins
- Extending WordPress functionality
- Building admin interfaces
- Adding REST API endpoints
- Integrating third-party services

## Workflow Phases

### Phase 1: Plugin Setup

#### Skills to Invoke
- `app-builder` - Project scaffolding
- `backend-dev-guidelines` - Backend patterns

#### Actions
1. Create plugin directory structure
2. Set up main plugin file with header
3. Implement activation/deactivation hooks
4. Set up autoloading
5. Configure text domain

#### Copy-Paste Prompts
```
Use @app-builder to scaffold a new WordPress plugin
```

### Phase 2: Plugin Architecture

#### Skills to Invoke
- `backend-dev-guidelines` - Architecture patterns

#### Actions
1. Design plugin class structure
2. Implement singleton pattern
3. Create loader class
4. Set up dependency injection
5. Configure plugin lifecycle

#### Copy-Paste Prompts
```
Use @backend-dev-guidelines to design plugin architecture
```

### Phase 3: Hooks Implementation

#### Skills to Invoke
- `wordpress-penetration-testing` - WordPress patterns

#### Actions
1. Register action hooks
2. Create filter hooks
3. Implement callback functions
4. Set up hook priorities
5. Add conditional hooks

#### Copy-Paste Prompts
```
Use @wordpress-penetration-testing to understand WordPress hooks
```

### Phase 4: Admin Interface

#### Skills to Invoke
- `frontend-developer` - Admin UI

#### Actions
1. Create admin menu
2. Build settings pages
3. Implement options registration
4. Add settings sections/fields
5. Create admin notices

#### Copy-Paste Prompts
```
Use @frontend-developer to create WordPress admin interface
```

### Phase 5: Database Operations

#### Skills to Invoke
- `database-design` - Database design
- `postgresql` - Database patterns

#### Actions
1. Create custom tables
2. Implement CRUD operations
3. Add data validation
4. Set up data sanitization
5. Create data upgrade routines

#### Copy-Paste Prompts
```
Use @database-design to design plugin database schema
```

### Phase 6: REST API

#### Skills to Invoke
- `api-design-principles` - API design
- `api-patterns` - API patterns

#### Actions
1. Register REST routes
2. Create endpoint callbacks
3. Implement permission callbacks
4. Add request validation
5. Document API endpoints

#### Copy-Paste Prompts
```
Use @api-design-principles to create WordPress REST API endpoints
```

### Phase 7: Security

#### Skills to Invoke
- `wordpress-penetration-testing` - WordPress security
- `security-scanning-security-sast` - Security scanning

#### Actions
1. Implement nonce verification
2. Add capability checks
3. Sanitize all inputs
4. Escape all outputs
5. Secure database queries

#### Copy-Paste Prompts
```
Use @wordpress-penetration-testing to audit plugin security
```

### Phase 8: Testing

#### Skills to Invoke
- `test-automator` - Test automation
- `php-pro` - PHP testing

#### Actions
1. Set up PHPUnit
2. Create unit tests
3. Write integration tests
4. Test with WordPress test suite
5. Configure CI

#### Copy-Paste Prompts
```
Use @test-automator to set up plugin testing
```

## Plugin Structure

```
plugin-name/
├── plugin-name.php
├── includes/
│   ├── class-plugin.php
│   ├── class-loader.php
│   ├── class-activator.php
│   └── class-deactivator.php
├── admin/
│   ├── class-plugin-admin.php
│   ├── css/
│   └── js/
├── public/
│   ├── class-plugin-public.php
│   ├── css/
│   └── js/
├── languages/
└── vendor/
```

## Quality Gates

- [ ] Plugin activates without errors
- [ ] All hooks working
- [ ] Admin interface functional
- [ ] Security measures implemented
- [ ] Tests passing
- [ ] Documentation complete

## Related Workflow Bundles

- `wordpress` - WordPress development
- `wordpress-theme-development` - Theme development
- `wordpress-woocommerce` - WooCommerce

---

## Imported Reference

---
name: wordpress-theme-development
description: "WordPress theme development workflow covering theme architecture, template hierarchy, custom post types, block editor support, and responsive design."
category: granular-workflow-bundle
risk: safe
source: personal
date_added: "2026-02-27"
---

# WordPress Theme Development Workflow

## Overview

Specialized workflow for creating custom WordPress themes from scratch, including modern block editor (Gutenberg) support, template hierarchy, and responsive design.

## When to Use This Workflow

Use this workflow when:
- Creating custom WordPress themes
- Converting designs to WordPress themes
- Adding block editor support
- Implementing custom post types
- Building child themes

## Workflow Phases

### Phase 1: Theme Setup

#### Skills to Invoke
- `app-builder` - Project scaffolding
- `frontend-developer` - Frontend development

#### Actions
1. Create theme directory structure
2. Set up style.css with theme header
3. Create functions.php
4. Configure theme support
5. Set up enqueue scripts/styles

#### Copy-Paste Prompts
```
Use @app-builder to scaffold a new WordPress theme project
```

### Phase 2: Template Hierarchy

#### Skills to Invoke
- `frontend-developer` - Template development

#### Actions
1. Create index.php (fallback template)
2. Implement header.php and footer.php
3. Create single.php for posts
4. Create page.php for pages
5. Add archive.php for archives
6. Implement search.php and 404.php

#### Copy-Paste Prompts
```
Use @frontend-developer to create WordPress template files
```

### Phase 3: Theme Functions

#### Skills to Invoke
- `backend-dev-guidelines` - Backend patterns

#### Actions
1. Register navigation menus
2. Add theme support (thumbnails, RSS, etc.)
3. Register widget areas
4. Create custom template tags
5. Implement helper functions

#### Copy-Paste Prompts
```
Use @backend-dev-guidelines to create theme functions
```

### Phase 4: Custom Post Types

#### Skills to Invoke
- `wordpress-penetration-testing` - WordPress patterns

#### Actions
1. Register custom post types
2. Create custom taxonomies
3. Add custom meta boxes
4. Implement custom fields
5. Create archive templates

#### Copy-Paste Prompts
```
Use @wordpress-penetration-testing to understand WordPress CPT patterns
```

### Phase 5: Block Editor Support

#### Skills to Invoke
- `frontend-developer` - Block development

#### Actions
1. Enable block editor support
2. Register custom blocks
3. Create block styles
4. Add block patterns
5. Configure block templates

#### Copy-Paste Prompts
```
Use @frontend-developer to create custom Gutenberg blocks
```

### Phase 6: Styling and Design

#### Skills to Invoke
- `frontend-design` - UI design
- `tailwind-patterns` - Tailwind CSS

#### Actions
1. Implement responsive design
2. Add CSS framework or custom styles
3. Create design system
4. Implement theme customizer
5. Add accessibility features

#### Copy-Paste Prompts
```
Use @frontend-design to create responsive theme design
```

### Phase 7: Testing

#### Skills to Invoke
- `playwright-skill` - Browser testing
- `webapp-testing` - Web app testing

#### Actions
1. Test across browsers
2. Verify responsive breakpoints
3. Test block editor
4. Check accessibility
5. Performance testing

#### Copy-Paste Prompts
```
Use @playwright-skill to test WordPress theme
```

## Theme Structure

```
theme-name/
├── style.css
├── functions.php
├── index.php
├── header.php
├── footer.php
├── sidebar.php
├── single.php
├── page.php
├── archive.php
├── search.php
├── 404.php
├── comments.php
├── template-parts/
├── inc/
├── assets/
│   ├── css/
│   ├── js/
│   └── images/
└── languages/
```

## Quality Gates

- [ ] All templates working
- [ ] Block editor supported
- [ ] Responsive design verified
- [ ] Accessibility checked
- [ ] Performance optimized
- [ ] Cross-browser tested

## Related Workflow Bundles

- `wordpress` - WordPress development
- `wordpress-plugin-development` - Plugin development
- `wordpress-woocommerce` - WooCommerce

## Imported Module: Convex
---
name: convex
description: "Convex reactive backend expert: schema design, TypeScript functions, real-time subscriptions, auth, file storage, scheduling, and deployment."
risk: safe
source: "https://docs.convex.dev"
date_added: "2026-02-27"
---

# Convex

You are an expert in Convex — the open-source, reactive backend platform where queries are TypeScript code. You have deep knowledge of schema design, function authoring (queries, mutations, actions), real-time data subscriptions, authentication, file storage, scheduling, and deployment workflows across React, Next.js, Angular, Vue, Svelte, React Native, and server-side environments.

## When to Use

- Use when building a new project with Convex as the backend
- Use when adding Convex to an existing React, Next.js, Angular, Vue, Svelte, or React Native app
- Use when designing schemas for a Convex document-relational database
- Use when writing or debugging Convex functions (queries, mutations, actions)
- Use when implementing real-time/reactive data patterns
- Use when setting up authentication with Convex Auth or third-party providers (Clerk, Auth0, etc.)
- Use when working with Convex file storage, scheduled functions, or cron jobs
- Use when deploying or managing Convex projects

## Core Concepts

Convex is a **document-relational** database with a fully managed backend. Key differentiators:

- **Reactive by default**: Queries automatically re-run and push updates to all connected clients when underlying data changes
- **TypeScript-first**: All backend logic — queries, mutations, actions, schemas — is written in TypeScript
- **ACID transactions**: Serializable isolation with optimistic concurrency control
- **No infrastructure to manage**: Serverless, scales automatically, zero config
- **End-to-end type safety**: Types flow from schema → backend functions → client hooks

### Function Types

| Type            | Purpose                   | Can Read DB    | Can Write DB      | Can Call External APIs | Cached/Reactive |
| :-------------- | :------------------------ | :------------- | :---------------- | :--------------------- | :-------------- |
| **Query**       | Read data                 | ✅             | ❌                | ❌                     | ✅              |
| **Mutation**    | Write data                | ✅             | ✅                | ❌                     | ❌              |
| **Action**      | Side effects              | via `runQuery` | via `runMutation` | ✅                     | ❌              |
| **HTTP Action** | Webhooks/custom endpoints | via `runQuery` | via `runMutation` | ✅                     | ❌              |

## Project Setup

### New Project (Next.js)

```bash
npx create-next-app@latest my-app
cd my-app && npm install convex
npx convex dev
```

### Add to Existing Project

```bash
npm install convex
npx convex dev
```

The `npx convex dev` command:

1. Prompts you to log in (GitHub)
2. Creates a project and deployment
3. Generates `convex/` folder for backend functions
4. Syncs functions to your dev deployment in real-time
5. Creates `.env.local` with `CONVEX_DEPLOYMENT` and `NEXT_PUBLIC_CONVEX_URL`

### Folder Structure

```
my-app/
├── convex/
│   ├── _generated/        ← Auto-generated (DO NOT EDIT)
│   │   ├── api.d.ts
│   │   ├── dataModel.d.ts
│   │   └── server.d.ts
│   ├── schema.ts          ← Database schema definition
│   ├── tasks.ts           ← Query/mutation functions
│   └── http.ts            ← HTTP actions (optional)
├── .env.local             ← CONVEX_DEPLOYMENT, NEXT_PUBLIC_CONVEX_URL
└── convex.json            ← Project config (optional)
```

## Schema Design

Define your schema in `convex/schema.ts` using the validator library:

```typescript
import { defineSchema, defineTable } from "convex/server";
import { v } from "convex/values";

export default defineSchema({
  users: defineTable({
    name: v.string(),
    email: v.string(),
    avatarUrl: v.optional(v.string()),
    tokenIdentifier: v.string(),
  })
    .index("by_token", ["tokenIdentifier"])
    .index("by_email", ["email"]),

  messages: defineTable({
    authorId: v.id("users"),
    channelId: v.id("channels"),
    body: v.string(),
    attachmentId: v.optional(v.id("_storage")),
  })
    .index("by_channel", ["channelId"])
    .searchIndex("search_body", { searchField: "body" }),

  channels: defineTable({
    name: v.string(),
    description: v.optional(v.string()),
    isPrivate: v.boolean(),
  }),
});
```

### Validator Types

| Validator                         | TypeScript Type       | Notes                                          |
| :-------------------------------- | :-------------------- | :--------------------------------------------- |
| `v.string()`                      | `string`              |                                                |
| `v.number()`                      | `number`              | IEEE 754 float                                 |
| `v.bigint()`                      | `bigint`              |                                                |
| `v.boolean()`                     | `boolean`             |                                                |
| `v.null()`                        | `null`                |                                                |
| `v.id("tableName")`               | `Id<"tableName">`     | Document reference                             |
| `v.array(v.string())`             | `string[]`            |                                                |
| `v.object({...})`                 | `{...}`               | Nested objects                                 |
| `v.optional(v.string())`          | `string \| undefined` |                                                |
| `v.union(v.string(), v.number())` | `string \| number`    |                                                |
| `v.literal("active")`             | `"active"`            | Literal types                                  |
| `v.bytes()`                       | `ArrayBuffer`         | Binary data                                    |
| `v.float64()`                     | `number`              | Explicit 64-bit float (used in vector indexes) |
| `v.any()`                         | `any`                 | Escape hatch                                   |

### Indexes

```typescript
// Single-field index
defineTable({ email: v.string() }).index("by_email", ["email"]);

// Compound index (order matters for range queries)
defineTable({
  orgId: v.string(),
  createdAt: v.number(),
}).index("by_org_and_date", ["orgId", "createdAt"]);

// Full-text search index
defineTable({ body: v.string(), channelId: v.id("channels") }).searchIndex(
  "search_body",
  {
    searchField: "body",
    filterFields: ["channelId"],
  },
);

// Vector search index (for AI/embeddings)
defineTable({ embedding: v.array(v.float64()), text: v.string() }).vectorIndex(
  "by_embedding",
  {
    vectorField: "embedding",
    dimensions: 1536,
  },
);
```

## Writing Functions

### Queries (Read Data)

Queries are reactive — clients automatically get updates when data changes.

````typescript
import { query } from "./_generated/server";
import { v } from "convex/values";

// Simple query — list all tasks
export const list = query({
  args: {},
  handler: async (ctx) => {
    return await ctx.db.query("tasks").collect();
  },
});

// Query with arguments and filtering
export const getByChannel = query({
  args: { channelId: v.id("channels") },
  handler: async (ctx, args) => {
    return await ctx.db
      .query("messages")
      .withIndex("by_channel", (q) => q.eq("channelId", args.channelId))
      .order("desc")
      .take(50);
  },
});

// Query with auth check
export const getMyProfile = query({
  args: {},
  handler: async (ctx) => {
    const identity = await ctx.auth.getUserIdentity();
    if (!identity) return null;

    return await ctx.db
      .query("users")
      .withIndex("by_token", (q) =>
        q.eq("tokenIdentifier", identity.tokenIdentifier),
      )
      .unique();
  },
});

### Paginated Queries

Use cursor-based pagination for lists or infinite scroll UIs.

```typescript
import { query } from "./_generated/server";
import { paginationOptsValidator } from "convex/server";

export const listPaginated = query({
  args: {
    paginationOpts: paginationOptsValidator
  },
  handler: async (ctx, args) => {
    return await ctx.db
      .query("messages")
      .order("desc")
      .paginate(args.paginationOpts);
  },
});
```

### Mutations (Write Data)

Mutations run as ACID transactions with serializable isolation.

```typescript
import { mutation } from "./_generated/server";
import { v } from "convex/values";

// Insert a document
export const create = mutation({
  args: { text: v.string(), isCompleted: v.boolean() },
  handler: async (ctx, args) => {
    const taskId = await ctx.db.insert("tasks", {
      text: args.text,
      isCompleted: args.isCompleted,
    });
    return taskId;
  },
});

// Update a document
export const update = mutation({
  args: { id: v.id("tasks"), isCompleted: v.boolean() },
  handler: async (ctx, args) => {
    await ctx.db.patch(args.id, { isCompleted: args.isCompleted });
  },
});

// Delete a document
export const remove = mutation({
  args: { id: v.id("tasks") },
  handler: async (ctx, args) => {
    await ctx.db.delete(args.id);
  },
});

// Multi-document transaction (automatically atomic)
export const transferCredits = mutation({
  args: {
    fromUserId: v.id("users"),
    toUserId: v.id("users"),
    amount: v.number(),
  },
  handler: async (ctx, args) => {
    const fromUser = await ctx.db.get(args.fromUserId);
    const toUser = await ctx.db.get(args.toUserId);
    if (!fromUser || !toUser) throw new Error("User not found");
    if (fromUser.credits < args.amount) throw new Error("Insufficient credits");

    await ctx.db.patch(args.fromUserId, {
      credits: fromUser.credits - args.amount,
    });
    await ctx.db.patch(args.toUserId, {
      credits: toUser.credits + args.amount,
    });
  },
});
````

### Actions (External APIs & Side Effects)

Actions can call third-party services but cannot directly access the database — they must use `ctx.runQuery` and `ctx.runMutation`.

```typescript
import { action } from "./_generated/server";
import { v } from "convex/values";
import { api } from "./_generated/api";

export const sendEmail = action({
  args: { to: v.string(), subject: v.string(), body: v.string() },
  handler: async (ctx, args) => {
    // Call external API
    const response = await fetch("https://api.sendgrid.com/v3/mail/send", {
      method: "POST",
      headers: {
        Authorization: `Bearer ${process.env.SENDGRID_API_KEY}`,
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        personalizations: [{ to: [{ email: args.to }] }],
        from: { email: "noreply@example.com" },
        subject: args.subject,
        content: [{ type: "text/plain", value: args.body }],
      }),
    });

    if (!response.ok) throw new Error("Failed to send email");

    // Write result back to database via mutation
    await ctx.runMutation(api.emails.recordSent, {
      to: args.to,
      subject: args.subject,
      sentAt: Date.now(),
    });
  },
});

// Generate AI embeddings
export const generateEmbedding = action({
  args: { text: v.string(), documentId: v.id("documents") },
  handler: async (ctx, args) => {
    const response = await fetch("https://api.openai.com/v1/embeddings", {
      method: "POST",
      headers: {
        Authorization: `Bearer ${process.env.OPENAI_API_KEY}`,
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        model: "text-embedding-3-small",
        input: args.text,
      }),
    });

    const { data } = await response.json();
    await ctx.runMutation(api.documents.saveEmbedding, {
      documentId: args.documentId,
      embedding: data[0].embedding,
    });
  },
});
```

### HTTP Actions (Webhooks)

```typescript
import { httpRouter } from "convex/server";
import { httpAction } from "./_generated/server";
import { api } from "./_generated/api";

const http = httpRouter();

http.route({
  path: "/webhooks/stripe",
  method: "POST",
  handler: httpAction(async (ctx, request) => {
    const body = await request.text();
    const signature = request.headers.get("stripe-signature");

    // Verify webhook signature here...

    const event = JSON.parse(body);
    await ctx.runMutation(api.payments.handleWebhook, { event });

    return new Response("OK", { status: 200 });
  }),
});

export default http;
```

## Client-Side Integration

### React / Next.js

```typescript
// app/ConvexClientProvider.tsx
"use client";
import { ConvexProvider, ConvexReactClient } from "convex/react";
import { ReactNode } from "react";

const convex = new ConvexReactClient(process.env.NEXT_PUBLIC_CONVEX_URL!);

export function ConvexClientProvider({ children }: { children: ReactNode }) {
  return <ConvexProvider client={convex}>{children}</ConvexProvider>;
}
```

```typescript
// app/layout.tsx — wrap children
import { ConvexClientProvider } from "./ConvexClientProvider";

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>
        <ConvexClientProvider>{children}</ConvexClientProvider>
      </body>
    </html>
  );
}
```

```typescript
// Component using Convex hooks
"use client";
import { useQuery, useMutation } from "convex/react";
import { api } from "@/convex/_generated/api";

export function TaskList() {
  // Reactive query — auto-updates when data changes
  const tasks = useQuery(api.tasks.list);
  const addTask = useMutation(api.tasks.create);
  const toggleTask = useMutation(api.tasks.update);

  if (tasks === undefined) return <p>Loading...</p>;

  return (
    <div>
      {tasks.map((task) => (
        <div key={task._id}>
          <input
            type="checkbox"
            checked={task.isCompleted}
            onChange={() =>
              toggleTask({ id: task._id, isCompleted: !task.isCompleted })
            }
          />
          {task.text}
        </div>
      ))}
      <button onClick={() => addTask({ text: "New task", isCompleted: false })}>
        Add Task
      </button>
    </div>
  );
}
```

```typescript
// Component using Paginated Queries
"use client";
import { usePaginatedQuery } from "convex/react";
import { api } from "@/convex/_generated/api";

export function MessageLog() {
  const { results, status, loadMore } = usePaginatedQuery(
    api.messages.listPaginated,
    {}, // args
    { initialNumItems: 20 }
  );

  return (
    <div>
      {results.map((msg) => (
        <div key={msg._id}>{msg.body}</div>
      ))}

      {status === "LoadingFirstPage" && <p>Loading...</p>}

      {status === "CanLoadMore" && (
        <button onClick={() => loadMore(20)}>Load More</button>
      )}
    </div>
  );
}
```

### With Auth (First-Party Convex Auth)

Convex provides a robust, native authentication library (`@convex-dev/auth`) featuring Magic Links, Passwords, and 80+ OAuth providers without needing a third-party service.

```typescript
// app/ConvexClientProvider.tsx
"use client";
import { ConvexAuthProvider } from "@convex-dev/auth/react";
import { ConvexReactClient } from "convex/react";
import { ReactNode } from "react";

const convex = new ConvexReactClient(process.env.NEXT_PUBLIC_CONVEX_URL!);

export function ConvexClientProvider({ children }: { children: ReactNode }) {
  return (
    <ConvexAuthProvider client={convex}>
      {children}
    </ConvexAuthProvider>
  );
}
```

```typescript
// Client-side sign in
import { useAuthActions } from "@convex-dev/auth/react";

export function Login() {
  const { signIn } = useAuthActions();
  return <button onClick={() => signIn("github")}>Sign in with GitHub</button>;
}
```

### With Auth (Third-Party Clerk Example)

If you prefer a hosted third-party solution like Clerk:

```typescript
// app/ConvexClientProvider.tsx
"use client";
import { ConvexProviderWithClerk } from "convex/react-clerk";
import { ClerkProvider, useAuth } from "@clerk/nextjs";
import { ConvexReactClient } from "convex/react";

const convex = new ConvexReactClient(process.env.NEXT_PUBLIC_CONVEX_URL!);

export function ConvexClientProvider({ children }: { children: ReactNode }) {
  return (
    <ClerkProvider publishableKey={process.env.NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY!}>
      <ConvexProviderWithClerk client={convex} useAuth={useAuth}>
        {children}
      </ConvexProviderWithClerk>
    </ClerkProvider>
  );
}
```

### With Auth (Better Auth Component)

Convex also has a community component (`@convex-dev/better-auth`) that integrates the Better Auth library directly into the Convex backend. This is currently in **early alpha**.

```bash
npm install better-auth @convex-dev/better-auth
npx convex env set BETTER_AUTH_SECRET your-secret-here
npx convex env set SITE_URL http://localhost:3000
```

Better Auth provides email/password, social logins, two-factor authentication, and session management — all running inside Convex functions rather than an external auth server.

### Angular Integration

Convex does not have an official Angular client library, but Angular apps can use the core `convex` package directly with Angular's Dependency Injection and Signals.

```typescript
// services/convex.service.ts
import { Injectable, signal, effect, OnDestroy } from "@angular/core";
import { ConvexClient } from "convex/browser";
import { api } from "../../convex/_generated/api";
import { FunctionReturnType } from "convex/server";

@Injectable({ providedIn: "root" })
export class ConvexService implements OnDestroy {
  private client = new ConvexClient(environment.convexUrl);

  // Reactive signal — updates automatically when data changes
  tasks = signal<FunctionReturnType<typeof api.tasks.list> | undefined>(
    undefined,
  );

  constructor() {
    // Subscribe to a reactive query
    this.client.onUpdate(api.tasks.list, {}, (result) => {
      this.tasks.set(result);
    });
  }

  async addTask(text: string) {
    await this.client.mutation(api.tasks.create, {
      text,
      isCompleted: false,
    });
  }

  ngOnDestroy() {
    this.client.close();
  }
}
```

```typescript
// Component usage
import { Component, inject } from "@angular/core";
import { ConvexService } from "./services/convex.service";

@Component({
  selector: "app-task-list",
  template: `
    @if (convex.tasks(); as tasks) {
      @for (task of tasks; track task._id) {
        <div>{{ task.text }}</div>
      }
    } @else {
      <p>Loading...</p>
    }
    <button (click)="convex.addTask('New task')">Add Task</button>
  `,
})
export class TaskListComponent {
  convex = inject(ConvexService);
}
```

> **Note:** The community library `@robmanganelly/ngx-convex` provides a more Angular-native experience with React-like hooks adapted for Angular DI and Signals.

## Scheduling & Cron Jobs

### One-off Scheduled Functions

```typescript
import { mutation } from "./_generated/server";
import { api } from "./_generated/api";

export const sendReminder = mutation({
  args: { userId: v.id("users"), message: v.string(), delayMs: v.number() },
  handler: async (ctx, args) => {
    await ctx.scheduler.runAfter(args.delayMs, api.notifications.send, {
      userId: args.userId,
      message: args.message,
    });
  },
});
```

### Cron Jobs

```typescript
// convex/crons.ts
import { cronJobs } from "convex/server";
import { api } from "./_generated/api";

const crons = cronJobs();

crons.interval("clear old logs", { hours: 24 }, api.logs.clearOld);

crons.cron(
  "weekly digest",
  "0 9 * * 1", // Every Monday at 9 AM
  api.emails.sendWeeklyDigest,
);

export default crons;
```

## File Storage

```typescript
// Generate an upload URL (mutation)
export const generateUploadUrl = mutation({
  args: {},
  handler: async (ctx) => {
    return await ctx.storage.generateUploadUrl();
  },
});

// Save file reference after upload (mutation)
export const saveFile = mutation({
  args: { storageId: v.id("_storage"), name: v.string() },
  handler: async (ctx, args) => {
    await ctx.db.insert("files", {
      storageId: args.storageId,
      name: args.name,
    });
  },
});

// Get a URL to serve a file (query)
export const getFileUrl = query({
  args: { storageId: v.id("_storage") },
  handler: async (ctx, args) => {
    return await ctx.storage.getUrl(args.storageId);
  },
});
```

## Environment Variables

```bash
# Set environment variables for your deployment
npx convex env set OPENAI_API_KEY sk-...
npx convex env set SENDGRID_API_KEY SG...

# List current env vars
npx convex env list

# Remove an env var
npx convex env unset OPENAI_API_KEY
```

Access in actions (NOT in queries or mutations):

```typescript
// Only available in actions
const apiKey = process.env.OPENAI_API_KEY;
```

## Deployment & CLI

```bash
# Development (watches for changes, syncs to dev deployment)
npx convex dev

# Deploy to production
npx convex deploy

# Import data
npx convex import --table tasks data.jsonl

# Export data
npx convex export --path ./backup

# Open Convex dashboard
npx convex dashboard

# Run a function from CLI
npx convex run tasks:list

# View logs
npx convex logs
```

## Best Practices

- ✅ Define schemas — adds type safety across your entire stack
- ✅ Use indexes for queries — avoids full table scans
- ✅ Use compound indexes with equality filters first, range filter last
- ✅ Rely on native determinism — `Date.now()` and `Math.random()` are 100% safe to use in queries and mutations because Convex freezes time at the start of every function execution!
- ✅ Use `v.id("tableName")` for document references instead of plain strings
- ✅ Use actions for external API calls (never call external APIs from queries or mutations)
- ✅ Use `ctx.runQuery` / `ctx.runMutation` from actions — never access `ctx.db` directly in actions
- ✅ Add argument validators to all functions — they enforce runtime type safety
- ✅ Return `null` when a document isn't found instead of throwing an error unless missing is exceptional
- ✅ Prefer `withIndex` over `.filter()` for query performance

## Anti-Patterns to Avoid

1. **❌ External API calls in queries/mutations**: Only actions can call external services. Queries and mutations run in the Convex transaction engine.
2. **❌ Doing slow CPU-bound work in mutations**: Mutations block database commits; offload heavy processing to actions.
3. **❌ Using `.collect()` on large tables without limits**: Fetches all documents into memory. Use `.take(N)` or `.paginate()`.
4. **❌ Skipping schema definition**: Without a schema you lose end-to-end type safety, the main Convex advantage.
5. **❌ Using `.filter()` instead of indexes**: `.filter()` does a full table scan. Define an index and use `.withIndex()`.
6. **❌ Storing large blobs in documents**: Use Convex file storage (`_storage`) for files; keep documents lean.
7. **❌ Circular `runQuery`/`runMutation` chains**: Actions calling mutations that schedule actions can create infinite loops.

## Common Pitfalls

- **Problem:** "Query returns `undefined` on first render"
  **Solution:** This is expected — Convex queries are async. Check for `undefined` before rendering (this means loading, not empty).

- **Problem:** "Mutation throws `Document not found`"
  **Solution:** Documents may have been deleted between your read and write due to optimistic concurrency. Re-read inside the mutation.

- **Problem:** "`process.env` is undefined in query/mutation"
  **Solution:** Environment variables are only accessible in **actions** (not queries or mutations) because queries/mutations run in the deterministic transaction engine.

- **Problem:** "Function handler is too slow"
  **Solution:** Add indexes for your query patterns. Use `withIndex()` instead of `.filter()`. For complex operations, break into smaller mutations.

- **Problem:** "Schema push fails with existing data"
  **Solution:** Convex validates existing data against new schemas. Either migrate existing documents first, or use `v.optional()` for new fields.

## Limitations

- Queries and mutations cannot call external HTTP APIs (use actions instead)
- No raw SQL — you work with the Convex query builder API
- Environment variables only available in actions, not in queries or mutations
- Document size limit of 1MB
- Maximum function execution time limits apply
- No server-side rendering of Convex data without specific SSR patterns (use preloading)
- Schemas are enforced at write-time; changing schemas requires data migration for existing documents

## Related Skills

- `@firebase` — Alternative BaaS with Firestore (compare: Convex is TypeScript-first with ACID transactions)
- `@supabase-automation` — Alternative with PostgreSQL backend (compare: Convex is document-relational with built-in reactivity)
- `@prisma-expert` — ORM for traditional databases (Convex replaces both ORM and database)
- `@react-patterns` — Frontend patterns that pair well with Convex React hooks
- `@nextjs-app-router` — Next.js App Router integration patterns
- `@authentication-oauth` — Auth patterns (Convex supports Clerk, Auth0, Convex Auth)
- `@stripe` — Payment integration via Convex actions and HTTP webhooks

## Resources

- [Official Docs](https://docs.convex.dev)
- [Convex Stack (Blog)](https://stack.convex.dev)
- [GitHub](https://github.com/get-convex/convex-backend)
- [Discord Community](https://convex.dev/community)
- [Convex Chef (AI Starter)](https://chef.convex.dev)

## Imported Module: Django Access Review
---
name: django-access-review
description: django-access-review
---

---
name: django-access-review
description: Django access control and IDOR security review. Use when reviewing Django views, DRF viewsets, ORM queries, or any Python/Django code handling user authorization. Trigger keywords: "IDOR", "access control", "authorization", "Django permissions", "object permissions", "tenant...
--- LICENSE
---

<!--
Reference material based on OWASP Cheat Sheet Series (CC BY-SA 4.0)
https://cheatsheetseries.owasp.org/
-->

# Django Access Control & IDOR Review

Find access control vulnerabilities by investigating how the codebase answers one question:

**Can User A access, modify, or delete User B's data?**

## Philosophy: Investigation Over Pattern Matching

Do NOT scan for predefined vulnerable patterns. Instead:

1. **Understand** how authorization works in THIS codebase
2. **Ask questions** about specific data flows
3. **Trace code** to find where (or if) access checks happen
4. **Report** only what you've confirmed through investigation

Every codebase implements authorization differently. Your job is to understand this specific implementation, then find gaps.

---

## Phase 1: Understand the Authorization Model

Before looking for bugs, answer these questions about the codebase:

### How is authorization enforced?

Research the codebase to find:

```
□ Where are permission checks implemented?
  - Decorators? (@login_required, @permission_required, custom?)
  - Middleware? (TenantMiddleware, AuthorizationMiddleware?)
  - Base classes? (BaseAPIView, TenantScopedViewSet?)
  - Permission classes? (DRF permission_classes?)
  - Custom mixins? (OwnershipMixin, TenantMixin?)

□ How are queries scoped?
  - Custom managers? (TenantManager, UserScopedManager?)
  - get_queryset() overrides?
  - Middleware that sets query context?

□ What's the ownership model?
  - Single user ownership? (document.owner_id)
  - Organization/tenant ownership? (document.organization_id)
  - Hierarchical? (org -> team -> user -> resource)
  - Role-based within context? (org admin vs member)
```

### Investigation commands

```bash
# Find how auth is typically done
grep -rn "permission_classes\|@login_required\|@permission_required" --include="*.py" | head -20

# Find base classes that views inherit from
grep -rn "class Base.*View\|class.*Mixin.*:" --include="*.py" | head -20

# Find custom managers
grep -rn "class.*Manager\|def get_queryset" --include="*.py" | head -20

# Find ownership fields on models
grep -rn "owner\|user_id\|organization\|tenant" --include="models.py" | head -30
```

**Do not proceed until you understand the authorization model.**

---

## Phase 2: Map the Attack Surface

Identify endpoints that handle user-specific data:

### What resources exist?

```
□ What models contain user data?
□ Which have ownership fields (owner_id, user_id, organization_id)?
□ Which are accessed via ID in URLs or request bodies?
```

### What operations are exposed?

For each resource, map:
- List endpoints - what data is returned?
- Detail/retrieve endpoints - how is the object fetched?
- Create endpoints - who sets the owner?
- Update endpoints - can users modify others' data?
- Delete endpoints - can users delete others' data?
- Custom actions - what do they access?

---

## Phase 3: Ask Questions and Investigate

For each endpoint that handles user data, ask:

### The Core Question

**"If I'm User A and I know the ID of User B's resource, can I access it?"**

Trace the code to answer this:

```
1. Where does the resource ID enter the system?
   - URL path: /api/documents/{id}/
   - Query param: ?document_id=123
   - Request body: {"document_id": 123}

2. Where is that ID used to fetch data?
   - Find the ORM query or database call

3. Between (1) and (2), what checks exist?
   - Is the query scoped to current user?
   - Is there an explicit ownership check?
   - Is there a permission check on the object?
   - Does a base class or mixin enforce access?

4. If you can't find a check, is there one you missed?
   - Check parent classes
   - Check middleware
   - Check managers
   - Check decorators at URL level
```

### Follow-Up Questions

```
□ For list endpoints: Does the query filter to user's data, or return everything?

□ For create endpoints: Who sets the owner - the server or the request?

□ For bulk operations: Are they scoped to user's data?

□ For related resources: If I can access a document, can I access its comments?
  What if the document belongs to someone else?

□ For tenant/org resources: Can User in Org A access Org B's data by changing
  the org_id in the URL?
```

---

## Phase 4: Trace Specific Flows

Pick a concrete endpoint and trace it completely.

### Example Investigation

```
Endpoint: GET /api/documents/{pk}/

1. Find the view handling this URL
   → DocumentViewSet.retrieve() in api/views.py

2. Check what DocumentViewSet inherits from
   → class DocumentViewSet(viewsets.ModelViewSet)
   → No custom base class with authorization

3. Check permission_classes
   → permission_classes = [IsAuthenticated]
   → Only checks login, not ownership

4. Check get_queryset()
   → def get_queryset(self):
   →     return Document.objects.all()
   → Returns ALL documents!

5. Check for has_object_permission()
   → Not implemented

6. Check retrieve() method
   → Uses default, which calls get_object()
   → get_object() uses get_queryset(), which returns all

7. Conclusion: IDOR - Any authenticated user can access any document
```

### What to look for when tracing

```
Potential gap indicators (investigate further, don't auto-flag):
- get_queryset() returns .all() or filters without user
- Direct Model.objects.get(pk=pk) without ownership in query
- ID comes from request body for sensitive operations
- Permission class checks auth but not ownership
- No has_object_permission() and queryset isn't scoped

Likely safe patterns (but verify the implementation):
- get_queryset() filters by request.user or user's org
- Custom permission class with has_object_permission()
- Base class that enforces scoping
- Manager that auto-filters
```

---

## Phase 5: Report Findings

Only report issues you've confirmed through investigation.

### Confidence Levels

| Level | Meaning | Action |
|-------|---------|--------|
| **HIGH** | Traced the flow, confirmed no check exists | Report with evidence |
| **MEDIUM** | Check may exist but couldn't confirm | Note for manual verification |
| **LOW** | Theoretical, likely mitigated | Do not report |

### Suggested Fixes Must Enforce, Not Document

**Bad fix**: Adding a comment saying "caller must validate permissions"
**Good fix**: Adding code that actually validates permissions

A comment or docstring does not enforce authorization. Your suggested fix must include actual code that:
- Validates the user has permission before proceeding
- Raises an exception or returns an error if unauthorized
- Makes unauthorized access impossible, not just discouraged

Example of a BAD fix suggestion:
```python
def get_resource(resource_id):
    # IMPORTANT: Caller must ensure user has access to this resource
    return Resource.objects.get(pk=resource_id)
```

Example of a GOOD fix suggestion:
```python
def get_resource(resource_id, user):
    resource = Resource.objects.get(pk=resource_id)
    if resource.owner_id != user.id:
        raise PermissionDenied("Access denied")
    return resource
```

If you can't determine the right enforcement mechanism, say so - but never suggest documentation as the fix.

### Report Format

```markdown
## Access Control Review: [Component]

### Authorization Model
[Brief description of how this codebase handles authorization]

### Findings

#### [IDOR-001] [Title] (Severity: High/Medium)
- **Location**: `path/to/file.py:123`
- **Confidence**: High - confirmed through code tracing
- **The Question**: Can User A access User B's documents?
- **Investigation**:
  1. Traced GET /api/documents/{pk}/ to DocumentViewSet
  2. Checked get_queryset() - returns Document.objects.all()
  3. Checked permission_classes - only IsAuthenticated
  4. Checked for has_object_permission() - not implemented
  5. Verified no relevant middleware or base class checks
- **Evidence**: [Code snippet showing the gap]
- **Impact**: Any authenticated user can read any document by ID
- **Suggested Fix**: [Code that enforces authorization - NOT a comment]

### Needs Manual Verification
[Issues where authorization exists but couldn't confirm effectiveness]

### Areas Not Reviewed
[Endpoints or flows not covered in this review]
```

---

## Common Django Authorization Patterns

These are patterns you might find - not a checklist to match against.

### Query Scoping
```python
# Scoped to user
Document.objects.filter(owner=request.user)

# Scoped to organization
Document.objects.filter(organization=request.user.organization)

# Using a custom manager
Document.objects.for_user(request.user)  # Investigate what this does
```

### Permission Enforcement
```python
# DRF permission classes
permission_classes = [IsAuthenticated, IsOwner]

# Custom has_object_permission
def has_object_permission(self, request, view, obj):
    return obj.owner == request.user

# Django decorators
@permission_required('app.view_document')

# Manual checks
if document.owner != request.user:
    raise PermissionDenied()
```

### Ownership Assignment
```python
# Server-side (safe)
def perform_create(self, serializer):
    serializer.save(owner=self.request.user)

# From request (investigate)
serializer.save(**request.data)  # Does request.data include owner?
```

---

## Investigation Checklist

Use this to guide your review, not as a pass/fail checklist:

```
□ I understand how authorization is typically implemented in this codebase
□ I've identified the ownership model (user, org, tenant, etc.)
□ I've mapped the key endpoints that handle user data
□ For each sensitive endpoint, I've traced the flow and asked:
  - Where does the ID come from?
  - Where is data fetched?
  - What checks exist between input and data access?
□ I've verified my findings by checking parent classes and middleware
□ I've only reported issues I've confirmed through investigation
```

## Imported Module: Django Perf Review
---
name: django-perf-review
description: Django performance code review. Use when asked to "review Django performance", "find N+1 queries", "optimize Django", "check queryset performance", "database performance", "Django ORM issues", or audit Django code for performance problems.
allowed-tools: Read, Grep, Glob, Bash, Task
license: LICENSE
---

# Django Performance Review

Review Django code for **validated** performance issues. Research the codebase to confirm issues before reporting. Report only what you can prove.

## Review Approach

1. **Research first** - Trace data flow, check for existing optimizations, verify data volume
2. **Validate before reporting** - Pattern matching is not validation
3. **Zero findings is acceptable** - Don't manufacture issues to appear thorough
4. **Severity must match impact** - If you catch yourself writing "minor" in a CRITICAL finding, it's not critical. Downgrade or skip it.

## Impact Categories

Issues are organized by impact. Focus on CRITICAL and HIGH - these cause real problems at scale.

| Priority | Category | Impact |
|----------|----------|--------|
| 1 | N+1 Queries | **CRITICAL** - Multiplies with data, causes timeouts |
| 2 | Unbounded Querysets | **CRITICAL** - Memory exhaustion, OOM kills |
| 3 | Missing Indexes | **HIGH** - Full table scans on large tables |
| 4 | Write Loops | **HIGH** - Lock contention, slow requests |
| 5 | Inefficient Patterns | **LOW** - Rarely worth reporting |

---

## Priority 1: N+1 Queries (CRITICAL)

**Impact:** Each N+1 adds `O(n)` database round trips. 100 rows = 100 extra queries. 10,000 rows = timeout.

### Rule: Prefetch related data accessed in loops

Validate by tracing: View → Queryset → Template/Serializer → Loop access

```python
# PROBLEM: N+1 - each iteration queries profile
def user_list(request):
    users = User.objects.all()
    return render(request, 'users.html', {'users': users})

# Template:
# {% for user in users %}
#     {{ user.profile.bio }}  ← triggers query per user
# {% endfor %}

# SOLUTION: Prefetch in view
def user_list(request):
    users = User.objects.select_related('profile')
    return render(request, 'users.html', {'users': users})
```

### Rule: Prefetch in serializers, not just views

DRF serializers accessing related fields cause N+1 if queryset isn't optimized.

```python
# PROBLEM: SerializerMethodField queries per object
class UserSerializer(serializers.ModelSerializer):
    order_count = serializers.SerializerMethodField()

    def get_order_count(self, obj):
        return obj.orders.count()  # ← query per user

# SOLUTION: Annotate in viewset, access in serializer
class UserViewSet(viewsets.ModelViewSet):
    def get_queryset(self):
        return User.objects.annotate(order_count=Count('orders'))

class UserSerializer(serializers.ModelSerializer):
    order_count = serializers.IntegerField(read_only=True)
```

### Rule: Model properties that query are dangerous in loops

```python
# PROBLEM: Property triggers query when accessed
class User(models.Model):
    @property
    def recent_orders(self):
        return self.orders.filter(created__gte=last_week)[:5]

# Used in template loop = N+1

# SOLUTION: Use Prefetch with custom queryset, or annotate
```

### Validation Checklist for N+1
- [ ] Traced data flow from view to template/serializer
- [ ] Confirmed related field is accessed inside a loop
- [ ] Searched codebase for existing select_related/prefetch_related
- [ ] Verified table has significant row count (1000+)
- [ ] Confirmed this is a hot path (not admin, not rare action)

---

## Priority 2: Unbounded Querysets (CRITICAL)

**Impact:** Loading entire tables exhausts memory. Large tables cause OOM kills and worker restarts.

### Rule: Always paginate list endpoints

```python
# PROBLEM: No pagination - loads all rows
class UserListView(ListView):
    model = User
    template_name = 'users.html'

# SOLUTION: Add pagination
class UserListView(ListView):
    model = User
    template_name = 'users.html'
    paginate_by = 25
```

### Rule: Use iterator() for large batch processing

```python
# PROBLEM: Loads all objects into memory at once
for user in User.objects.all():
    process(user)

# SOLUTION: Stream with iterator()
for user in User.objects.iterator(chunk_size=1000):
    process(user)
```

### Rule: Never call list() on unbounded querysets

```python
# PROBLEM: Forces full evaluation into memory
all_users = list(User.objects.all())

# SOLUTION: Keep as queryset, slice if needed
users = User.objects.all()[:100]
```

### Validation Checklist for Unbounded Querysets
- [ ] Table is large (10k+ rows) or will grow unbounded
- [ ] No pagination class, paginate_by, or slicing
- [ ] This runs on user-facing request (not background job with chunking)

---

## Priority 3: Missing Indexes (HIGH)

**Impact:** Full table scans. Negligible on small tables, catastrophic on large ones.

### Rule: Index fields used in WHERE clauses on large tables

```python
# PROBLEM: Filtering on unindexed field
# User.objects.filter(email=email)  # full scan if no index

class User(models.Model):
    email = models.EmailField()  # ← no db_index

# SOLUTION: Add index
class User(models.Model):
    email = models.EmailField(db_index=True)
```

### Rule: Index fields used in ORDER BY on large tables

```python
# PROBLEM: Sorting requires full scan without index
Order.objects.order_by('-created')

# SOLUTION: Index the sort field
class Order(models.Model):
    created = models.DateTimeField(db_index=True)
```

### Rule: Use composite indexes for common query patterns

```python
class Order(models.Model):
    user = models.ForeignKey(User)
    status = models.CharField(max_length=20)
    created = models.DateTimeField()

    class Meta:
        indexes = [
            models.Index(fields=['user', 'status']),  # for filter(user=x, status=y)
            models.Index(fields=['status', '-created']),  # for filter(status=x).order_by('-created')
        ]
```

### Validation Checklist for Missing Indexes
- [ ] Table has 10k+ rows
- [ ] Field is used in filter() or order_by() on hot path
- [ ] Checked model - no db_index=True or Meta.indexes entry
- [ ] Not a foreign key (already indexed automatically)

---

## Priority 4: Write Loops (HIGH)

**Impact:** N database writes instead of 1. Lock contention. Slow requests.

### Rule: Use bulk_create instead of create() in loops

```python
# PROBLEM: N inserts, N round trips
for item in items:
    Model.objects.create(name=item['name'])

# SOLUTION: Single bulk insert
Model.objects.bulk_create([
    Model(name=item['name']) for item in items
])
```

### Rule: Use update() or bulk_update instead of save() in loops

```python
# PROBLEM: N updates
for obj in queryset:
    obj.status = 'done'
    obj.save()

# SOLUTION A: Single UPDATE statement (same value for all)
queryset.update(status='done')

# SOLUTION B: bulk_update (different values)
for obj in objects:
    obj.status = compute_status(obj)
Model.objects.bulk_update(objects, ['status'], batch_size=500)
```

### Rule: Use delete() on queryset, not in loops

```python
# PROBLEM: N deletes
for obj in queryset:
    obj.delete()

# SOLUTION: Single DELETE
queryset.delete()
```

### Validation Checklist for Write Loops
- [ ] Loop iterates over 100+ items (or unbounded)
- [ ] Each iteration calls create(), save(), or delete()
- [ ] This runs on user-facing request (not one-time migration script)

---

## Priority 5: Inefficient Patterns (LOW)

**Rarely worth reporting.** Include only as minor notes if you're already reporting real issues.

### Pattern: count() vs exists()

```python
# Slightly suboptimal
if queryset.count() > 0:
    do_thing()

# Marginally better
if queryset.exists():
    do_thing()
```

**Usually skip** - difference is <1ms in most cases.

### Pattern: len(queryset) vs count()

```python
# Fetches all rows to count
if len(queryset) > 0:  # bad if queryset not yet evaluated

# Single COUNT query
if queryset.count() > 0:
```

**Only flag** if queryset is large and not already evaluated.

### Pattern: get() in small loops

```python
# N queries, but if N is small (< 20), often fine
for id in ids:
    obj = Model.objects.get(id=id)
```

**Only flag** if loop is large or this is in a very hot path.

---

## Validation Requirements

Before reporting ANY issue:

1. **Trace the data flow** - Follow queryset from creation to consumption
2. **Search for existing optimizations** - Grep for select_related, prefetch_related, pagination
3. **Verify data volume** - Check if table is actually large
4. **Confirm hot path** - Trace call sites, verify this runs frequently
5. **Rule out mitigations** - Check for caching, rate limiting

**If you cannot validate all steps, do not report.**

---

## Output Format

```markdown
## Django Performance Review: [File/Component Name]

### Summary
Validated issues: X (Y Critical, Z High)

### Findings

#### [PERF-001] N+1 Query in UserListView (CRITICAL)
**Location:** `views.py:45`

**Issue:** Related field `profile` accessed in template loop without prefetch.

**Validation:**
- Traced: UserListView → users queryset → user_list.html → `{{ user.profile.bio }}` in loop
- Searched codebase: no select_related('profile') found
- User table: 50k+ rows (verified in admin)
- Hot path: linked from homepage navigation

**Evidence:**
```python
def get_queryset(self):
    return User.objects.filter(active=True)  # no select_related
```

**Fix:**
```python
def get_queryset(self):
    return User.objects.filter(active=True).select_related('profile')
```
```

If no issues found: "No performance issues identified after reviewing [files] and validating [what you checked]."

**Before submitting, sanity check each finding:**
- Does the severity match the actual impact? ("Minor inefficiency" ≠ CRITICAL)
- Is this a real performance issue or just a style preference?
- Would fixing this measurably improve performance?

If the answer to any is "no" - remove the finding.

---

## What NOT to Report

- Test files
- Admin-only views
- Management commands
- Migration files
- One-time scripts
- Code behind disabled feature flags
- Tables with <1000 rows that won't grow
- Patterns in cold paths (rarely executed code)
- Micro-optimizations (exists vs count, only/defer without evidence)

### False Positives to Avoid

**Queryset variable assignment is not an issue:**
```python
# This is FINE - no performance difference
projects_qs = Project.objects.filter(org=org)
projects = list(projects_qs)

# vs this - identical performance
projects = list(Project.objects.filter(org=org))
```
Querysets are lazy. Assigning to a variable doesn't execute anything.

**Single query patterns are not N+1:**
```python
# This is ONE query, not N+1
projects = list(Project.objects.filter(org=org))
```
N+1 requires a loop that triggers additional queries. A single `list()` call is fine.

**Missing select_related on single object fetch is not N+1:**
```python
# This is 2 queries, not N+1 - report as LOW at most
state = AutofixState.objects.filter(pr_id=pr_id).first()
project_id = state.request.project_id  # second query
```
N+1 requires a loop. A single object doing 2 queries instead of 1 can be reported as LOW if relevant, but never as CRITICAL/HIGH.

**Style preferences are not performance issues:**
If your only suggestion is "combine these two lines" or "rename this variable" - that's style, not performance. Don't report it.

## Imported Module: Django Pro
---
name: django-pro
description: Master Django 5.x with async views, DRF, Celery, and Django Channels. Build scalable web applications with proper architecture, testing, and deployment.
risk: unknown
source: community
date_added: '2026-02-27'
---

## Use this skill when

- Working on django pro tasks or workflows
- Needing guidance, best practices, or checklists for django pro

## Do not use this skill when

- The task is unrelated to django pro
- You need a different domain or tool outside this scope

## Instructions

- Clarify goals, constraints, and required inputs.
- Apply relevant best practices and validate outcomes.
- Provide actionable steps and verification.
- If detailed examples are required, open `resources/implementation-playbook.md`.

You are a Django expert specializing in Django 5.x best practices, scalable architecture, and modern web application development.

## Purpose

Expert Django developer specializing in Django 5.x best practices, scalable architecture, and modern web application development. Masters both traditional synchronous and async Django patterns, with deep knowledge of the Django ecosystem including DRF, Celery, and Django Channels.

## Capabilities

### Core Django Expertise

- Django 5.x features including async views, middleware, and ORM operations
- Model design with proper relationships, indexes, and database optimization
- Class-based views (CBVs) and function-based views (FBVs) best practices
- Django ORM optimization with select_related, prefetch_related, and query annotations
- Custom model managers, querysets, and database functions
- Django signals and their proper usage patterns
- Django admin customization and ModelAdmin configuration

### Architecture & Project Structure

- Scalable Django project architecture for enterprise applications
- Modular app design following Django's reusability principles
- Settings management with environment-specific configurations
- Service layer pattern for business logic separation
- Repository pattern implementation when appropriate
- Django REST Framework (DRF) for API development
- GraphQL with Strawberry Django or Graphene-Django

### Modern Django Features

- Async views and middleware for high-performance applications
- ASGI deployment with Uvicorn/Daphne/Hypercorn
- Django Channels for WebSocket and real-time features
- Background task processing with Celery and Redis/RabbitMQ
- Django's built-in caching framework with Redis/Memcached
- Database connection pooling and optimization
- Full-text search with PostgreSQL or Elasticsearch

### Testing & Quality

- Comprehensive testing with pytest-django
- Factory pattern with factory_boy for test data
- Django TestCase, TransactionTestCase, and LiveServerTestCase
- API testing with DRF test client
- Coverage analysis and test optimization
- Performance testing and profiling with django-silk
- Django Debug Toolbar integration

### Security & Authentication

- Django's security middleware and best practices
- Custom authentication backends and user models
- JWT authentication with djangorestframework-simplejwt
- OAuth2/OIDC integration
- Permission classes and object-level permissions with django-guardian
- CORS, CSRF, and XSS protection
- SQL injection prevention and query parameterization

### Database & ORM

- Complex database migrations and data migrations
- Multi-database configurations and database routing
- PostgreSQL-specific features (JSONField, ArrayField, etc.)
- Database performance optimization and query analysis
- Raw SQL when necessary with proper parameterization
- Database transactions and atomic operations
- Connection pooling with django-db-pool or pgbouncer

### Deployment & DevOps

- Production-ready Django configurations
- Docker containerization with multi-stage builds
- Gunicorn/uWSGI configuration for WSGI
- Static file serving with WhiteNoise or CDN integration
- Media file handling with django-storages
- Environment variable management with django-environ
- CI/CD pipelines for Django applications

### Frontend Integration

- Django templates with modern JavaScript frameworks
- HTMX integration for dynamic UIs without complex JavaScript
- Django + React/Vue/Angular architectures
- Webpack integration with django-webpack-loader
- Server-side rendering strategies
- API-first development patterns

### Performance Optimization

- Database query optimization and indexing strategies
- Django ORM query optimization techniques
- Caching strategies at multiple levels (query, view, template)
- Lazy loading and eager loading patterns
- Database connection pooling
- Asynchronous task processing
- CDN and static file optimization

### Third-Party Integrations

- Payment processing (Stripe, PayPal, etc.)
- Email backends and transactional email services
- SMS and notification services
- Cloud storage (AWS S3, Google Cloud Storage, Azure)
- Search engines (Elasticsearch, Algolia)
- Monitoring and logging (Sentry, DataDog, New Relic)

## Behavioral Traits

- Follows Django's "batteries included" philosophy
- Emphasizes reusable, maintainable code
- Prioritizes security and performance equally
- Uses Django's built-in features before reaching for third-party packages
- Writes comprehensive tests for all critical paths
- Documents code with clear docstrings and type hints
- Follows PEP 8 and Django coding style
- Implements proper error handling and logging
- Considers database implications of all ORM operations
- Uses Django's migration system effectively

## Knowledge Base

- Django 5.x documentation and release notes
- Django REST Framework patterns and best practices
- PostgreSQL optimization for Django
- Python 3.11+ features and type hints
- Modern deployment strategies for Django
- Django security best practices and OWASP guidelines
- Celery and distributed task processing
- Redis for caching and message queuing
- Docker and container orchestration
- Modern frontend integration patterns

## Response Approach

1. **Analyze requirements** for Django-specific considerations
2. **Suggest Django-idiomatic solutions** using built-in features
3. **Provide production-ready code** with proper error handling
4. **Include tests** for the implemented functionality
5. **Consider performance implications** of database queries
6. **Document security considerations** when relevant
7. **Offer migration strategies** for database changes
8. **Suggest deployment configurations** when applicable

## Example Interactions

- "Help me optimize this Django queryset that's causing N+1 queries"
- "Design a scalable Django architecture for a multi-tenant SaaS application"
- "Implement async views for handling long-running API requests"
- "Create a custom Django admin interface with inline formsets"
- "Set up Django Channels for real-time notifications"
- "Optimize database queries for a high-traffic Django application"
- "Implement JWT authentication with refresh tokens in DRF"
- "Create a robust background task system with Celery"

## Imported Module: Expo Dev Client
---
name: expo-dev-client
description: Build and distribute Expo development clients locally or via TestFlight
version: 1.0.0
license: MIT
---

Use EAS Build to create development clients for testing native code changes on physical devices. Use this for creating custom Expo Go clients for testing branches of your app.

## Important: When Development Clients Are Needed

**Only create development clients when your app requires custom native code.** Most apps work fine in Expo Go.

You need a dev client ONLY when using:
- Local Expo modules (custom native code)
- Apple targets (widgets, app clips, extensions)
- Third-party native modules not in Expo Go

**Try Expo Go first** with `npx expo start`. If everything works, you don't need a dev client.

## EAS Configuration

Ensure `eas.json` has a development profile:

```json
{
  "cli": {
    "version": ">= 16.0.1",
    "appVersionSource": "remote"
  },
  "build": {
    "production": {
      "autoIncrement": true
    },
    "development": {
      "autoIncrement": true,
      "developmentClient": true
    }
  },
  "submit": {
    "production": {},
    "development": {}
  }
}
```

Key settings:
- `developmentClient: true` - Bundles expo-dev-client for development builds
- `autoIncrement: true` - Automatically increments build numbers
- `appVersionSource: "remote"` - Uses EAS as the source of truth for version numbers

## Building for TestFlight

Build iOS dev client and submit to TestFlight in one command:

```bash
eas build -p ios --profile development --submit
```

This will:
1. Build the development client in the cloud
2. Automatically submit to App Store Connect
3. Send you an email when the build is ready in TestFlight

After receiving the TestFlight email:
1. Download the build from TestFlight on your device
2. Launch the app to see the expo-dev-client UI
3. Connect to your local Metro bundler or scan a QR code

## Building Locally

Build a development client on your machine:

```bash
# iOS (requires Xcode)
eas build -p ios --profile development --local

# Android
eas build -p android --profile development --local
```

Local builds output:
- iOS: `.ipa` file
- Android: `.apk` or `.aab` file

## Installing Local Builds

Install iOS build on simulator:

```bash
# Find the .app in the .tar.gz output
tar -xzf build-*.tar.gz
xcrun simctl install booted ./path/to/App.app
```

Install iOS build on device (requires signing):

```bash
# Use Xcode Devices window or ideviceinstaller
ideviceinstaller -i build.ipa
```

Install Android build:

```bash
adb install build.apk
```

## Building for Specific Platform

```bash
# iOS only
eas build -p ios --profile development

# Android only
eas build -p android --profile development

# Both platforms
eas build --profile development
```

## Checking Build Status

```bash
# List recent builds
eas build:list

# View build details
eas build:view
```

## Using the Dev Client

Once installed, the dev client provides:
- **Development server connection** - Enter your Metro bundler URL or scan QR
- **Build information** - View native build details
- **Launcher UI** - Switch between development servers

Connect to local development:

```bash
# Start Metro bundler
npx expo start --dev-client

# Scan QR code with dev client or enter URL manually
```

## Troubleshooting

**Build fails with signing errors:**
```bash
eas credentials
```

**Clear build cache:**
```bash
eas build -p ios --profile development --clear-cache
```

**Check EAS CLI version:**
```bash
eas --version
eas update
```

## Imported Module: Expo Tailwind Setup
---
name: expo-tailwind-setup
description: Set up Tailwind CSS v4 in Expo with react-native-css and NativeWind v5 for universal styling
version: 1.0.0
license: MIT
---

# Tailwind CSS Setup for Expo with react-native-css

This guide covers setting up Tailwind CSS v4 in Expo using react-native-css and NativeWind v5 for universal styling across iOS, Android, and Web.

## Overview

This setup uses:

- **Tailwind CSS v4** - Modern CSS-first configuration
- **react-native-css** - CSS runtime for React Native
- **NativeWind v5** - Metro transformer for Tailwind in React Native
- **@tailwindcss/postcss** - PostCSS plugin for Tailwind v4

## Installation

```bash
# Install dependencies
npx expo install tailwindcss@^4 nativewind@5.0.0-preview.2 react-native-css@0.0.0-nightly.5ce6396 @tailwindcss/postcss tailwind-merge clsx
```

Add resolutions for lightningcss compatibility:

```json
// package.json
{
  "resolutions": {
    "lightningcss": "1.30.1"
  }
}
```

- autoprefixer is not needed in Expo because of lightningcss
- postcss is included in expo by default

## Configuration Files

### Metro Config

Create or update `metro.config.js`:

```js
// metro.config.js
const { getDefaultConfig } = require("expo/metro-config");
const { withNativewind } = require("nativewind/metro");

/** @type {import('expo/metro-config').MetroConfig} */
const config = getDefaultConfig(__dirname);

module.exports = withNativewind(config, {
  // inline variables break PlatformColor in CSS variables
  inlineVariables: false,
  // We add className support manually
  globalClassNamePolyfill: false,
});
```

### PostCSS Config

Create `postcss.config.mjs`:

```js
// postcss.config.mjs
export default {
  plugins: {
    "@tailwindcss/postcss": {},
  },
};
```

### Global CSS

Create `src/global.css`:

```css
@import "tailwindcss/theme.css" layer(theme);
@import "tailwindcss/preflight.css" layer(base);
@import "tailwindcss/utilities.css";

/* Platform-specific font families */
@media android {
  :root {
    --font-mono: monospace;
    --font-rounded: normal;
    --font-serif: serif;
    --font-sans: normal;
  }
}

@media ios {
  :root {
    --font-mono: ui-monospace;
    --font-serif: ui-serif;
    --font-sans: system-ui;
    --font-rounded: ui-rounded;
  }
}
```

## IMPORTANT: No Babel Config Needed

With Tailwind v4 and NativeWind v5, you do NOT need a babel.config.js for Tailwind. Remove any NativeWind babel presets if present:

```js
// DELETE babel.config.js if it only contains NativeWind config
// The following is NO LONGER needed:
// module.exports = function (api) {
//   api.cache(true);
//   return {
//     presets: [
//       ["babel-preset-expo", { jsxImportSource: "nativewind" }],
//       "nativewind/babel",
//     ],
//   };
// };
```

## CSS Component Wrappers

Since react-native-css requires explicit CSS element wrapping, create reusable components:

### Main Components (`src/tw/index.tsx`)

```tsx
import {
  useCssElement,
  useNativeVariable as useFunctionalVariable,
} from "react-native-css";

import { Link as RouterLink } from "expo-router";
import Animated from "react-native-reanimated";
import React from "react";
import {
  View as RNView,
  Text as RNText,
  Pressable as RNPressable,
  ScrollView as RNScrollView,
  TouchableHighlight as RNTouchableHighlight,
  TextInput as RNTextInput,
  StyleSheet,
} from "react-native";

// CSS-enabled Link
export const Link = (
  props: React.ComponentProps<typeof RouterLink> & { className?: string }
) => {
  return useCssElement(RouterLink, props, { className: "style" });
};

Link.Trigger = RouterLink.Trigger;
Link.Menu = RouterLink.Menu;
Link.MenuAction = RouterLink.MenuAction;
Link.Preview = RouterLink.Preview;

// CSS Variable hook
export const useCSSVariable =
  process.env.EXPO_OS !== "web"
    ? useFunctionalVariable
    : (variable: string) => `var(${variable})`;

// View
export type ViewProps = React.ComponentProps<typeof RNView> & {
  className?: string;
};

export const View = (props: ViewProps) => {
  return useCssElement(RNView, props, { className: "style" });
};
View.displayName = "CSS(View)";

// Text
export const Text = (
  props: React.ComponentProps<typeof RNText> & { className?: string }
) => {
  return useCssElement(RNText, props, { className: "style" });
};
Text.displayName = "CSS(Text)";

// ScrollView
export const ScrollView = (
  props: React.ComponentProps<typeof RNScrollView> & {
    className?: string;
    contentContainerClassName?: string;
  }
) => {
  return useCssElement(RNScrollView, props, {
    className: "style",
    contentContainerClassName: "contentContainerStyle",
  });
};
ScrollView.displayName = "CSS(ScrollView)";

// Pressable
export const Pressable = (
  props: React.ComponentProps<typeof RNPressable> & { className?: string }
) => {
  return useCssElement(RNPressable, props, { className: "style" });
};
Pressable.displayName = "CSS(Pressable)";

// TextInput
export const TextInput = (
  props: React.ComponentProps<typeof RNTextInput> & { className?: string }
) => {
  return useCssElement(RNTextInput, props, { className: "style" });
};
TextInput.displayName = "CSS(TextInput)";

// AnimatedScrollView
export const AnimatedScrollView = (
  props: React.ComponentProps<typeof Animated.ScrollView> & {
    className?: string;
    contentClassName?: string;
    contentContainerClassName?: string;
  }
) => {
  return useCssElement(Animated.ScrollView, props, {
    className: "style",
    contentClassName: "contentContainerStyle",
    contentContainerClassName: "contentContainerStyle",
  });
};

// TouchableHighlight with underlayColor extraction
function XXTouchableHighlight(
  props: React.ComponentProps<typeof RNTouchableHighlight>
) {
  const { underlayColor, ...style } = StyleSheet.flatten(props.style) || {};
  return (
    <RNTouchableHighlight
      underlayColor={underlayColor}
      {...props}
      style={style}
    />
  );
}

export const TouchableHighlight = (
  props: React.ComponentProps<typeof RNTouchableHighlight>
) => {
  return useCssElement(XXTouchableHighlight, props, { className: "style" });
};
TouchableHighlight.displayName = "CSS(TouchableHighlight)";
```

### Image Component (`src/tw/image.tsx`)

```tsx
import { useCssElement } from "react-native-css";
import React from "react";
import { StyleSheet } from "react-native";
import Animated from "react-native-reanimated";
import { Image as RNImage } from "expo-image";

const AnimatedExpoImage = Animated.createAnimatedComponent(RNImage);

export type ImageProps = React.ComponentProps<typeof Image>;

function CSSImage(props: React.ComponentProps<typeof AnimatedExpoImage>) {
  // @ts-expect-error: Remap objectFit style to contentFit property
  const { objectFit, objectPosition, ...style } =
    StyleSheet.flatten(props.style) || {};

  return (
    <AnimatedExpoImage
      contentFit={objectFit}
      contentPosition={objectPosition}
      {...props}
      source={
        typeof props.source === "string" ? { uri: props.source } : props.source
      }
      // @ts-expect-error: Style is remapped above
      style={style}
    />
  );
}

export const Image = (
  props: React.ComponentProps<typeof CSSImage> & { className?: string }
) => {
  return useCssElement(CSSImage, props, { className: "style" });
};

Image.displayName = "CSS(Image)";
```

### Animated Components (`src/tw/animated.tsx`)

```tsx
import * as TW from "./index";
import RNAnimated from "react-native-reanimated";

export const Animated = {
  ...RNAnimated,
  View: RNAnimated.createAnimatedComponent(TW.View),
};
```

## Usage

Import CSS-wrapped components from your tw directory:

```tsx
import { View, Text, ScrollView, Image } from "@/tw";

export default function MyScreen() {
  return (
    <ScrollView className="flex-1 bg-white">
      <View className="p-4 gap-4">
        <Text className="text-xl font-bold text-gray-900">Hello Tailwind!</Text>
        <Image
          className="w-full h-48 rounded-lg object-cover"
          source={{ uri: "https://example.com/image.jpg" }}
        />
      </View>
    </ScrollView>
  );
}
```

## Custom Theme Variables

Add custom theme variables in your global.css using `@theme`:

```css
@layer theme {
  @theme {
    /* Custom fonts */
    --font-rounded: "SF Pro Rounded", sans-serif;

    /* Custom line heights */
    --text-xs--line-height: calc(1em / 0.75);
    --text-sm--line-height: calc(1.25em / 0.875);
    --text-base--line-height: calc(1.5em / 1);

    /* Custom leading scales */
    --leading-tight: 1.25em;
    --leading-snug: 1.375em;
    --leading-normal: 1.5em;
  }
}
```

## Platform-Specific Styles

Use platform media queries for platform-specific styling:

```css
@media ios {
  :root {
    --font-sans: system-ui;
    --font-rounded: ui-rounded;
  }
}

@media android {
  :root {
    --font-sans: normal;
    --font-rounded: normal;
  }
}
```

## Apple System Colors with CSS Variables

Create a CSS file for Apple semantic colors:

```css
/* src/css/sf.css */
@layer base {
  html {
    color-scheme: light;
  }
}

:root {
  /* Accent colors with light/dark mode */
  --sf-blue: light-dark(rgb(0 122 255), rgb(10 132 255));
  --sf-green: light-dark(rgb(52 199 89), rgb(48 209 89));
  --sf-red: light-dark(rgb(255 59 48), rgb(255 69 58));

  /* Gray scales */
  --sf-gray: light-dark(rgb(142 142 147), rgb(142 142 147));
  --sf-gray-2: light-dark(rgb(174 174 178), rgb(99 99 102));

  /* Text colors */
  --sf-text: light-dark(rgb(0 0 0), rgb(255 255 255));
  --sf-text-2: light-dark(rgb(60 60 67 / 0.6), rgb(235 235 245 / 0.6));

  /* Background colors */
  --sf-bg: light-dark(rgb(255 255 255), rgb(0 0 0));
  --sf-bg-2: light-dark(rgb(242 242 247), rgb(28 28 30));
}

/* iOS native colors via platformColor */
@media ios {
  :root {
    --sf-blue: platformColor(systemBlue);
    --sf-green: platformColor(systemGreen);
    --sf-red: platformColor(systemRed);
    --sf-gray: platformColor(systemGray);
    --sf-text: platformColor(label);
    --sf-text-2: platformColor(secondaryLabel);
    --sf-bg: platformColor(systemBackground);
    --sf-bg-2: platformColor(secondarySystemBackground);
  }
}

/* Register as Tailwind theme colors */
@layer theme {
  @theme {
    --color-sf-blue: var(--sf-blue);
    --color-sf-green: var(--sf-green);
    --color-sf-red: var(--sf-red);
    --color-sf-gray: var(--sf-gray);
    --color-sf-text: var(--sf-text);
    --color-sf-text-2: var(--sf-text-2);
    --color-sf-bg: var(--sf-bg);
    --color-sf-bg-2: var(--sf-bg-2);
  }
}
```

Then use in components:

```tsx
<Text className="text-sf-text">Primary text</Text>
<Text className="text-sf-text-2">Secondary text</Text>
<View className="bg-sf-bg">...</View>
```

## Using CSS Variables in JavaScript

Use the `useCSSVariable` hook:

```tsx
import { useCSSVariable } from "@/tw";

function MyComponent() {
  const blue = useCSSVariable("--sf-blue");

  return <View style={{ borderColor: blue }} />;
}
```

## Key Differences from NativeWind v4 / Tailwind v3

1. **No babel.config.js** - Configuration is now CSS-first
2. **PostCSS plugin** - Uses `@tailwindcss/postcss` instead of `tailwindcss`
3. **CSS imports** - Use `@import "tailwindcss/..."` instead of `@tailwind` directives
4. **Theme config** - Use `@theme` in CSS instead of `tailwind.config.js`
5. **Component wrappers** - Must wrap components with `useCssElement` for className support
6. **Metro config** - Use `withNativewind` with different options (`inlineVariables: false`)

## Troubleshooting

### Styles not applying

1. Ensure you have the CSS file imported in your app entry
2. Check that components are wrapped with `useCssElement`
3. Verify Metro config has `withNativewind` applied

### Platform colors not working

1. Use `platformColor()` in `@media ios` blocks
2. Fall back to `light-dark()` for web/Android

### TypeScript errors

Add className to component props:

```tsx
type Props = React.ComponentProps<typeof RNView> & { className?: string };
```

## Imported Module: Firebase
---
name: firebase
description: "Firebase gives you a complete backend in minutes - auth, database, storage, functions, hosting. But the ease of setup hides real complexity. Security rules are your last line of defense, and they'r..."
risk: unknown
source: "vibeship-spawner-skills (Apache 2.0)"
date_added: "2026-02-27"
---

# Firebase

You're a developer who has shipped dozens of Firebase projects. You've seen the
"easy" path lead to security breaches, runaway costs, and impossible migrations.
You know Firebase is powerful, but you also know its sharp edges.

Your hard-won lessons: The team that skipped security rules got pwned. The team
that designed Firestore like SQL couldn't query their data. The team that
attached listeners to large collections got a $10k bill. You've learned from
all of them.

You advocate for Firebase w

## Capabilities

- firebase-auth
- firestore
- firebase-realtime-database
- firebase-cloud-functions
- firebase-storage
- firebase-hosting
- firebase-security-rules
- firebase-admin-sdk
- firebase-emulators

## Patterns

### Modular SDK Import

Import only what you need for smaller bundles

### Security Rules Design

Secure your data with proper rules from day one

### Data Modeling for Queries

Design Firestore data structure around query patterns

## Anti-Patterns

### ❌ No Security Rules

### ❌ Client-Side Admin Operations

### ❌ Listener on Large Collections

## Related Skills

Works well with: `nextjs-app-router`, `react-patterns`, `authentication-oauth`, `stripe`

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

## Imported Module: Hubspot Integration
---
name: hubspot-integration
description: "Expert patterns for HubSpot CRM integration including OAuth authentication, CRM objects, associations, batch operations, webhooks, and custom objects. Covers Node.js and Python SDKs. Use when: hubs..."
risk: unknown
source: "vibeship-spawner-skills (Apache 2.0)"
date_added: "2026-02-27"
---

# HubSpot Integration

## Patterns

### OAuth 2.0 Authentication

Secure authentication for public apps

### Private App Token

Authentication for single-account integrations

### CRM Object CRUD Operations

Create, read, update, delete CRM records

## Anti-Patterns

### ❌ Using Deprecated API Keys

### ❌ Individual Requests Instead of Batch

### ❌ Polling Instead of Webhooks

## ⚠️ Sharp Edges

| Issue | Severity | Solution |
|-------|----------|----------|
| Issue | high | See docs |
| Issue | high | See docs |
| Issue | critical | See docs |
| Issue | high | See docs |
| Issue | critical | See docs |
| Issue | medium | See docs |
| Issue | high | See docs |
| Issue | medium | See docs |

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

## Imported Module: M365 Agents Dotnet
---
name: m365-agents-dotnet
description: Microsoft 365 Agents SDK for .NET. Build multichannel agents for Teams/M365/Copilot Studio with ASP.NET Core hosting, AgentApplication routing, and MSAL-based auth.
risk: unknown
source: community
date_added: '2026-02-27'
---

# Microsoft 365 Agents SDK (.NET)

## Overview
Build enterprise agents for Microsoft 365, Teams, and Copilot Studio using the Microsoft.Agents SDK with ASP.NET Core hosting, agent routing, and MSAL-based authentication.

## Before implementation
- Use the microsoft-docs MCP to verify the latest APIs for AddAgent, AgentApplication, and authentication options.
- Confirm package versions in NuGet for the Microsoft.Agents.* packages you plan to use.

## Installation

```bash
dotnet add package Microsoft.Agents.Hosting.AspNetCore
dotnet add package Microsoft.Agents.Authentication.Msal
dotnet add package Microsoft.Agents.Storage
dotnet add package Microsoft.Agents.CopilotStudio.Client
dotnet add package Microsoft.Identity.Client.Extensions.Msal
```

## Configuration (appsettings.json)

```json
{
  "TokenValidation": {
    "Enabled": true,
    "Audiences": [
      "{{ClientId}}"
    ],
    "TenantId": "{{TenantId}}"
  },
  "AgentApplication": {
    "StartTypingTimer": false,
    "RemoveRecipientMention": false,
    "NormalizeMentions": false
  },
  "Connections": {
    "ServiceConnection": {
      "Settings": {
        "AuthType": "ClientSecret",
        "ClientId": "{{ClientId}}",
        "ClientSecret": "{{ClientSecret}}",
        "AuthorityEndpoint": "https://login.microsoftonline.com/{{TenantId}}",
        "Scopes": [
          "https://api.botframework.com/.default"
        ]
      }
    }
  },
  "ConnectionsMap": [
    {
      "ServiceUrl": "*",
      "Connection": "ServiceConnection"
    }
  ],
  "CopilotStudioClientSettings": {
    "DirectConnectUrl": "",
    "EnvironmentId": "",
    "SchemaName": "",
    "TenantId": "",
    "AppClientId": "",
    "AppClientSecret": ""
  }
}
```

## Core Workflow: ASP.NET Core agent host

```csharp
using Microsoft.Agents.Builder;
using Microsoft.Agents.Hosting.AspNetCore;
using Microsoft.Agents.Storage;
using Microsoft.AspNetCore.Builder;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

var builder = WebApplication.CreateBuilder(args);

builder.Services.AddHttpClient();
builder.AddAgentApplicationOptions();
builder.AddAgent<MyAgent>();
builder.Services.AddSingleton<IStorage, MemoryStorage>();

builder.Services.AddControllers();
builder.Services.AddAgentAspNetAuthentication(builder.Configuration);

WebApplication app = builder.Build();

app.UseAuthentication();
app.UseAuthorization();

app.MapGet("/", () => "Microsoft Agents SDK Sample");

var incomingRoute = app.MapPost("/api/messages",
    async (HttpRequest request, HttpResponse response, IAgentHttpAdapter adapter, IAgent agent, CancellationToken ct) =>
    {
        await adapter.ProcessAsync(request, response, agent, ct);
    });

if (!app.Environment.IsDevelopment())
{
    incomingRoute.RequireAuthorization();
}
else
{
    app.Urls.Add("http://localhost:3978");
}

app.Run();
```

## AgentApplication routing

```csharp
using Microsoft.Agents.Builder;
using Microsoft.Agents.Builder.App;
using Microsoft.Agents.Builder.State;
using Microsoft.Agents.Core.Models;
using System;
using System.Threading;
using System.Threading.Tasks;

public sealed class MyAgent : AgentApplication
{
    public MyAgent(AgentApplicationOptions options) : base(options)
    {
        OnConversationUpdate(ConversationUpdateEvents.MembersAdded, WelcomeAsync);
        OnActivity(ActivityTypes.Message, OnMessageAsync, rank: RouteRank.Last);
        OnTurnError(OnTurnErrorAsync);
    }

    private static async Task WelcomeAsync(ITurnContext turnContext, ITurnState turnState, CancellationToken ct)
    {
        foreach (ChannelAccount member in turnContext.Activity.MembersAdded)
        {
            if (member.Id != turnContext.Activity.Recipient.Id)
            {
                await turnContext.SendActivityAsync(
                    MessageFactory.Text("Welcome to the agent."),
                    ct);
            }
        }
    }

    private static async Task OnMessageAsync(ITurnContext turnContext, ITurnState turnState, CancellationToken ct)
    {
        await turnContext.SendActivityAsync(
            MessageFactory.Text($"You said: {turnContext.Activity.Text}"),
            ct);
    }

    private static async Task OnTurnErrorAsync(
        ITurnContext turnContext,
        ITurnState turnState,
        Exception exception,
        CancellationToken ct)
    {
        await turnState.Conversation.DeleteStateAsync(turnContext, ct);

        var endOfConversation = Activity.CreateEndOfConversationActivity();
        endOfConversation.Code = EndOfConversationCodes.Error;
        endOfConversation.Text = exception.Message;
        await turnContext.SendActivityAsync(endOfConversation, ct);
    }
}
```

## Copilot Studio direct-to-engine client

### DelegatingHandler for token acquisition (interactive flow)

```csharp
using System.Net.Http.Headers;
using Microsoft.Agents.CopilotStudio.Client;
using Microsoft.Identity.Client;

internal sealed class AddTokenHandler : DelegatingHandler
{
    private readonly SampleConnectionSettings _settings;

    public AddTokenHandler(SampleConnectionSettings settings) : base(new HttpClientHandler())
    {
        _settings = settings;
    }

    protected override async Task<HttpResponseMessage> SendAsync(
        HttpRequestMessage request,
        CancellationToken cancellationToken)
    {
        if (request.Headers.Authorization is null)
        {
            string[] scopes = [CopilotClient.ScopeFromSettings(_settings)];

            IPublicClientApplication app = PublicClientApplicationBuilder
                .Create(_settings.AppClientId)
                .WithAuthority(AadAuthorityAudience.AzureAdMyOrg)
                .WithTenantId(_settings.TenantId)
                .WithRedirectUri("http://localhost")
                .Build();

            AuthenticationResult authResponse;
            try
            {
                var account = (await app.GetAccountsAsync()).FirstOrDefault();
                authResponse = await app.AcquireTokenSilent(scopes, account).ExecuteAsync(cancellationToken);
            }
            catch (MsalUiRequiredException)
            {
                authResponse = await app.AcquireTokenInteractive(scopes).ExecuteAsync(cancellationToken);
            }

            request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", authResponse.AccessToken);
        }

        return await base.SendAsync(request, cancellationToken);
    }
}
```

### Console host with CopilotClient

```csharp
using Microsoft.Agents.CopilotStudio.Client;
using Microsoft.Extensions.DependencyInjection;
using Microsoft.Extensions.Hosting;

HostApplicationBuilder builder = Host.CreateApplicationBuilder(args);

var settings = new SampleConnectionSettings(
    builder.Configuration.GetSection("CopilotStudioClientSettings"));

builder.Services.AddHttpClient("mcs").ConfigurePrimaryHttpMessageHandler(() =>
{
    return new AddTokenHandler(settings);
});

builder.Services
    .AddSingleton(settings)
    .AddTransient<CopilotClient>(sp =>
    {
        var logger = sp.GetRequiredService<ILoggerFactory>().CreateLogger<CopilotClient>();
        return new CopilotClient(settings, sp.GetRequiredService<IHttpClientFactory>(), logger, "mcs");
    });

IHost host = builder.Build();
var client = host.Services.GetRequiredService<CopilotClient>();

await foreach (var activity in client.StartConversationAsync(emitStartConversationEvent: true))
{
    Console.WriteLine(activity.Type);
}

await foreach (var activity in client.AskQuestionAsync("Hello!", null))
{
    Console.WriteLine(activity.Type);
}
```

## Best Practices

1. Use AgentApplication subclasses to centralize routing and error handling.
2. Use MemoryStorage only for development; use persisted storage in production.
3. Enable TokenValidation in production and require authorization on /api/messages.
4. Keep auth secrets in configuration providers (Key Vault, managed identity, env vars).
5. Reuse HttpClient from IHttpClientFactory and cache MSAL tokens.
6. Prefer async handlers and pass CancellationToken to SDK calls.

## Reference Files

| File | Contents |
| --- | --- |
| references/acceptance-criteria.md | Import paths, hosting pipeline, Copilot Studio client patterns, anti-patterns |

## Reference Links

| Resource | URL |
| --- | --- |
| Microsoft 365 Agents SDK | https://learn.microsoft.com/en-us/microsoft-365/agents-sdk/ |
| AddAgent API | https://learn.microsoft.com/en-us/dotnet/api/microsoft.agents.hosting.aspnetcore.servicecollectionextensions.addagent?view=m365-agents-sdk |
| AgentApplication API | https://learn.microsoft.com/en-us/dotnet/api/microsoft.agents.builder.app.agentapplication?view=m365-agents-sdk |
| Auth configuration options | https://learn.microsoft.com/en-us/microsoft-365/agents-sdk/microsoft-authentication-library-configuration-options |
| Copilot Studio integration | https://learn.microsoft.com/en-us/microsoft-365/agents-sdk/integrate-with-mcs |
| GitHub samples | https://github.com/microsoft/agents |

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

## Imported Module: M365 Agents Py
---
name: m365-agents-py
description: Microsoft 365 Agents SDK for Python. Build multichannel agents for Teams/M365/Copilot Studio with aiohttp hosting, AgentApplication routing, streaming responses, and MSAL-based auth.
risk: unknown
source: community
date_added: '2026-02-27'
---

# Microsoft 365 Agents SDK (Python)

Build enterprise agents for Microsoft 365, Teams, and Copilot Studio using the Microsoft Agents SDK with aiohttp hosting, AgentApplication routing, streaming responses, and MSAL-based authentication.

## Before implementation
- Use the microsoft-docs MCP to verify the latest API signatures for AgentApplication, start_agent_process, and authentication options.
- Confirm package versions on PyPI for the microsoft-agents-* packages you plan to use.

## Important Notice - Import Changes

> **⚠️ Breaking Change**: Recent updates have changed the Python import structure from `microsoft.agents` to `microsoft_agents` (using underscores instead of dots).

## Installation

```bash
pip install microsoft-agents-hosting-core
pip install microsoft-agents-hosting-aiohttp
pip install microsoft-agents-activity
pip install microsoft-agents-authentication-msal
pip install microsoft-agents-copilotstudio-client
pip install python-dotenv aiohttp
```

## Environment Variables (.env)

```bash
CONNECTIONS__SERVICE_CONNECTION__SETTINGS__CLIENTID=<client-id>
CONNECTIONS__SERVICE_CONNECTION__SETTINGS__CLIENTSECRET=<client-secret>
CONNECTIONS__SERVICE_CONNECTION__SETTINGS__TENANTID=<tenant-id>

# Optional: OAuth handlers for auto sign-in
AGENTAPPLICATION__USERAUTHORIZATION__HANDLERS__GRAPH__SETTINGS__AZUREBOTOAUTHCONNECTIONNAME=<connection-name>

# Optional: Azure OpenAI for streaming
AZURE_OPENAI_ENDPOINT=<endpoint>
AZURE_OPENAI_API_VERSION=<version>
AZURE_OPENAI_API_KEY=<key>

# Optional: Copilot Studio client
COPILOTSTUDIOAGENT__ENVIRONMENTID=<environment-id>
COPILOTSTUDIOAGENT__SCHEMANAME=<schema-name>
COPILOTSTUDIOAGENT__TENANTID=<tenant-id>
COPILOTSTUDIOAGENT__AGENTAPPID=<app-id>
```

## Core Workflow: aiohttp-hosted AgentApplication

```python
import logging
from os import environ

from dotenv import load_dotenv
from aiohttp.web import Request, Response, Application, run_app

from microsoft_agents.activity import load_configuration_from_env
from microsoft_agents.hosting.core import (
    Authorization,
    AgentApplication,
    TurnState,
    TurnContext,
    MemoryStorage,
)
from microsoft_agents.hosting.aiohttp import (
    CloudAdapter,
    start_agent_process,
    jwt_authorization_middleware,
)
from microsoft_agents.authentication.msal import MsalConnectionManager

# Enable logging
ms_agents_logger = logging.getLogger("microsoft_agents")
ms_agents_logger.addHandler(logging.StreamHandler())
ms_agents_logger.setLevel(logging.INFO)

# Load configuration
load_dotenv()
agents_sdk_config = load_configuration_from_env(environ)

# Create storage and connection manager
STORAGE = MemoryStorage()
CONNECTION_MANAGER = MsalConnectionManager(**agents_sdk_config)
ADAPTER = CloudAdapter(connection_manager=CONNECTION_MANAGER)
AUTHORIZATION = Authorization(STORAGE, CONNECTION_MANAGER, **agents_sdk_config)

# Create AgentApplication
AGENT_APP = AgentApplicationTurnState


@AGENT_APP.conversation_update("membersAdded")
async def on_members_added(context: TurnContext, _state: TurnState):
    await context.send_activity("Welcome to the agent!")


@AGENT_APP.activity("message")
async def on_message(context: TurnContext, _state: TurnState):
    await context.send_activity(f"You said: {context.activity.text}")


@AGENT_APP.error
async def on_error(context: TurnContext, error: Exception):
    await context.send_activity("The agent encountered an error.")


# Server setup
async def entry_point(req: Request) -> Response:
    agent: AgentApplication = req.app["agent_app"]
    adapter: CloudAdapter = req.app["adapter"]
    return await start_agent_process(req, agent, adapter)


APP = Application(middlewares=[jwt_authorization_middleware])
APP.router.add_post("/api/messages", entry_point)
APP["agent_configuration"] = CONNECTION_MANAGER.get_default_connection_configuration()
APP["agent_app"] = AGENT_APP
APP["adapter"] = AGENT_APP.adapter

if __name__ == "__main__":
    run_app(APP, host="localhost", port=environ.get("PORT", 3978))
```

## AgentApplication Routing

```python
import re
from microsoft_agents.hosting.core import (
    AgentApplication, TurnState, TurnContext, MessageFactory
)
from microsoft_agents.activity import ActivityTypes

AGENT_APP = AgentApplicationTurnState

# Welcome handler
@AGENT_APP.conversation_update("membersAdded")
async def on_members_added(context: TurnContext, _state: TurnState):
    await context.send_activity("Welcome!")

# Regex-based message handler
@AGENT_APP.message(re.compile(r"^hello$", re.IGNORECASE))
async def on_hello(context: TurnContext, _state: TurnState):
    await context.send_activity("Hello!")

# Simple string message handler
@AGENT_APP.message("/status")
async def on_status(context: TurnContext, _state: TurnState):
    await context.send_activity("Status: OK")

# Auth-protected message handler
@AGENT_APP.message("/me", auth_handlers=["GRAPH"])
async def on_profile(context: TurnContext, state: TurnState):
    token_response = await AGENT_APP.auth.get_token(context, "GRAPH")
    if token_response and token_response.token:
        # Use token to call Graph API
        await context.send_activity("Profile retrieved")

# Invoke activity handler
@AGENT_APP.activity(ActivityTypes.invoke)
async def on_invoke(context: TurnContext, _state: TurnState):
    invoke_response = Activity(
        type=ActivityTypes.invoke_response, value={"status": 200}
    )
    await context.send_activity(invoke_response)

# Fallback message handler
@AGENT_APP.activity("message")
async def on_message(context: TurnContext, _state: TurnState):
    await context.send_activity(f"Echo: {context.activity.text}")

# Error handler
@AGENT_APP.error
async def on_error(context: TurnContext, error: Exception):
    await context.send_activity("An error occurred.")
```

## Streaming Responses with Azure OpenAI

```python
from openai import AsyncAzureOpenAI
from microsoft_agents.activity import SensitivityUsageInfo

CLIENT = AsyncAzureOpenAI(
    api_version=environ["AZURE_OPENAI_API_VERSION"],
    azure_endpoint=environ["AZURE_OPENAI_ENDPOINT"],
    api_key=environ["AZURE_OPENAI_API_KEY"]
)

@AGENT_APP.message("poem")
async def on_poem_message(context: TurnContext, _state: TurnState):
    # Configure streaming response
    context.streaming_response.set_feedback_loop(True)
    context.streaming_response.set_generated_by_ai_label(True)
    context.streaming_response.set_sensitivity_label(
        SensitivityUsageInfo(
            type="https://schema.org/Message",
            schema_type="CreativeWork",
            name="Internal",
        )
    )
    context.streaming_response.queue_informative_update("Starting a poem...\n")

    # Stream from Azure OpenAI
    streamed_response = await CLIENT.chat.completions.create(
        model="gpt-4o",
        messages=[
            {"role": "system", "content": "You are a creative assistant."},
            {"role": "user", "content": "Write a poem about Python."}
        ],
        stream=True,
    )
    
    try:
        async for chunk in streamed_response:
            if chunk.choices and chunk.choices[0].delta.content:
                context.streaming_response.queue_text_chunk(
                    chunk.choices[0].delta.content
                )
    finally:
        await context.streaming_response.end_stream()
```

## OAuth / Auto Sign-In

```python
@AGENT_APP.message("/logout")
async def logout(context: TurnContext, state: TurnState):
    await AGENT_APP.auth.sign_out(context, "GRAPH")
    await context.send_activity(MessageFactory.text("You have been logged out."))


@AGENT_APP.message("/me", auth_handlers=["GRAPH"])
async def profile_request(context: TurnContext, state: TurnState):
    user_token_response = await AGENT_APP.auth.get_token(context, "GRAPH")
    if user_token_response and user_token_response.token:
        # Use token to call Microsoft Graph
        async with aiohttp.ClientSession() as session:
            headers = {
                "Authorization": f"Bearer {user_token_response.token}",
                "Content-Type": "application/json",
            }
            async with session.get(
                "https://graph.microsoft.com/v1.0/me", headers=headers
            ) as response:
                if response.status == 200:
                    user_info = await response.json()
                    await context.send_activity(f"Hello, {user_info['displayName']}!")
```

## Copilot Studio Client (Direct to Engine)

```python
import asyncio
from msal import PublicClientApplication
from microsoft_agents.activity import ActivityTypes, load_configuration_from_env
from microsoft_agents.copilotstudio.client import (
    ConnectionSettings,
    CopilotClient,
)

# Token cache (local file for interactive flows)
class LocalTokenCache:
    # See samples for full implementation
    pass

def acquire_token(settings, app_client_id, tenant_id):
    pca = PublicClientApplication(
        client_id=app_client_id,
        authority=f"https://login.microsoftonline.com/{tenant_id}",
    )
    
    token_request = {"scopes": ["https://api.powerplatform.com/.default"]}
    accounts = pca.get_accounts()
    
    if accounts:
        response = pca.acquire_token_silent(token_request["scopes"], account=accounts[0])
        return response.get("access_token")
    else:
        response = pca.acquire_token_interactive(**token_request)
        return response.get("access_token")


async def main():
    settings = ConnectionSettings(
        environment_id=environ.get("COPILOTSTUDIOAGENT__ENVIRONMENTID"),
        agent_identifier=environ.get("COPILOTSTUDIOAGENT__SCHEMANAME"),
    )
    
    token = acquire_token(
        settings,
        app_client_id=environ.get("COPILOTSTUDIOAGENT__AGENTAPPID"),
        tenant_id=environ.get("COPILOTSTUDIOAGENT__TENANTID"),
    )
    
    copilot_client = CopilotClient(settings, token)
    
    # Start conversation
    act = copilot_client.start_conversation(True)
    async for action in act:
        if action.text:
            print(action.text)
    
    # Ask question
    replies = copilot_client.ask_question("Hello!", action.conversation.id)
    async for reply in replies:
        if reply.type == ActivityTypes.message:
            print(reply.text)


asyncio.run(main())
```

## Best Practices

1. Use `microsoft_agents` import prefix (underscores, not dots).
2. Use `MemoryStorage` only for development; use BlobStorage or CosmosDB in production.
3. Always use `load_configuration_from_env(environ)` to load SDK configuration.
4. Include `jwt_authorization_middleware` in aiohttp Application middlewares.
5. Use `MsalConnectionManager` for MSAL-based authentication.
6. Call `end_stream()` in finally blocks when using streaming responses.
7. Use `auth_handlers` parameter on message decorators for OAuth-protected routes.
8. Keep secrets in environment variables, not in source code.

## Reference Files

| File | Contents |
| --- | --- |
| references/acceptance-criteria.md | Import paths, hosting pipeline, streaming, OAuth, and Copilot Studio patterns |

## Reference Links

| Resource | URL |
| --- | --- |
| Microsoft 365 Agents SDK | https://learn.microsoft.com/en-us/microsoft-365/agents-sdk/ |
| GitHub samples (Python) | https://github.com/microsoft/Agents-for-python |
| PyPI packages | https://pypi.org/search/?q=microsoft-agents |
| Integrate with Copilot Studio | https://learn.microsoft.com/en-us/microsoft-365/agents-sdk/integrate-with-mcs |

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

## Imported Module: M365 Agents Ts
---
name: m365-agents-ts
description: Microsoft 365 Agents SDK for TypeScript/Node.js.
risk: unknown
source: community
date_added: '2026-02-27'
---

# Microsoft 365 Agents SDK (TypeScript)

Build enterprise agents for Microsoft 365, Teams, and Copilot Studio using the Microsoft 365 Agents SDK with Express hosting, AgentApplication routing, streaming responses, and Copilot Studio client integrations.

## Before implementation
- Use the microsoft-docs MCP to verify the latest API signatures for AgentApplication, startServer, and CopilotStudioClient.
- Confirm package versions on npm before wiring up samples or templates.

## Installation

```bash
npm install @microsoft/agents-hosting @microsoft/agents-hosting-express @microsoft/agents-activity
npm install @microsoft/agents-copilotstudio-client
```

## Environment Variables

```bash
PORT=3978
AZURE_RESOURCE_NAME=<azure-openai-resource>
AZURE_API_KEY=<azure-openai-key>
AZURE_OPENAI_DEPLOYMENT_NAME=gpt-4o-mini

TENANT_ID=<tenant-id>
CLIENT_ID=<client-id>
CLIENT_SECRET=<client-secret>

COPILOT_ENVIRONMENT_ID=<environment-id>
COPILOT_SCHEMA_NAME=<schema-name>
COPILOT_CLIENT_ID=<copilot-app-client-id>
COPILOT_BEARER_TOKEN=<copilot-jwt>
```

## Core Workflow: Express-hosted AgentApplication

```typescript
import { AgentApplication, TurnContext, TurnState } from "@microsoft/agents-hosting";
import { startServer } from "@microsoft/agents-hosting-express";

const agent = new AgentApplication<TurnState>();

agent.onConversationUpdate("membersAdded", async (context: TurnContext) => {
  await context.sendActivity("Welcome to the agent.");
});

agent.onMessage("hello", async (context: TurnContext) => {
  await context.sendActivity(`Echo: ${context.activity.text}`);
});

startServer(agent);
```

## Streaming responses with Azure OpenAI

```typescript
import { azure } from "@ai-sdk/azure";
import { AgentApplication, TurnContext, TurnState } from "@microsoft/agents-hosting";
import { startServer } from "@microsoft/agents-hosting-express";
import { streamText } from "ai";

const agent = new AgentApplication<TurnState>();

agent.onMessage("poem", async (context: TurnContext) => {
  context.streamingResponse.setFeedbackLoop(true);
  context.streamingResponse.setGeneratedByAILabel(true);
  context.streamingResponse.setSensitivityLabel({
    type: "https://schema.org/Message",
    "@type": "CreativeWork",
    name: "Internal",
  });

  await context.streamingResponse.queueInformativeUpdate("starting a poem...");

  const { fullStream } = streamText({
    model: azure(process.env.AZURE_OPENAI_DEPLOYMENT_NAME || "gpt-4o-mini"),
    system: "You are a creative assistant.",
    prompt: "Write a poem about Apollo.",
  });

  try {
    for await (const part of fullStream) {
      if (part.type === "text-delta" && part.text.length > 0) {
        await context.streamingResponse.queueTextChunk(part.text);
      }
      if (part.type === "error") {
        throw new Error(`Streaming error: ${part.error}`);
      }
    }
  } finally {
    await context.streamingResponse.endStream();
  }
});

startServer(agent);
```

## Invoke activity handling

```typescript
import { Activity, ActivityTypes } from "@microsoft/agents-activity";
import { AgentApplication, TurnContext, TurnState } from "@microsoft/agents-hosting";

const agent = new AgentApplication<TurnState>();

agent.onActivity("invoke", async (context: TurnContext) => {
  const invokeResponse = Activity.fromObject({
    type: ActivityTypes.InvokeResponse,
    value: { status: 200 },
  });

  await context.sendActivity(invokeResponse);
  await context.sendActivity("Thanks for submitting your feedback.");
});
```

## Copilot Studio client (Direct to Engine)

```typescript
import { CopilotStudioClient } from "@microsoft/agents-copilotstudio-client";

const settings = {
  environmentId: process.env.COPILOT_ENVIRONMENT_ID!,
  schemaName: process.env.COPILOT_SCHEMA_NAME!,
  clientId: process.env.COPILOT_CLIENT_ID!,
};

const tokenProvider = async (): Promise<string> => {
  return process.env.COPILOT_BEARER_TOKEN!;
};

const client = new CopilotStudioClient(settings, tokenProvider);

const conversation = await client.startConversationAsync();
const reply = await client.askQuestionAsync("Hello!", conversation.id);
console.log(reply);
```

## Copilot Studio WebChat integration

```typescript
import { CopilotStudioWebChat } from "@microsoft/agents-copilotstudio-client";

const directLine = CopilotStudioWebChat.createConnection(client, {
  showTyping: true,
});

window.WebChat.renderWebChat({
  directLine,
}, document.getElementById("webchat")!);
```

## Best Practices

1. Use AgentApplication for routing and keep handlers focused on one responsibility.
2. Prefer streamingResponse for long-running completions and call endStream in finally blocks.
3. Keep secrets out of source code; load tokens from environment variables or secure stores.
4. Reuse CopilotStudioClient instances and cache tokens in your token provider.
5. Validate invoke payloads before logging or persisting feedback.

## Reference Files

| File | Contents |
| --- | --- |
| references/acceptance-criteria.md | Import paths, hosting pipeline, streaming, and Copilot Studio patterns |

## Reference Links

| Resource | URL |
| --- | --- |
| Microsoft 365 Agents SDK | https://learn.microsoft.com/en-us/microsoft-365/agents-sdk/ |
| JavaScript SDK overview | https://learn.microsoft.com/en-us/javascript/api/overview/agents-overview?view=agents-sdk-js-latest |
| @microsoft/agents-hosting-express | https://learn.microsoft.com/en-us/javascript/api/%40microsoft/agents-hosting-express?view=agents-sdk-js-latest |
| @microsoft/agents-copilotstudio-client | https://learn.microsoft.com/en-us/javascript/api/%40microsoft/agents-copilotstudio-client?view=agents-sdk-js-latest |
| Integrate with Copilot Studio | https://learn.microsoft.com/en-us/microsoft-365/agents-sdk/integrate-with-mcs |
| GitHub samples | https://github.com/microsoft/Agents/tree/main/samples/nodejs |

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

## Imported Module: Neon Postgres
---
name: neon-postgres
description: "Expert patterns for Neon serverless Postgres, branching, connection pooling, and Prisma/Drizzle integration Use when: neon database, serverless postgres, database branching, neon postgres, postgres..."
risk: unknown
source: "vibeship-spawner-skills (Apache 2.0)"
date_added: "2026-02-27"
---

# Neon Postgres

## Patterns

### Prisma with Neon Connection

Configure Prisma for Neon with connection pooling.

Use two connection strings:
- DATABASE_URL: Pooled connection for Prisma Client
- DIRECT_URL: Direct connection for Prisma Migrate

The pooled connection uses PgBouncer for up to 10K connections.
Direct connection required for migrations (DDL operations).


### Drizzle with Neon Serverless Driver

Use Drizzle ORM with Neon's serverless HTTP driver for
edge/serverless environments.

Two driver options:
- neon-http: Single queries over HTTP (fastest for one-off queries)
- neon-serverless: WebSocket for transactions and sessions


### Connection Pooling with PgBouncer

Neon provides built-in connection pooling via PgBouncer.

Key limits:
- Up to 10,000 concurrent connections to pooler
- Connections still consume underlying Postgres connections
- 7 connections reserved for Neon superuser

Use pooled endpoint for application, direct for migrations.


## ⚠️ Sharp Edges

| Issue | Severity | Solution |
|-------|----------|----------|
| Issue | high | See docs |
| Issue | high | See docs |
| Issue | high | See docs |
| Issue | medium | See docs |
| Issue | medium | See docs |
| Issue | low | See docs |
| Issue | medium | See docs |
| Issue | high | See docs |

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

## Imported Module: Nestjs Expert
---
name: nestjs-expert
description: "Nest.js framework expert specializing in module architecture, dependency injection, middleware, guards, interceptors, testing with Jest/Supertest, TypeORM/Mongoose integration, and Passport.js auth..."
category: framework
risk: unknown
source: community
date_added: "2026-02-27"
---

# Nest.js Expert

You are an expert in Nest.js with deep knowledge of enterprise-grade Node.js application architecture, dependency injection patterns, decorators, middleware, guards, interceptors, pipes, testing strategies, database integration, and authentication systems.

## When invoked:

0. If a more specialized expert fits better, recommend switching and stop:
   - Pure TypeScript type issues → typescript-type-expert
   - Database query optimization → database-expert  
   - Node.js runtime issues → nodejs-expert
   - Frontend React issues → react-expert
   
   Example: "This is a TypeScript type system issue. Use the typescript-type-expert subagent. Stopping here."

1. Detect Nest.js project setup using internal tools first (Read, Grep, Glob)
2. Identify architecture patterns and existing modules
3. Apply appropriate solutions following Nest.js best practices
4. Validate in order: typecheck → unit tests → integration tests → e2e tests

## Domain Coverage

### Module Architecture & Dependency Injection
- Common issues: Circular dependencies, provider scope conflicts, module imports
- Root causes: Incorrect module boundaries, missing exports, improper injection tokens
- Solution priority: 1) Refactor module structure, 2) Use forwardRef, 3) Adjust provider scope
- Tools: `nest generate module`, `nest generate service`
- Resources: [Nest.js Modules](https://docs.nestjs.com/modules), [Providers](https://docs.nestjs.com/providers)

### Controllers & Request Handling
- Common issues: Route conflicts, DTO validation, response serialization
- Root causes: Decorator misconfiguration, missing validation pipes, improper interceptors
- Solution priority: 1) Fix decorator configuration, 2) Add validation, 3) Implement interceptors
- Tools: `nest generate controller`, class-validator, class-transformer
- Resources: [Controllers](https://docs.nestjs.com/controllers), [Validation](https://docs.nestjs.com/techniques/validation)

### Middleware, Guards, Interceptors & Pipes
- Common issues: Execution order, context access, async operations
- Root causes: Incorrect implementation, missing async/await, improper error handling
- Solution priority: 1) Fix execution order, 2) Handle async properly, 3) Implement error handling
- Execution order: Middleware → Guards → Interceptors (before) → Pipes → Route handler → Interceptors (after)
- Resources: [Middleware](https://docs.nestjs.com/middleware), [Guards](https://docs.nestjs.com/guards)

### Testing Strategies (Jest & Supertest)
- Common issues: Mocking dependencies, testing modules, e2e test setup
- Root causes: Improper test module creation, missing mock providers, incorrect async handling
- Solution priority: 1) Fix test module setup, 2) Mock dependencies correctly, 3) Handle async tests
- Tools: `@nestjs/testing`, Jest, Supertest
- Resources: [Testing](https://docs.nestjs.com/fundamentals/testing)

### Database Integration (TypeORM & Mongoose)
- Common issues: Connection management, entity relationships, migrations
- Root causes: Incorrect configuration, missing decorators, improper transaction handling
- Solution priority: 1) Fix configuration, 2) Correct entity setup, 3) Implement transactions
- TypeORM: `@nestjs/typeorm`, entity decorators, repository pattern
- Mongoose: `@nestjs/mongoose`, schema decorators, model injection
- Resources: [TypeORM](https://docs.nestjs.com/techniques/database), [Mongoose](https://docs.nestjs.com/techniques/mongodb)

### Authentication & Authorization (Passport.js)
- Common issues: Strategy configuration, JWT handling, guard implementation
- Root causes: Missing strategy setup, incorrect token validation, improper guard usage
- Solution priority: 1) Configure Passport strategy, 2) Implement guards, 3) Handle JWT properly
- Tools: `@nestjs/passport`, `@nestjs/jwt`, passport strategies
- Resources: [Authentication](https://docs.nestjs.com/security/authentication), [Authorization](https://docs.nestjs.com/security/authorization)

### Configuration & Environment Management
- Common issues: Environment variables, configuration validation, async configuration
- Root causes: Missing config module, improper validation, incorrect async loading
- Solution priority: 1) Setup ConfigModule, 2) Add validation, 3) Handle async config
- Tools: `@nestjs/config`, Joi validation
- Resources: [Configuration](https://docs.nestjs.com/techniques/configuration)

### Error Handling & Logging
- Common issues: Exception filters, logging configuration, error propagation
- Root causes: Missing exception filters, improper logger setup, unhandled promises
- Solution priority: 1) Implement exception filters, 2) Configure logger, 3) Handle all errors
- Tools: Built-in Logger, custom exception filters
- Resources: [Exception Filters](https://docs.nestjs.com/exception-filters), [Logger](https://docs.nestjs.com/techniques/logger)

## Environmental Adaptation

### Detection Phase
I analyze the project to understand:
- Nest.js version and configuration
- Module structure and organization
- Database setup (TypeORM/Mongoose/Prisma)
- Testing framework configuration
- Authentication implementation

Detection commands:
```bash
# Check Nest.js setup
test -f nest-cli.json && echo "Nest.js CLI project detected"
grep -q "@nestjs/core" package.json && echo "Nest.js framework installed"
test -f tsconfig.json && echo "TypeScript configuration found"

# Detect Nest.js version
grep "@nestjs/core" package.json | sed 's/.*"\([0-9\.]*\)".*/Nest.js version: \1/'

# Check database setup
grep -q "@nestjs/typeorm" package.json && echo "TypeORM integration detected"
grep -q "@nestjs/mongoose" package.json && echo "Mongoose integration detected"
grep -q "@prisma/client" package.json && echo "Prisma ORM detected"

# Check authentication
grep -q "@nestjs/passport" package.json && echo "Passport authentication detected"
grep -q "@nestjs/jwt" package.json && echo "JWT authentication detected"

# Analyze module structure
find src -name "*.module.ts" -type f | head -5 | xargs -I {} basename {} .module.ts
```

**Safety note**: Avoid watch/serve processes; use one-shot diagnostics only.

### Adaptation Strategies
- Match existing module patterns and naming conventions
- Follow established testing patterns
- Respect database strategy (repository pattern vs active record)
- Use existing authentication guards and strategies

## Tool Integration

### Diagnostic Tools
```bash
# Analyze module dependencies
nest info

# Check for circular dependencies
npm run build -- --watch=false

# Validate module structure
npm run lint
```

### Fix Validation
```bash
# Verify fixes (validation order)
npm run build          # 1. Typecheck first
npm run test           # 2. Run unit tests
npm run test:e2e       # 3. Run e2e tests if needed
```

**Validation order**: typecheck → unit tests → integration tests → e2e tests

## Problem-Specific Approaches (Real Issues from GitHub & Stack Overflow)

### 1. "Nest can't resolve dependencies of the [Service] (?)"
**Frequency**: HIGHEST (500+ GitHub issues) | **Complexity**: LOW-MEDIUM
**Real Examples**: GitHub #3186, #886, #2359 | SO 75483101
When encountering this error:
1. Check if provider is in module's providers array
2. Verify module exports if crossing boundaries  
3. Check for typos in provider names (GitHub #598 - misleading error)
4. Review import order in barrel exports (GitHub #9095)

### 2. "Circular dependency detected"
**Frequency**: HIGH | **Complexity**: HIGH
**Real Examples**: SO 65671318 (32 votes) | Multiple GitHub discussions
Community-proven solutions:
1. Use forwardRef() on BOTH sides of the dependency
2. Extract shared logic to a third module (recommended)
3. Consider if circular dependency indicates design flaw
4. Note: Community warns forwardRef() can mask deeper issues

### 3. "Cannot test e2e because Nestjs doesn't resolve dependencies"
**Frequency**: HIGH | **Complexity**: MEDIUM
**Real Examples**: SO 75483101, 62942112, 62822943
Proven testing solutions:
1. Use @golevelup/ts-jest for createMock() helper
2. Mock JwtService in test module providers
3. Import all required modules in Test.createTestingModule()
4. For Bazel users: Special configuration needed (SO 62942112)

### 4. "[TypeOrmModule] Unable to connect to the database"
**Frequency**: MEDIUM | **Complexity**: HIGH  
**Real Examples**: GitHub typeorm#1151, #520, #2692
Key insight - this error is often misleading:
1. Check entity configuration - @Column() not @Column('description')
2. For multiple DBs: Use named connections (GitHub #2692)
3. Implement connection error handling to prevent app crash (#520)
4. SQLite: Verify database file path (typeorm#8745)

### 5. "Unknown authentication strategy 'jwt'"
**Frequency**: HIGH | **Complexity**: LOW
**Real Examples**: SO 79201800, 74763077, 62799708
Common JWT authentication fixes:
1. Import Strategy from 'passport-jwt' NOT 'passport-local'
2. Ensure JwtModule.secret matches JwtStrategy.secretOrKey
3. Check Bearer token format in Authorization header
4. Set JWT_SECRET environment variable

### 6. "ActorModule exporting itself instead of ActorService"
**Frequency**: MEDIUM | **Complexity**: LOW
**Real Example**: GitHub #866
Module export configuration fix:
1. Export the SERVICE not the MODULE from exports array
2. Common mistake: exports: [ActorModule] → exports: [ActorService]
3. Check all module exports for this pattern
4. Validate with nest info command

### 7. "secretOrPrivateKey must have a value" (JWT)
**Frequency**: HIGH | **Complexity**: LOW
**Real Examples**: Multiple community reports
JWT configuration fixes:
1. Set JWT_SECRET in environment variables
2. Check ConfigModule loads before JwtModule
3. Verify .env file is in correct location
4. Use ConfigService for dynamic configuration

### 8. Version-Specific Regressions
**Frequency**: LOW | **Complexity**: MEDIUM
**Real Example**: GitHub #2359 (v6.3.1 regression)
Handling version-specific bugs:
1. Check GitHub issues for your specific version
2. Try downgrading to previous stable version
3. Update to latest patch version
4. Report regressions with minimal reproduction

### 9. "Nest can't resolve dependencies of the UserController (?, +)"
**Frequency**: HIGH | **Complexity**: LOW
**Real Example**: GitHub #886
Controller dependency resolution:
1. The "?" indicates missing provider at that position
2. Count constructor parameters to identify which is missing
3. Add missing service to module providers
4. Check service is properly decorated with @Injectable()

### 10. "Nest can't resolve dependencies of the Repository" (Testing)
**Frequency**: MEDIUM | **Complexity**: MEDIUM
**Real Examples**: Community reports
TypeORM repository testing:
1. Use getRepositoryToken(Entity) for provider token
2. Mock DataSource in test module
3. Provide test database connection
4. Consider mocking repository completely

### 11. "Unauthorized 401 (Missing credentials)" with Passport JWT
**Frequency**: HIGH | **Complexity**: LOW
**Real Example**: SO 74763077
JWT authentication debugging:
1. Verify Authorization header format: "Bearer [token]"
2. Check token expiration (use longer exp for testing)
3. Test without nginx/proxy to isolate issue
4. Use jwt.io to decode and verify token structure

### 12. Memory Leaks in Production
**Frequency**: LOW | **Complexity**: HIGH
**Real Examples**: Community reports
Memory leak detection and fixes:
1. Profile with node --inspect and Chrome DevTools
2. Remove event listeners in onModuleDestroy()
3. Close database connections properly
4. Monitor heap snapshots over time

### 13. "More informative error message when dependencies are improperly setup"
**Frequency**: N/A | **Complexity**: N/A
**Real Example**: GitHub #223 (Feature Request)
Debugging dependency injection:
1. NestJS errors are intentionally generic for security
2. Use verbose logging during development
3. Add custom error messages in your providers
4. Consider using dependency injection debugging tools

### 14. Multiple Database Connections
**Frequency**: MEDIUM | **Complexity**: MEDIUM
**Real Example**: GitHub #2692
Configuring multiple databases:
1. Use named connections in TypeOrmModule
2. Specify connection name in @InjectRepository()
3. Configure separate connection options
4. Test each connection independently

### 15. "Connection with sqlite database is not established"
**Frequency**: LOW | **Complexity**: LOW
**Real Example**: typeorm#8745
SQLite-specific issues:
1. Check database file path is absolute
2. Ensure directory exists before connection
3. Verify file permissions
4. Use synchronize: true for development

### 16. Misleading "Unable to connect" Errors
**Frequency**: MEDIUM | **Complexity**: HIGH
**Real Example**: typeorm#1151
True causes of connection errors:
1. Entity syntax errors show as connection errors
2. Wrong decorator usage: @Column() not @Column('description')
3. Missing decorators on entity properties
4. Always check entity files when connection errors occur

### 17. "Typeorm connection error breaks entire nestjs application"
**Frequency**: MEDIUM | **Complexity**: MEDIUM
**Real Example**: typeorm#520
Preventing app crash on DB failure:
1. Wrap connection in try-catch in useFactory
2. Allow app to start without database
3. Implement health checks for DB status
4. Use retryAttempts and retryDelay options

## Common Patterns & Solutions

### Module Organization
```typescript
// Feature module pattern
@Module({
  imports: [CommonModule, DatabaseModule],
  controllers: [FeatureController],
  providers: [FeatureService, FeatureRepository],
  exports: [FeatureService] // Export for other modules
})
export class FeatureModule {}
```

### Custom Decorator Pattern
```typescript
// Combine multiple decorators
export const Auth = (...roles: Role[]) => 
  applyDecorators(
    UseGuards(JwtAuthGuard, RolesGuard),
    Roles(...roles),
  );
```

### Testing Pattern
```typescript
// Comprehensive test setup
beforeEach(async () => {
  const module = await Test.createTestingModule({
    providers: [
      ServiceUnderTest,
      {
        provide: DependencyService,
        useValue: mockDependency,
      },
    ],
  }).compile();
  
  service = module.get<ServiceUnderTest>(ServiceUnderTest);
});
```

### Exception Filter Pattern
```typescript
@Catch(HttpException)
export class HttpExceptionFilter implements ExceptionFilter {
  catch(exception: HttpException, host: ArgumentsHost) {
    // Custom error handling
  }
}
```

## Code Review Checklist

When reviewing Nest.js applications, focus on:

### Module Architecture & Dependency Injection
- [ ] All services are properly decorated with @Injectable()
- [ ] Providers are listed in module's providers array and exports when needed
- [ ] No circular dependencies between modules (check for forwardRef usage)
- [ ] Module boundaries follow domain/feature separation
- [ ] Custom providers use proper injection tokens (avoid string tokens)

### Testing & Mocking
- [ ] Test modules use minimal, focused provider mocks
- [ ] TypeORM repositories use getRepositoryToken(Entity) for mocking
- [ ] No actual database dependencies in unit tests
- [ ] All async operations are properly awaited in tests
- [ ] JwtService and external dependencies are mocked appropriately

### Database Integration (TypeORM Focus)
- [ ] Entity decorators use correct syntax (@Column() not @Column('description'))
- [ ] Connection errors don't crash the entire application
- [ ] Multiple database connections use named connections
- [ ] Database connections have proper error handling and retry logic
- [ ] Entities are properly registered in TypeOrmModule.forFeature()

### Authentication & Security (JWT + Passport)
- [ ] JWT Strategy imports from 'passport-jwt' not 'passport-local'
- [ ] JwtModule secret matches JwtStrategy secretOrKey exactly
- [ ] Authorization headers follow 'Bearer [token]' format
- [ ] Token expiration times are appropriate for use case
- [ ] JWT_SECRET environment variable is properly configured

### Request Lifecycle & Middleware
- [ ] Middleware execution order follows: Middleware → Guards → Interceptors → Pipes
- [ ] Guards properly protect routes and return boolean/throw exceptions
- [ ] Interceptors handle async operations correctly
- [ ] Exception filters catch and transform errors appropriately
- [ ] Pipes validate DTOs with class-validator decorators

### Performance & Optimization
- [ ] Caching is implemented for expensive operations
- [ ] Database queries avoid N+1 problems (use DataLoader pattern)
- [ ] Connection pooling is configured for database connections
- [ ] Memory leaks are prevented (clean up event listeners)
- [ ] Compression middleware is enabled for production

## Decision Trees for Architecture

### Choosing Database ORM
```
Project Requirements:
├─ Need migrations? → TypeORM or Prisma
├─ NoSQL database? → Mongoose
├─ Type safety priority? → Prisma
├─ Complex relations? → TypeORM
└─ Existing database? → TypeORM (better legacy support)
```

### Module Organization Strategy
```
Feature Complexity:
├─ Simple CRUD → Single module with controller + service
├─ Domain logic → Separate domain module + infrastructure
├─ Shared logic → Create shared module with exports
├─ Microservice → Separate app with message patterns
└─ External API → Create client module with HttpModule
```

### Testing Strategy Selection
```
Test Type Required:
├─ Business logic → Unit tests with mocks
├─ API contracts → Integration tests with test database
├─ User flows → E2E tests with Supertest
├─ Performance → Load tests with k6 or Artillery
└─ Security → OWASP ZAP or security middleware tests
```

### Authentication Method
```
Security Requirements:
├─ Stateless API → JWT with refresh tokens
├─ Session-based → Express sessions with Redis
├─ OAuth/Social → Passport with provider strategies
├─ Multi-tenant → JWT with tenant claims
└─ Microservices → Service-to-service auth with mTLS
```

### Caching Strategy
```
Data Characteristics:
├─ User-specific → Redis with user key prefix
├─ Global data → In-memory cache with TTL
├─ Database results → Query result cache
├─ Static assets → CDN with cache headers
└─ Computed values → Memoization decorators
```

## Performance Optimization

### Caching Strategies
- Use built-in cache manager for response caching
- Implement cache interceptors for expensive operations
- Configure TTL based on data volatility
- Use Redis for distributed caching

### Database Optimization
- Use DataLoader pattern for N+1 query problems
- Implement proper indexes on frequently queried fields
- Use query builder for complex queries vs. ORM methods
- Enable query logging in development for analysis

### Request Processing
- Implement compression middleware
- Use streaming for large responses
- Configure proper rate limiting
- Enable clustering for multi-core utilization

## External Resources

### Core Documentation
- [Nest.js Documentation](https://docs.nestjs.com)
- [Nest.js CLI](https://docs.nestjs.com/cli/overview)
- [Nest.js Recipes](https://docs.nestjs.com/recipes)

### Testing Resources
- [Jest Documentation](https://jestjs.io/docs/getting-started)
- [Supertest](https://github.com/visionmedia/supertest)
- [Testing Best Practices](https://github.com/goldbergyoni/javascript-testing-best-practices)

### Database Resources
- [TypeORM Documentation](https://typeorm.io)
- [Mongoose Documentation](https://mongoosejs.com)

### Authentication
- [Passport.js Strategies](http://www.passportjs.org)
- [JWT Best Practices](https://tools.ietf.org/html/rfc8725)

## Quick Reference Patterns

### Dependency Injection Tokens
```typescript
// Custom provider token
export const CONFIG_OPTIONS = Symbol('CONFIG_OPTIONS');

// Usage in module
@Module({
  providers: [
    {
      provide: CONFIG_OPTIONS,
      useValue: { apiUrl: 'https://api.example.com' }
    }
  ]
})
```

### Global Module Pattern
```typescript
@Global()
@Module({
  providers: [GlobalService],
  exports: [GlobalService],
})
export class GlobalModule {}
```

### Dynamic Module Pattern
```typescript
@Module({})
export class ConfigModule {
  static forRoot(options: ConfigOptions): DynamicModule {
    return {
      module: ConfigModule,
      providers: [
        {
          provide: 'CONFIG_OPTIONS',
          useValue: options,
        },
      ],
    };
  }
}
```

## Success Metrics
- ✅ Problem correctly identified and located in module structure
- ✅ Solution follows Nest.js architectural patterns
- ✅ All tests pass (unit, integration, e2e)
- ✅ No circular dependencies introduced
- ✅ Performance metrics maintained or improved
- ✅ Code follows established project conventions
- ✅ Proper error handling implemented
- ✅ Security best practices applied
- ✅ Documentation updated for API changes

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

## Imported Module: New Rails Project
---
name: new-rails-project
argument-hint: [project name]
description: Create a new Rails project
allowed-tools: Bash(rails *), Bash(bundle *), Bash(bin/*), Bash(npm *), Bash(yarn *)
context: fork
metadata:
  author: Shpigford
  version: "1.0"
---

Generate a new Rails project named $1 in the current directory. You may reference @AGENT_CONTEXT.md for general guidance, though the guidance here takes precedence.

# Tech Stack
Set up the following tech stack:
- **Rails ~8** with PostgreSQL - Server-side framework and database
- **Inertia.js ~2.3** - Bridges Rails and React for SPA-like experience without API
- **React ~19.2** - Frontend UI framework
- **Vite ~5** - JavaScript bundler with HMR
- **Tailwind CSS ~4** - Utility-first CSS framework
- **Sidekiq 8** - Background job processing with scheduled jobs via sidekiq-scheduler
- **Redis** - Sessions, caching, and job queue

# Rails guidance
- Do not use Kamal or Docker
- Do not use Rails "solid_*" components/systems
- Development should generally match production settings where possible
- Use Redis for caching

# Database
- All tables use UUID primary keys (pgcrypto extension)
- Timestamps use `timestamptz` for timezone awareness
- JSONB columns for flexible metadata storage
- Comprehensive indexing strategy for performance
- Encrypted fields for sensitive data (OAuth tokens, API keys)

# Background jobs
- Use Sidekiq 8 with Redis

# Testing
- Always use minitest
- Use `mocha` gem and VCR for external services (only in the providers layer)
- Prefer `OpenStruct` for mock instances
- Only mock what's necessary

# Code maintenace
- Run `bundle exec rubocop -a` after significant code changes
- Use `.rubocop.yml` for style configuration
- Security scanning with `bundle exec brakeman`

# Frontend
- All React components and views should be TSX

# General guidance
- Ask lots of clarifying questions when planning. The more the better. Make extensive use of AskUserQuestionTool to gather requirements and specifications. You can't ask too many questions.

# Verify
Verify the boilerplate is working by running `bin/rails server` and accessing the application at `http://localhost:3000` via playwright MCP.

## Imported Module: Odoo Accounting Setup
---
name: odoo-accounting-setup
description: "Expert guide for configuring Odoo Accounting: chart of accounts, journals, fiscal positions, taxes, payment terms, and bank reconciliation."
risk: safe
source: "self"
---

# Odoo Accounting Setup

## Overview

This skill guides functional consultants and business owners through setting up Odoo Accounting correctly from scratch. It covers chart of accounts configuration, journal setup, tax rules, fiscal positions, payment terms, and the bank statement reconciliation workflow.

## When to Use This Skill

- Setting up a new Odoo instance for a company for the first time.
- Configuring multi-currency or multi-company accounting.
- Troubleshooting tax calculation or fiscal position mapping errors.
- Creating payment terms for installment billing (e.g., Net 30, 50% upfront).

## How It Works

1. **Activate**: Mention `@odoo-accounting-setup` and describe your accounting scenario.
2. **Configure**: Receive step-by-step Odoo menu navigation with exact field values.
3. **Validate**: Get a checklist to verify your setup is complete and correct.

## Examples

### Example 1: Create a Payment Term (Net 30 with 2% Early Pay Discount)

```text
Menu: Accounting → Configuration → Payment Terms → New

Name: Net 30 / 2% Early Pay Discount
Company: [Your Company]

Lines:
  Line 1:
    - Due Type: Percent
    - Value: 100%
    - Due: 30 days (full balance due in 30 days)

Early Payment Discount (Odoo 16+):
  Discount %: 2
  Discount Days: 10
  Balance Sheet Accounts:
    - Gain: 4900 Early Payment Discounts Granted
    - Loss: 5900 Early Payment Discounts Received
```

> **Note (v16+):** Use the built-in **Early Payment Discount** field instead of the old split-line workaround. Odoo now posts the discount automatically when the customer pays within the discount window and generates correct accounting entries.

### Example 2: Fiscal Position for EU VAT (B2B Intra-Community)

```text
Menu: Accounting → Configuration → Fiscal Positions → New

Name: EU Intra-Community B2B
Auto-detection: ON
  - Country Group: Europe
  - VAT Required: YES (customer must have EU VAT number)

Tax Mapping:
  Tax on Sales (21% VAT) → 0% Intra-Community VAT
  Tax on Purchases      → 0% Reverse Charge

Account Mapping:
  (Leave empty unless your localization requires account remapping)
```

### Example 3: Reconciliation Model for Bank Fees

```text
Menu: Accounting → Configuration → Reconciliation Models → New

Name: Bank Fee Auto-Match
Type: Write-off
Matching Order: 1

Conditions:
  - Label Contains: "BANK FEE" OR "SERVICE CHARGE"
  - Amount Type: Amount is lower than: $50.00

Action:
  - Account: 6200 Bank Charges
  - Tax: None
  - Analytic: Administrative
```

## Best Practices

- ✅ **Do:** Install your country's **localization module** first (`l10n_us`, `l10n_mx`, etc.) before manually creating accounts — it sets up the correct chart of accounts.
- ✅ **Do:** Use **Fiscal Positions** to automate B2B vs B2C tax switching — never change taxes manually on individual invoices.
- ✅ **Do:** Lock accounting periods (Accounting → Actions → Lock Dates) after month-end closing to prevent retroactive edits.
- ✅ **Do:** Use the **Early Payment Discount** feature (v16+) instead of splitting payment term lines for discount modelling.
- ❌ **Don't:** Delete journal entries — always reverse them with a credit note or the built-in reversal function.
- ❌ **Don't:** Mix personal and business transactions in the same journal.
- ❌ **Don't:** Create manual journal entries to fix bank reconciliation mismatches — use the reconciliation model workflow instead.

## Limitations

- Does not cover **multi-currency revaluation** or foreign exchange gain/loss accounting in depth.
- **Country-specific e-invoicing** (CFDI, FatturaPA, SAF-T) requires additional localization modules — use `@odoo-l10n-compliance` for those.
- Payroll accounting integration (salary journals, deduction accounts) is not covered here — use `@odoo-hr-payroll-setup`.
- Odoo Community Edition does not include the full **lock dates** feature; some controls are Enterprise-only.

## Imported Module: Odoo Automated Tests
---
name: odoo-automated-tests
description: "Write and run Odoo automated tests using TransactionCase, HttpCase, and browser tour tests. Covers test data setup, mocking, and CI integration."
risk: safe
source: "self"
---

# Odoo Automated Tests

## Overview

Odoo has a built-in testing framework based on Python's `unittest`. This skill helps you write `TransactionCase` unit tests, `HttpCase` integration tests, and JavaScript tour tests. It also covers running tests in CI pipelines.

## When to Use This Skill

- Writing unit tests for a custom model's business logic.
- Creating an HTTP test to verify a controller endpoint.
- Debugging test failures in a CI pipeline.
- Setting up automated test execution with `--test-enable`.

## How It Works

1. **Activate**: Mention `@odoo-automated-tests` and describe the feature to test.
2. **Generate**: Get complete test class code with setup, teardown, and assertions.
3. **Run**: Get the exact `odoo` CLI command to execute your tests.

## Examples

### Example 1: TransactionCase Unit Test (Odoo 15+ pattern)

```python
# tests/test_hospital_patient.py
from odoo.tests.common import TransactionCase
from odoo.tests import tagged
from odoo.exceptions import ValidationError

@tagged('post_install', '-at_install')
class TestHospitalPatient(TransactionCase):

    @classmethod
    def setUpClass(cls):
        # Use setUpClass for performance — runs once per class, not per test
        super().setUpClass()
        cls.Patient = cls.env['hospital.patient']
        cls.doctor = cls.env['res.users'].browse(cls.env.uid)

    def test_create_patient(self):
        patient = self.Patient.create({
            'name': 'John Doe',
            'doctor_id': self.doctor.id,
        })
        self.assertEqual(patient.state, 'draft')
        self.assertEqual(patient.name, 'John Doe')

    def test_confirm_patient(self):
        patient = self.Patient.create({'name': 'Jane Smith'})
        patient.action_confirm()
        self.assertEqual(patient.state, 'confirmed')

    def test_empty_name_raises_error(self):
        with self.assertRaises(ValidationError):
            self.Patient.create({'name': ''})

    def test_access_denied_for_other_user(self):
        # Test security rules by running as a different user
        other_user = self.env.ref('base.user_demo')
        with self.assertRaises(Exception):
            self.Patient.with_user(other_user).create({'name': 'Test'})
```

> **`setUpClass` vs `setUp`:** Use `setUpClass` (Odoo 15+) for shared test data. It runs once per class and is significantly faster than `setUp` which re-initializes for every single test method.

### Example 2: Run Tests via CLI

```bash
# Run all tests for a specific module
./odoo-bin --test-enable --stop-after-init -d my_database -u hospital_management

# Run only tests tagged with a specific tag
./odoo-bin --test-enable --stop-after-init -d my_database \
  --test-tags hospital_management

# Run a specific test class
./odoo-bin --test-enable --stop-after-init -d my_database \
  --test-tags /hospital_management:TestHospitalPatient
```

### Example 3: HttpCase for Controller Testing

```python
from odoo.tests.common import HttpCase
from odoo.tests import tagged

@tagged('post_install', '-at_install')
class TestPatientController(HttpCase):

    def test_patient_page_authenticated(self):
        # Authenticate as a user, not with hardcoded password
        self.authenticate(self.env.user.login, self.env.user.login)
        resp = self.url_open('/hospital/patients')
        self.assertEqual(resp.status_code, 200)

    def test_patient_page_redirects_unauthenticated(self):
        # No authenticate() call = public/anonymous user
        resp = self.url_open('/hospital/patients', allow_redirects=False)
        self.assertIn(resp.status_code, [301, 302, 403])
```

## Best Practices

- ✅ **Do:** Use `setUpClass()` with `cls.env` instead of `setUp()` — it is dramatically faster for large test suites.
- ✅ **Do:** Use `@tagged('post_install', '-at_install')` to run tests after all modules are installed.
- ✅ **Do:** Test both the happy path and error conditions (`ValidationError`, `AccessError`, `UserError`).
- ✅ **Do:** Use `self.with_user(user)` to test access control without calling `sudo()`.
- ❌ **Don't:** Use a production database for tests — always use a dedicated test database.
- ❌ **Don't:** Rely on test execution order — each `TransactionCase` test is rolled back in isolation.
- ❌ **Don't:** Hardcode passwords in `HttpCase.authenticate()` — use `self.env.user.login` or a fixture user.

## Limitations

- **JavaScript tour tests** require a running browser (via `phantomjs` or `Chrome headless`) and a live Odoo server — not covered in depth here.
- `HttpCase` tests are significantly slower than `TransactionCase` — use them only for controller/route verification.
- Does not cover **mocking external services** (e.g., mocking an SMTP server or payment gateway in tests).
- Test isolation is at the **transaction level**, not database level — tests that commit data (e.g., via `cr.commit()`) can leak state between tests.

## Imported Module: Odoo Backup Strategy
---
name: odoo-backup-strategy
description: "Complete Odoo backup and restore strategy: database dumps, filestore backup, automated scheduling, cloud storage upload, and tested restore procedures."
risk: safe
source: "self"
---

# Odoo Backup Strategy

## Overview

A complete Odoo backup must include both the **PostgreSQL database** and the **filestore** (attachments, images). This skill covers manual and automated backup procedures, offsite storage, and the correct restore sequence to bring a down Odoo instance back online.

## When to Use This Skill

- Setting up a backup strategy for a production Odoo instance.
- Automating daily backups with shell scripts and cron.
- Restoring Odoo after a server failure or data corruption event.
- Diagnosing a failed backup or corrupt restore.

## How It Works

1. **Activate**: Mention `@odoo-backup-strategy` and describe your server environment.
2. **Generate**: Receive a complete backup script tailored to your setup.
3. **Restore**: Get step-by-step restore instructions for any failure scenario.

## Examples

### Example 1: Manual Database + Filestore Backup

```bash
#!/bin/bash
# backup_odoo.sh

DATE=$(date +%Y%m%d_%H%M%S)
DB_NAME="odoo"
DB_USER="odoo"
FILESTORE_PATH="/var/lib/odoo/.local/share/Odoo/filestore/$DB_NAME"
BACKUP_DIR="/backups/odoo"

mkdir -p "$BACKUP_DIR"

# Step 1: Dump the database
pg_dump -U $DB_USER -Fc $DB_NAME > "$BACKUP_DIR/db_$DATE.dump"

# Step 2: Archive the filestore
tar -czf "$BACKUP_DIR/filestore_$DATE.tar.gz" -C "$FILESTORE_PATH" .

echo "✅ Backup complete: db_$DATE.dump + filestore_$DATE.tar.gz"
```

### Example 2: Automate with Cron (daily at 2 AM)

```bash
# Run: crontab -e
# Add this line:
0 2 * * * /opt/scripts/backup_odoo.sh >> /var/log/odoo_backup.log 2>&1
```

### Example 3: Upload to S3 (after backup)

```bash
# Add to backup script after tar command:
aws s3 cp "$BACKUP_DIR/db_$DATE.dump"        s3://my-odoo-backups/db/
aws s3 cp "$BACKUP_DIR/filestore_$DATE.tar.gz" s3://my-odoo-backups/filestore/

# Optional: Delete local backups older than 7 days
find "$BACKUP_DIR" -type f -mtime +7 -delete
```

### Example 4: Full Restore Procedure

```bash
# Step 1: Stop Odoo
docker compose stop odoo  # or: systemctl stop odoo

# Step 2: Recreate and restore the database
# (--clean alone fails if the DB doesn't exist; drop and recreate first)
dropdb -U odoo odoo 2>/dev/null || true
createdb -U odoo odoo
pg_restore -U odoo -d odoo db_YYYYMMDD_HHMMSS.dump

# Step 3: Restore the filestore
FILESTORE=/var/lib/odoo/.local/share/Odoo/filestore/odoo
rm -rf "$FILESTORE"/*
tar -xzf filestore_YYYYMMDD_HHMMSS.tar.gz -C "$FILESTORE"/

# Step 4: Restart Odoo
docker compose start odoo

# Step 5: Verify — open Odoo in the browser and check:
#   - Can you log in?
#   - Are recent records visible?
#   - Are file attachments loading?
```

## Best Practices

- ✅ **Do:** Test restores monthly in a staging environment — a backup you've never restored is not a backup.
- ✅ **Do:** Follow the **3-2-1 rule**: 3 copies, 2 different media types, 1 offsite copy (e.g., S3 or a remote server).
- ✅ **Do:** Back up **immediately before every Odoo upgrade** — this is your rollback point.
- ✅ **Do:** Verify backup integrity: `pg_restore --list backup.dump` should complete without errors.
- ❌ **Don't:** Back up only the database without the filestore — all attachments and images will be missing after a restore.
- ❌ **Don't:** Store backups on the same disk or same server as Odoo — a disk or server failure destroys both.
- ❌ **Don't:** Run `pg_restore --clean` against a non-existent database — always create the database first.

## Limitations

- Does not cover **Odoo.sh built-in backups** — Odoo.sh has its own backup system accessible from the dashboard.
- This script assumes a **single-database** Odoo setup. Multi-database instances require looping over all databases.
- Filestore path may differ between installations (Docker volume vs. bare-metal). Always verify the path with `odoo-bin shell` before running a restore.
- Large filestores (100GB+) may require incremental backup tools like `rsync` or `restic` rather than full `tar.gz` archives.

## Imported Module: Odoo Ecommerce Configurator
---
name: odoo-ecommerce-configurator
description: "Expert guide for Odoo eCommerce and Website: product catalog, payment providers, shipping methods, SEO, and order-to-fulfillment workflow."
risk: safe
source: "self"
---

# Odoo eCommerce Configurator

## Overview

This skill helps you set up and optimize an Odoo-powered online store. It covers product publishing, payment gateway integration, shipping carrier configuration, cart and checkout customization, and the workflow from online order to warehouse fulfillment.

## When to Use This Skill

- Launching an Odoo eCommerce store for the first time.
- Integrating a payment provider (Stripe, PayPal, Adyen).
- Configuring shipping rates with carrier integration (UPS, FedEx, DHL).
- Optimizing product pages for SEO with Odoo Website tools.

## How It Works

1. **Activate**: Mention `@odoo-ecommerce-configurator` and describe your store scenario.
2. **Configure**: Receive step-by-step Odoo eCommerce setup with menu paths.
3. **Optimize**: Get SEO, conversion, and catalog best practices.

## Examples

### Example 1: Publish a Product to the Website

```text
Menu: Website → eCommerce → Products → Select Product

Fields to complete for a great product listing:
  Name:               Ergonomic Mesh Office Chair  (keyword-rich)
  Internal Reference: CHAIR-MESH-001               (required for inventory)
  Sales Price:        $299.00
  Website Description (website tab): 150–300 words of unique content

Publishing:
  Toggle "Published" in the top-right corner of the product form
  or via: Website → Go to Website → Toggle "Published" button

SEO (website tab → SEO section):
  Page Title:       Ergonomic Mesh Chair | Office Chairs | YourStore
  Meta Description: Discover the most comfortable ergonomic mesh office
                    chair, designed for all-day support...  (≤160 chars)

Website tab:
  Can be Sold: YES
  Website:     yourstore.com  (if running multiple websites)
```

### Example 2: Configure Stripe Payment Provider

```text
Menu: Website → Configuration → Payment Providers → Stripe → Configure
(or: Accounting → Configuration → Payment Providers → Stripe)

State: Test  (use Test mode until fully validated, then switch to Enabled)

Credentials (from your Stripe Dashboard → Developers → API Keys):
  Publishable Key: pk_live_XXXXXXXX
  Secret Key:      sk_live_XXXXXXXX  (store securely; never expose client-side)

Payment Journal: Bank (USD)
Capture Mode:    Automatic  (charge card immediately on order confirmation)
                 or Manual  (authorize only; charge later on fulfillment)

Webhook:
  Add Odoo's webhook URL in Stripe Dashboard → Webhooks
  URL: https://yourstore.com/payment/stripe/webhook
  Events: payment_intent.succeeded, payment_intent.payment_failed
```

### Example 3: Set Up Flat Rate Shipping with Free Threshold

```text
Menu: Inventory → Configuration → Delivery Methods → New

Name: Standard Shipping (3–5 business days)
Provider: Fixed Price
Delivery Product: [Shipping] Standard  (used for invoicing)

Pricing:
  Price: $9.99
  ☑ Free if order amount is above: $75.00

Availability:
  Countries: United States
  States: All states

Publish to website:
  ☑ Published  (visible to customers at checkout)
```

### Example 4: Set Up Abandoned Cart Recovery

```text
Menu: Email Marketing → Mailing Lists → (create a list if needed)

For automated abandoned cart emails in Odoo 16/17:
Menu: Marketing → Marketing Automation → New Campaign

Trigger: Odoo record updated
Model: eCommerce Cart (sale.order with state = 'draft')
Filter: Cart not updated in 1 hour AND not confirmed

Actions:
  1. Wait 1 hour
  2. Send Email: "You left something behind!"  (use a recovery email template)
  3. Wait 24 hours
  4. Send Email: "Last chance — items selling fast"

Note: Some Odoo hosting plans may require "Email Marketing" app enabled.
```

## Best Practices

- ✅ **Do:** Use **Product Variants** (color, size) instead of duplicate products — cleaner catalog and shared inventory tracking.
- ✅ **Do:** Enable **HTTPS** (SSL certificate) via your hosting provider and set HSTS in Website → Settings → Security.
- ✅ **Do:** Set up **Abandoned Cart Recovery** using Marketing Automation or a scheduled email sequence.
- ✅ **Do:** Add a **Stripe webhook** so Odoo is notified of payment events in real time — without it, failed payments may not update correctly.
- ❌ **Don't:** Leave the payment provider in **Test mode** in production — no real charges will be processed.
- ❌ **Don't:** Publish products without an **Internal Reference (SKU)** — it breaks inventory tracking and order fulfillment.
- ❌ **Don't:** Use the same Stripe key for Test and Production environments — always rotate to live keys before going live.

## Limitations

- **Carrier integration** (live UPS/FedEx rate calculation) requires the specific carrier connector module (e.g., `delivery_ups`) and a carrier account API key.
- Does not cover **multi-website** configuration — running separate storefronts with different pricelists and languages requires Enterprise.
- **B2B eCommerce** (customer login required, custom catalog and prices per customer) has additional configuration steps not fully covered here.
- Odoo eCommerce does not support **subscription billing** natively — that requires the Enterprise **Subscriptions** module.

## Imported Module: Odoo Edi Connector
---
name: odoo-edi-connector
description: "Guide for implementing EDI (Electronic Data Interchange) with Odoo: X12, EDIFACT document mapping, partner onboarding, and automated order processing."
---

# Odoo EDI Connector

## Overview

Electronic Data Interchange (EDI) is the standard for automated B2B document exchange — purchase orders, invoices, ASNs (Advance Shipping Notices). This skill guides you through mapping EDI transactions (ANSI X12 or EDIFACT) to Odoo business objects, setting up trading partner configurations, and automating inbound/outbound document flows.

## When to Use This Skill

- A retail partner requires EDI 850 (Purchase Orders) to do business with you.
- You need to send EDI 856 (ASN) when goods are shipped.
- Automating EDI 810 (Invoice) generation from Odoo confirmed deliveries.
- Mapping EDI fields to Odoo fields for a new trading partner.

## How It Works

1. **Activate**: Mention `@odoo-edi-connector` and specify the EDI transaction set and trading partner.
2. **Map**: Receive a complete field mapping table between EDI segments and Odoo fields.
3. **Automate**: Get Python code to parse incoming EDI files and create Odoo records.

## EDI ↔ Odoo Object Mapping

| EDI Transaction | Odoo Object |
|---|---|
| 850 Purchase Order | `sale.order` (inbound customer PO) |
| 855 PO Acknowledgment | Confirmation email / SO confirmation |
| 856 ASN (Advance Ship Notice) | `stock.picking` (delivery order) |
| 810 Invoice | `account.move` (customer invoice) |
| 846 Inventory Inquiry | `product.product` stock levels |
| 997 Functional Acknowledgment | Automated receipt confirmation |

## Examples

### Example 1: Parse EDI 850 and Create Odoo Sale Order (Python)

```python
from pyx12 import x12file  # pip install pyx12

import xmlrpc.client

odoo_url = "https://myodoo.example.com"
db, uid, pwd = "my_db", 2, "api_key"
models = xmlrpc.client.ServerProxy(f"{odoo_url}/xmlrpc/2/object")

def process_850(edi_file_path):
    """Parse X12 850 Purchase Order and create Odoo Sale Order"""
    with x12file.X12File(edi_file_path) as f:
        for transaction in f.get_transaction_sets():
            # Extract header info (BEG segment)
            po_number = transaction['BEG'][3]    # Purchase Order Number
            po_date   = transaction['BEG'][5]    # Purchase Order Date

            # Extract partner (N1 segment — Buyer)
            partner_name = transaction['N1'][2]

            # Find partner in Odoo
            partner = models.execute_kw(db, uid, pwd, 'res.partner', 'search',
                [[['name', 'ilike', partner_name]]])
            partner_id = partner[0] if partner else False

            # Extract line items (PO1 segments)
            order_lines = []
            for po1 in transaction.get_segments('PO1'):
                sku     = po1[7]    # Product ID
                qty     = float(po1[2])
                price   = float(po1[4])

                product = models.execute_kw(db, uid, pwd, 'product.product', 'search',
                    [[['default_code', '=', sku]]])
                if product:
                    order_lines.append((0, 0, {
                        'product_id': product[0],
                        'product_uom_qty': qty,
                        'price_unit': price,
                    }))

            # Create Sale Order
            if partner_id and order_lines:
                models.execute_kw(db, uid, pwd, 'sale.order', 'create', [{
                    'partner_id': partner_id,
                    'client_order_ref': po_number,
                    'order_line': order_lines,
                }])
```

### Example 2: Send EDI 997 Acknowledgment

```python
def generate_997(isa_control, gs_control, transaction_control):
    """Generate a functional acknowledgment for received EDI"""
    return f"""ISA*00*          *00*          *ZZ*YOURISAID      *ZZ*PARTNERISAID   *{today}*1200*^*00501*{isa_control}*0*P*>~
GS*FA*YOURGID*PARTNERGID*{today}*1200*{gs_control}*X*005010X231A1~
ST*997*0001~
AK1*PO*{gs_control}~
AK9*A*1*1*1~
SE*4*0001~
GE*1*{gs_control}~
IEA*1*{isa_control}~"""
```

## Best Practices

- ✅ **Do:** Store every raw EDI transaction in an audit log table before processing.
- ✅ **Do:** Always send a **997 Functional Acknowledgment** within 24 hours of receiving a transaction.
- ✅ **Do:** Negotiate a test cycle with trading partners before going live — use test ISA qualifier `T`.
- ❌ **Don't:** Process EDI files synchronously in web requests — queue them for async processing.
- ❌ **Don't:** Hardcode trading partner qualifiers — store them in a configuration table per partner.

## Imported Module: Odoo Module Developer
---
name: odoo-module-developer
description: "Expert guide for creating custom Odoo modules. Covers __manifest__.py, model inheritance, ORM patterns, and module structure best practices."
risk: safe
source: "self"
---

# Odoo Module Developer

## Overview

This skill transforms your AI into an expert Odoo custom module developer. It guides you through scaffolding new modules, defining models, setting up security, and following Odoo's coding conventions for both Community and Enterprise editions.

## When to Use This Skill

- Starting a new custom Odoo module from scratch.
- Extending an existing Odoo model (e.g., adding fields to `sale.order`).
- Troubleshooting module loading errors or manifest issues.
- Implementing `onchange`, `compute`, and `constraint` methods correctly.

## How It Works

1. **Activate**: Mention `@odoo-module-developer` and describe the module you want to build.
2. **Scaffold**: Get the full folder structure with `__manifest__.py`, `__init__.py`, models, views, and security files.
3. **Review**: Paste existing code and get a review against Odoo best practices.

## Examples

### Example 1: Scaffold a New Module

**Prompt:** `Create a module called "hospital_management" with a model for patients`

**Generated structure:**

```text
hospital_management/
├── __manifest__.py
├── __init__.py
├── models/
│   ├── __init__.py
│   └── hospital_patient.py
├── views/
│   └── hospital_patient_views.xml
├── security/
│   ├── ir.model.access.csv
│   └── security.xml
└── data/
```

**`__manifest__.py`:**

```python
{
    'name': 'Hospital Management',
    'version': '17.0.1.0.0',
    'category': 'Healthcare',
    'depends': ['base', 'mail'],
    'data': [
        'security/ir.model.access.csv',
        'views/hospital_patient_views.xml',
    ],
    'installable': True,
    'license': 'LGPL-3',
}
```

**`models/hospital_patient.py`:**

```python
from odoo import models, fields, api

class HospitalPatient(models.Model):
    _name = 'hospital.patient'
    _description = 'Hospital Patient'
    _inherit = ['mail.thread', 'mail.activity.mixin']

    name = fields.Char(string='Patient Name', required=True, tracking=True)
    birth_date = fields.Date(string='Birth Date')
    doctor_id = fields.Many2one('res.users', string='Assigned Doctor')
    state = fields.Selection([
        ('draft', 'New'),
        ('confirmed', 'Confirmed'),
        ('done', 'Done'),
    ], default='draft', tracking=True)
```

## Best Practices

- ✅ **Do:** Always prefix your model `_name` with a namespace (e.g., `hospital.patient`).
- ✅ **Do:** Use `_inherit = ['mail.thread']` to add chatter/logging automatically.
- ✅ **Do:** Specify `version` in manifest as `{odoo_version}.{major}.{minor}.{patch}`.
- ✅ **Do:** Set `'author'` and `'website'` in `__manifest__.py` so your module is identifiable in the Apps list.
- ❌ **Don't:** Modify core Odoo model files directly — always use `_inherit`.
- ❌ **Don't:** Forget to add new models to `ir.model.access.csv` or users will get access errors.
- ❌ **Don't:** Use spaces or uppercase in folder names — Odoo requires snake_case module names.

## Limitations

- Does not cover **OWL JavaScript components** or frontend widget development — use `@odoo-xml-views-builder` for view XML.
- **Odoo 13 and below** have a different module structure (no `__manifest__.py` auto-loading) — this skill targets v14+.
- Does not cover **multi-company** or **multi-website** configuration; those require additional model fields (`company_id`, `website_id`).
- Does not generate automated test files — use `@odoo-automated-tests` for that.

## Imported Module: Odoo Orm Expert
---
name: odoo-orm-expert
description: "Master Odoo ORM patterns: search, browse, create, write, domain filters, computed fields, and performance-safe query techniques."
risk: safe
source: "self"
---

# Odoo ORM Expert

## Overview

This skill teaches you Odoo's Object Relational Mapper (ORM) in depth. It covers reading/writing records, building domain filters, working with relational fields, and avoiding common performance pitfalls like N+1 queries.

## When to Use This Skill

- Writing `search()`, `browse()`, `create()`, `write()`, or `unlink()` calls.
- Building complex domain filters for views or server actions.
- Implementing computed, stored, and related fields.
- Debugging slow queries or optimizing bulk operations.

## How It Works

1. **Activate**: Mention `@odoo-orm-expert` and describe what data operation you need.
2. **Get Code**: Receive correct, idiomatic Odoo ORM code with explanations.
3. **Optimize**: Ask for performance review on existing ORM code.

## Examples

### Example 1: Search with Domain Filters

```python
# Find all confirmed sale orders for a specific customer, created this year
import datetime

start_of_year = datetime.date.today().replace(month=1, day=1).strftime('%Y-%m-%d')

orders = self.env['sale.order'].search([
    ('partner_id', '=', partner_id),
    ('state', '=', 'sale'),
    ('date_order', '>=', start_of_year),
], order='date_order desc', limit=50)

# Note: pass dates as 'YYYY-MM-DD' strings in domains,
# NOT as fields.Date objects — the ORM serializes them correctly.
```

### Example 2: Computed Field

```python
total_order_count = fields.Integer(
    string='Total Orders',
    compute='_compute_total_order_count',
    store=True
)

@api.depends('sale_order_ids')
def _compute_total_order_count(self):
    for record in self:
        record.total_order_count = len(record.sale_order_ids)
```

### Example 3: Safe Bulk Write (avoid N+1)

```python
# ✅ GOOD: One query for all records
partners = self.env['res.partner'].search([('country_id', '=', False)])
partners.write({'country_id': self.env.ref('base.us').id})

# ❌ BAD: Triggers a separate query per record
for partner in partners:
    partner.country_id = self.env.ref('base.us').id
```

## Best Practices

- ✅ **Do:** Use `mapped()`, `filtered()`, and `sorted()` on recordsets instead of Python loops.
- ✅ **Do:** Use `sudo()` sparingly and only when you understand the security implications.
- ✅ **Do:** Prefer `search_count()` over `len(search(...))` when you only need a count.
- ✅ **Do:** Use `with_context(...)` to pass context values cleanly rather than modifying `self.env.context` directly.
- ❌ **Don't:** Call `search()` inside a loop — this is the #1 Odoo performance killer.
- ❌ **Don't:** Use raw SQL unless absolutely necessary; use ORM for all standard operations.
- ❌ **Don't:** Pass Python `datetime`/`date` objects directly into domain tuples — always stringify them as `'YYYY-MM-DD'`.

## Limitations

- Does not cover **`cr.execute()` raw SQL** patterns in depth — use the Odoo performance tuner skill for SQL-level optimization.
- **Stored computed fields** can cause significant write overhead at scale; this skill does not cover partitioning strategies.
- Does not cover **transient models** (`models.TransientModel`) or wizard patterns.
- ORM behavior can differ slightly between Odoo SaaS and On-Premise due to config overrides.

## Imported Module: Odoo Project Timesheet
---
name: odoo-project-timesheet
description: "Expert guide for Odoo Project and Timesheets: task stages, billable time tracking, timesheet approval, budget alerts, and invoicing from timesheets."
risk: safe
source: "self"
---

# Odoo Project & Timesheet

## Overview

This skill helps you configure Odoo Project and Timesheets for service businesses, agencies, and consulting firms. It covers project setup with budgets, task stage management, employee timesheet logging, approval workflows, and converting approved timesheet hours to customer invoices.

## When to Use This Skill

- Setting up a new project with tasks, deadlines, and team assignments.
- Configuring billable vs. non-billable time tracking per project.
- Creating a timesheet approval workflow for managers.
- Invoicing customers based on logged hours (Time & Materials billing).

## How It Works

1. **Activate**: Mention `@odoo-project-timesheet` and describe your project or billing scenario.
2. **Configure**: Receive step-by-step setup instructions.
3. **Automate**: Get guidance on automatically generating invoices from approved timesheets.

## Examples

### Example 1: Create a Billable Project

```text
Menu: Project → New Project (or the "+" button in Project view)

Name:     Website Redesign — Acme Corp
Customer: Acme Corporation
Billable: YES  (toggle ON)

Settings tab:
  Billing Type: Based on Timesheets (Time & Materials)
  Service Product: Consulting Hours ($150/hr)
  ☑ Timesheets
  ☑ Task Dependencies
  ☑ Subtasks

Budget:
  Planned Hours: 120 hours
  Budget Alert: at 80% (96 hrs) → notify project manager
```

### Example 2: Log Time on a Task

```text
Method A — Directly inside the Task (recommended for accuracy):
  Open Task → Timesheets tab → Add a Line
  Employee:    John Doe
  Date:        Today
  Description: "Initial wireframes and site map" (required for clear invoices)
  Duration:    3:30  (3 hours 30 minutes)

Method B — Timesheets app (for end-of-day bulk entry):
  Menu: Timesheets → My Timesheets → New
  Project:  Website Redesign
  Task:     Wireframe Design
  Duration: 3:30
```

### Example 3: Enable Timesheet Approval Before Invoicing

```text
Menu: Timesheets → Configuration → Settings
  ☑ Timesheet Approval  (employees submit; managers approve)

Approval flow:
  1. Employee submits timesheet at week/month end
  2. Manager reviews: Timesheets → Managers → Timesheets to Approve
  3. Manager clicks "Approve" → entries are locked and billable
  4. Only approved entries flow into the invoice

If Approval is disabled, all logged hours are immediately billable.
```

### Example 4: Invoice from Timesheets

```text
Step 1: Verify approved hours
  Menu: Timesheets → Managers → All Timesheets
  Filter: Billable = YES, Timesheet Invoice State = "To Invoice"

Step 2: Generate Invoice
  Menu: Sales → Orders → To Invoice → Timesheets  (v15/v16)
  or:   Accounting → Customers → Invoiceable Time  (v17)
  Filter by Customer: Acme Corporation
  Select entries → Create Invoices

Step 3: Invoice pre-populates with:
  Product: Consulting Hours
  Quantity: Sum of approved hours
  Unit Price: $150.00
  Total: Calculated automatically
```

## Best Practices

- ✅ **Do:** Enable **Timesheet Approval** so only manager-approved hours appear on customer invoices.
- ✅ **Do:** Set a **budget alert** at 80% of planned hours so PMs can intervene before overruns.
- ✅ **Do:** Require **timesheet descriptions** — vague entries like "Work done" on invoices destroy client trust.
- ✅ **Do:** Use **Subtasks** to break work into granular pieces while keeping the parent task on the Kanban board.
- ❌ **Don't:** Mix billable and internal projects without tagging — it corrupts profitability and utilization reports.
- ❌ **Don't:** Log time on the Project itself (without a Task) — it cannot be reported at the task level.

## Limitations

- **Timesheet Approval** is an Enterprise-only feature in some Odoo versions — verify your plan includes it.
- Does not cover **Project Forecast** (resource capacity planning) — that requires the Enterprise Forecast app.
- **Time & Materials** invoicing works well for hourly billing but is not suited for **fixed-price projects** — use milestones or manual invoice lines for those.
- Timesheet entries logged outside an active project-task pair (e.g., on internal projects) are not assignable to customer invoices without custom configuration.

## Imported Module: Odoo Purchase Workflow
---
name: odoo-purchase-workflow
description: "Expert guide for Odoo Purchase: RFQ → PO → Receipt → Vendor Bill workflow, purchase agreements, vendor price lists, and 3-way matching."
risk: safe
source: "self"
---

# Odoo Purchase Workflow

## Overview

This skill guides you through the complete Odoo Purchase workflow — from sending a Request for Quotation (RFQ) to receiving goods and matching the vendor bill. It also covers purchase agreements, vendor price lists on products, automated reordering, and 3-way matching controls.

## When to Use This Skill

- Setting up the purchase flow for a new Odoo instance.
- Implementing purchase order approval workflows (2-level approval).
- Configuring vendor price lists with quantity-based discounts.
- Troubleshooting billing/receipt mismatches in 3-way matching.

## How It Works

1. **Activate**: Mention `@odoo-purchase-workflow` and describe your purchasing scenario.
2. **Configure**: Receive exact Odoo menu paths and field-by-field configuration.
3. **Troubleshoot**: Describe a billing or receiving issue and get a root cause diagnosis.

## Examples

### Example 1: Standard RFQ → PO → Receipt → Bill Flow

```text
Step 1: Create RFQ
  Menu: Purchase → Orders → Requests for Quotation → New
  Vendor: Acme Supplies
  Add product lines with quantity and unit price

Step 2: Send RFQ to Vendor
  Click "Send by Email" → Vendor receives PDF with RFQ details

Step 3: Confirm as Purchase Order
  Click "Confirm Order" → Status changes to "Purchase Order"

Step 4: Receive Goods
  Click "Receive Products" → Validate received quantities
  (partial receipts are supported; PO stays open for remaining qty)

Step 5: Match Vendor Bill (3-Way Match)
  Click "Create Bill" → Bill pre-filled from PO quantities
  Verify: PO qty = Received qty = Billed qty
  Post Bill → Register Payment
```

### Example 2: Enable 2-Level Purchase Approval

```text
Menu: Purchase → Configuration → Settings

Purchase Order Approval:
  ☑ Purchase Order Approval
  Minimum Order Amount: $5,000

Result:
  Orders ≤ $5,000  → Confirm directly to PO
  Orders > $5,000  → Status: "Waiting for Approval"
                     A purchase manager must click "Approve"
```

### Example 3: Vendor Price List (Quantity Breaks on a Product)

```text
Vendor price lists are configured per product, not as a global menu.

Menu: Inventory → Products → [Select Product] → Purchase Tab
  → Vendor Pricelist section → Add a line

Vendor: Acme Supplies
Currency: USD
Price:    $12.00
Min. Qty: 1

Add another line for quantity discount:
Min. Qty: 100 → Price: $10.50   (12.5% discount)
Min. Qty: 500 → Price:  $9.00   (25% discount)

Result: Odoo automatically selects the right price on a PO
based on the ordered quantity for this vendor.
```

## Best Practices

- ✅ **Do:** Enable **Purchase Order Approval** for orders above your company's approval threshold.
- ✅ **Do:** Use **Purchase Agreements (Blanket Orders)** for recurring vendors with pre-negotiated annual contracts.
- ✅ **Do:** Set a **vendor lead time** on products (Purchase tab) so Odoo can schedule arrival dates accurately.
- ✅ **Do:** Set the **Bill Control** policy to "Based on received quantities" (not ordered qty) for accurate 3-way matching.
- ❌ **Don't:** Confirm a PO before prices are agreed — use Draft/RFQ status to negotiate first.
- ❌ **Don't:** Post a vendor bill without linking it to a receipt — bypassing 3-way matching creates accounting discrepancies.
- ❌ **Don't:** Delete a PO that has received quantities — archive it instead to preserve the stock and accounting trail.

## Limitations

- Does not cover **subcontracting purchase flows** — those require the Manufacturing module and subcontracting BoM type.
- **EDI-based order exchange** (automated PO import/export) requires custom integration — use `@odoo-edi-connector` for that.
- Vendor pricelist currency conversion depends on the active **currency rate** in Odoo; rates must be kept current for accuracy.
- The **2-level approval** is a binary threshold; more complex approval matrices (department-based, multi-tier) require custom development or the Approvals app.

## Imported Module: Odoo Qweb Templates
---
name: odoo-qweb-templates
description: "Expert in Odoo QWeb templating for PDF reports, email templates, and website pages. Covers t-if, t-foreach, t-field, and report actions."
risk: safe
source: "self"
---

# Odoo QWeb Templates

## Overview

QWeb is Odoo's primary templating engine, used for PDF reports, website pages, and email templates. This skill generates correct, well-structured QWeb XML with proper directives, translation support, and report action bindings.

## When to Use This Skill

- Creating a custom PDF report (invoice, delivery slip, certificate).
- Building a QWeb email template triggered by workflow actions.
- Designing Odoo website pages with dynamic content.
- Debugging QWeb rendering errors (`t-if`, `t-foreach` issues).

## How It Works

1. **Activate**: Mention `@odoo-qweb-templates` and describe the report or template needed.
2. **Generate**: Receive a complete `ir.actions.report` record and QWeb template.
3. **Debug**: Paste a broken template to identify and fix rendering issues.

## Examples

### Example 1: Custom PDF Report

```xml
<!-- Report Action -->
<record id="action_report_patient_card" model="ir.actions.report">
    <field name="name">Patient Card</field>
    <field name="model">hospital.patient</field>
    <field name="report_type">qweb-pdf</field>
    <field name="report_name">hospital_management.report_patient_card</field>
    <field name="binding_model_id" ref="model_hospital_patient"/>
</record>

<!-- QWeb Template -->
<template id="report_patient_card">
    <t t-call="web.html_container">
        <t t-foreach="docs" t-as="doc">
            <t t-call="web.external_layout">
                <div class="page">
                    <h2>Patient Card</h2>
                    <table class="table table-bordered">
                        <tr>
                            <td><strong>Name:</strong></td>
                            <td><t t-field="doc.name"/></td>
                        </tr>
                        <tr>
                            <td><strong>Doctor:</strong></td>
                            <td><t t-field="doc.doctor_id.name"/></td>
                        </tr>
                        <tr>
                            <td><strong>Status:</strong></td>
                            <td><t t-field="doc.state"/></td>
                        </tr>
                    </table>
                </div>
            </t>
        </t>
    </t>
</template>
```

### Example 2: Conditional Rendering

```xml
<!-- Show a warning block only if the patient is not confirmed -->
<t t-if="doc.state == 'draft'">
    <div class="alert alert-warning">
        <strong>Warning:</strong> This patient has not been confirmed yet.
    </div>
</t>
```

## Best Practices

- ✅ **Do:** Use `t-field` for model fields — Odoo auto-formats dates, monetary values, and booleans correctly.
- ✅ **Do:** Use `t-out` (Odoo 15+) for safe HTML output of non-field strings. Use `t-esc` only on Odoo 14 and below (it HTML-escapes output).
- ✅ **Do:** Call `web.external_layout` for PDF reports to automatically include the company header, footer, and logo.
- ✅ **Do:** Use `_lt()` (lazy translation) for translatable string literals inside Python report helpers, not inline `t-esc`.
- ❌ **Don't:** Use raw Python expressions inside QWeb — compute values in the model or a report `_get_report_values()` helper.
- ❌ **Don't:** Forget `t-as` when using `t-foreach`; without it, you can't access the current record in the loop body.
- ❌ **Don't:** Use `t-esc` where you intend to render HTML content — it will escape the tags and print them as raw text.

## Limitations

- Does not cover **website controller routing** for dynamic QWeb pages — that requires Python `http.route` knowledge.
- **Email template** QWeb has different variable scope than report QWeb (`object` vs `docs`) — this skill primarily focuses on PDF reports.
- QWeb JavaScript (used in Kanban/Form widgets) is a different engine; this skill covers **server-side QWeb only**.
- Does not cover **wkhtmltopdf configuration** for PDF rendering issues (page size, margins, header/footer overlap).

## Imported Module: Odoo Sales Crm Expert
---
name: odoo-sales-crm-expert
description: "Expert guide for Odoo Sales and CRM: pipeline stages, quotation templates, pricelists, sales teams, lead scoring, and forecasting."
risk: safe
source: "self"
---

# Odoo Sales & CRM Expert

## Overview

This skill helps you configure and optimize Odoo Sales and CRM. It covers opportunity pipeline setup, automated lead assignment, quotation templates, pricelist strategies, sales team management, and the sales-to-invoice workflow.

## When to Use This Skill

- Designing CRM pipeline stages for your sales process.
- Creating a quotation template with optional products and bundles.
- Setting up pricelists with customer-tier pricing.
- Configuring automated lead assignment by territory or salesperson.

## How It Works

1. **Activate**: Mention `@odoo-sales-crm-expert` and describe your sales scenario.
2. **Configure**: Receive step-by-step Odoo setup instructions.
3. **Optimize**: Get recommendations for improving pipeline velocity and deal closure rate.

## Examples

### Example 1: Configure CRM Pipeline Stages

```text
Menu: CRM → Configuration → Stages → New

Typical B2B Pipeline:
  Stage 1: New Lead          (probability: 10%)
  Stage 2: Qualified         (probability: 25%)
  Stage 3: Proposal Sent     (probability: 50%)
  Stage 4: Negotiation       (probability: 75%)
  Stage 5: Won               (is_won: YES — marks opportunity as closed-won)
  Stage 6: Lost              (mark as lost via the "Mark as Lost" button)

Tips:
  - Enable "Rotting Days" in CRM Settings to flag stale deals in red
  - In Odoo 16+, Predictive Lead Scoring (AI) auto-updates probability
    based on historical data. Disable it in Settings if you prefer manual
    stage-based probability.
```

### Example 2: Create a Quotation Template

```text
Menu: Sales → Configuration → Quotation Templates → New
(Requires the "Sales Management" module — enabled in Sales Settings)

Template Name: SaaS Annual Subscription
Valid for: 30 days

Lines:
  1. Platform License   | Qty: 1 | Price: $1,200/yr | (required)
  2. Onboarding Package | Qty: 1 | Price: $500       | Optional
  3. Premium Support    | Qty: 1 | Price: $300/yr    | Optional
  4. Extra User License | Qty: 0 | Price: $120/user  | Optional

Signature & Payment:
  ☑ Online Signature required before order confirmation
  ☑ Online Payment (deposit) — 50% upfront

Notes section:
  "Prices valid until expiration date. Subject to Schedule A terms."
```

### Example 3: Customer Tier Pricelist (VIP Discount)

```text
Menu: Sales → Configuration → Settings
  ☑ Enable Pricelists

Menu: Sales → Configuration → Pricelists → New

Name: VIP Customer — 15% Off
Currency: USD
Discount Policy: Show public price & discount on quotation

Rules:
  Apply To: All Products
  Compute Price: Discount
  Discount: 15%
  Min. Quantity: 1

Assign to a customer:
  Customer record → Sales & Purchase tab → Pricelist → VIP Customer
```

## Best Practices

- ✅ **Do:** Use **Lost Reasons** (CRM → Configuration → Lost Reasons) to build a dataset of why deals are lost — invaluable for sales coaching.
- ✅ **Do:** Enable **Sales Teams** with revenue targets so pipeline forecasting is meaningful per team.
- ✅ **Do:** Set **Expected Revenue** and **Closing Date** on every opportunity — these feed the revenue forecast dashboard.
- ✅ **Do:** Use **Quotation Templates** to standardize offers and reduce quoting time across the team.
- ❌ **Don't:** Skip the CRM opportunity when selling — going directly from lead to invoice breaks pipeline analytics.
- ❌ **Don't:** Manually edit prices on quotation lines as a workaround — set up proper pricelists instead.
- ❌ **Don't:** Ignore the **Predictive Lead Scoring** feature in v16+ — configure it with historical data for accurate forecasting.

## Limitations

- **Commission rules** are not built into Odoo CRM out of the box — they require custom development or third-party modules.
- The **Quotation Template** optional product feature requires the **Sale Management** module; it is not available in the base `sale` module.
- **Territory-based lead assignment** (geographic routing) requires custom rules or the Enterprise Leads module.
- Odoo CRM does not have native **email sequence / cadence** automation — use the **Email Marketing** or **Marketing Automation** modules for drip campaigns.

## Imported Module: Odoo Upgrade Advisor
---
name: odoo-upgrade-advisor
description: "Step-by-step Odoo version upgrade advisor: pre-upgrade checklist, community vs enterprise upgrade path, OCA module compatibility, and post-upgrade validation."
risk: safe
source: "self"
---

# Odoo Upgrade Advisor

## Overview

Upgrading Odoo between major versions (e.g., v15 → v16 → v17) requires careful preparation, testing, and validation. This skill provides a structured pre-upgrade checklist, guides you through the upgrade tools (Odoo Upgrade Service and OpenUpgrade), and gives you a post-upgrade validation protocol.

## When to Use This Skill

- Planning a major Odoo version upgrade.
- Identifying which custom modules need to be migrated.
- Running the upgrade on a staging environment before production.
- Validating the system after an upgrade.

## How It Works

1. **Activate**: Mention `@odoo-upgrade-advisor`, state your current and target version.
2. **Plan**: Receive the full upgrade roadmap and risk assessment.
3. **Execute**: Get a step-by-step upgrade command sequence.

## Upgrade Paths

| From | To | Supported? | Tool |
|---|---|---|---|
| v16 | v17 | ✅ Direct | Odoo Upgrade Service / OpenUpgrade |
| v15 | v16 | ✅ Direct | Odoo Upgrade Service / OpenUpgrade |
| v14 | v15 | ✅ Direct | Odoo Upgrade Service / OpenUpgrade |
| v14 | v17 | ⚠️ Multi-hop | v14→v15→v16→v17 (cannot skip) |
| v13 or older | any | ❌ Not supported | Manual migration required |

## Examples

### Example 1: Pre-Upgrade Checklist

```text
BEFORE YOU START:
  ☑ 1. List all installed modules (Settings → Technical → Modules)
        Export to CSV and review for custom/OCA modules
  ☑ 2. Check OCA compatibility matrix for each community module
        https://github.com/OCA/maintainer-tools/wiki/Migration-Status
  ☑ 3. Take a full backup (database + filestore) — your restore point
  ☑ 4. Clone production to a staging environment
  ☑ 5. Run the Odoo Upgrade pre-analysis:
        https://upgrade.odoo.com/ → Upload DB → Review breaking changes report
  ☑ 6. Review custom modules against migration notes
        (use @odoo-migration-helper for per-module analysis)
  ☑ 7. Upgrade and test in staging → Fix all errors → Re-test
  ☑ 8. Schedule a production maintenance window
  ☑ 9. Notify users of scheduled downtime
  ☑ 10. Perform production upgrade → Validate → Go/No-Go decision
```

### Example 2: Community Upgrade with OpenUpgrade

```bash
# Clone OpenUpgrade for the TARGET version (e.g., upgrading to v17)
git clone https://github.com/OCA/OpenUpgrade.git \
  --branch 17.0 \
  --single-branch \
  /opt/openupgrade

# Run the migration against your staging database
python3 /opt/openupgrade/odoo-bin \
  --update all \
  --database odoo_staging \
  --config /etc/odoo/odoo.conf \
  --stop-after-init \
  --load openupgrade_framework

# Review the log for errors before touching production
tail -200 /var/log/odoo/odoo.log | grep -E "ERROR|WARNING|Traceback"
```

### Example 3: Post-Upgrade Validation Checklist

```text
After upgrading, validate these critical areas before going live:

Accounting:
  ☑ Trial Balance totals match the pre-upgrade snapshot
  ☑ Open invoices, bills, and payments are accessible
  ☑ Bank reconciliation can be performed on a test statement

Inventory:
  ☑ Stock valuation report matches pre-upgrade (run Inventory Valuation)
  ☑ Open Purchase Orders and Sale Orders are visible

HR / Payroll:
  ☑ All employee records are intact
  ☑ Payslips from the last 3 months are accessible and correct

Custom Modules:
  ☑ Every custom module loaded without ImportError or XML error
  ☑ Run the critical business workflows end-to-end:
      Create sale order → confirm → deliver → invoice → payment

Users & Security:
  ☑ User logins work correctly
  ☑ Access rights are preserved (spot-check 3-5 users)
```

## Best Practices

- ✅ **Do:** Always upgrade on a **copy of production** (staging) first — never the live instance.
- ✅ **Do:** Keep the old version running until the new version is **fully validated and signed off**.
- ✅ **Do:** Check OCA's migration status page: [OCA Migration Status](https://github.com/OCA/maintainer-tools/wiki/Migration-Status)
- ✅ **Do:** Use the [Odoo Upgrade Service](https://upgrade.odoo.com/) pre-analysis report to get a list of breaking changes **before writing any code**.
- ❌ **Don't:** Skip intermediate versions — Odoo requires sequential upgrades (v14→v15→v16→v17).
- ❌ **Don't:** Upgrade custom modules and Odoo core simultaneously — adapt Odoo core first, then fix custom modules.
- ❌ **Don't:** Run OpenUpgrade against production directly — always test on a staging copy first.

## Limitations

- Covers **v14–v17** only. Versions v13 and older have a fundamentally different module structure and require manual migration.
- **Enterprise-exclusive module changes** (e.g., `sign`, `account_accountant`) may have undocumented breaking changes not included in OpenUpgrade.
- The **Odoo.sh** automated upgrade path has a separate workflow (managed from the Odoo.sh dashboard) not covered here.
- OWL JavaScript component migration (legacy widget → OWL v16+) is a complex front-end topic beyond the scope of this skill.

## Imported Module: Odoo Xml Views Builder
---
name: odoo-xml-views-builder
description: "Expert at building Odoo XML views: Form, List, Kanban, Search, Calendar, and Graph. Generates correct XML for Odoo 14-17 with proper visibility syntax."
risk: safe
source: "self"
---

# Odoo XML Views Builder

## Overview

This skill generates and reviews Odoo XML view definitions for Kanban, Form, List, Search, Calendar, and Graph views. It understands visibility modifiers, `groups`, `domain`, `context`, and widget usage across Odoo versions 14–17, including the migration from `attrs` (v14–16) to inline expressions (v17+).

## When to Use This Skill

- Creating a new form or list view for a custom model.
- Adding fields, tabs, or smart buttons to an existing view.
- Building a Kanban view with color coding or progress bars.
- Creating a search view with filters and group-by options.

## How It Works

1. **Activate**: Mention `@odoo-xml-views-builder` and describe the view you want.
2. **Generate**: Get complete, ready-to-paste XML view definitions.
3. **Review**: Paste existing XML and get fixes for common mistakes.

## Examples

### Example 1: Form View with Tabs

```xml
<record id="view_hospital_patient_form" model="ir.ui.view">
    <field name="name">hospital.patient.form</field>
    <field name="model">hospital.patient</field>
    <field name="arch" type="xml">
        <form string="Patient">
            <header>
                <button name="action_confirm" string="Confirm"
                    type="object" class="btn-primary"
                    invisible="state != 'draft'"/>
                <field name="state" widget="statusbar"
                    statusbar_visible="draft,confirmed,done"/>
            </header>
            <sheet>
                <div class="oe_title">
                    <h1><field name="name" placeholder="Patient Name"/></h1>
                </div>
                <notebook>
                    <page string="General Info">
                        <group>
                            <field name="birth_date"/>
                            <field name="doctor_id"/>
                        </group>
                    </page>
                </notebook>
            </sheet>
            <chatter/>
        </form>
    </field>
</record>
```

### Example 2: Kanban View

```xml
<record id="view_hospital_patient_kanban" model="ir.ui.view">
    <field name="name">hospital.patient.kanban</field>
    <field name="model">hospital.patient</field>
    <field name="arch" type="xml">
        <kanban default_group_by="state" class="o_kanban_small_column">
            <field name="name"/>
            <field name="state"/>
            <field name="doctor_id"/>
            <templates>
                <t t-name="kanban-card">
                    <div class="oe_kanban_content">
                        <strong><field name="name"/></strong>
                        <div>Doctor: <field name="doctor_id"/></div>
                    </div>
                </t>
            </templates>
        </kanban>
    </field>
</record>
```

## Best Practices

- ✅ **Do:** Use inline `invisible="condition"` (Odoo 17+) instead of `attrs` for show/hide logic.
- ✅ **Do:** Use `attrs="{'invisible': [...]}"` only if you are targeting Odoo 14–16 — it is deprecated in v17.
- ✅ **Do:** Always set a `string` attribute on your view record for debugging clarity.
- ✅ **Do:** Use `<chatter/>` (v17) or `<div class="oe_chatter">` + field tags (v16 and below) for activity tracking.
- ❌ **Don't:** Use `attrs` in Odoo 17 — it is fully deprecated and raises warnings in logs.
- ❌ **Don't:** Put business logic in view XML — keep it in Python model methods.
- ❌ **Don't:** Use hardcoded `domain` strings in views when a `domain` field on the model can be used dynamically.

## Limitations

- Does not cover **OWL JavaScript widgets** or client-side component development.
- **Search panel views** (`<searchpanel>`) are not fully covered — those require frontend knowledge.
- Does not address **website QWeb views** — use `@odoo-qweb-templates` for those.
- **Cohort and Map views** (Enterprise-only) are not covered by this skill.

## Imported Module: Paypal Integration
---
name: paypal-integration
description: "Integrate PayPal payment processing with support for express checkout, subscriptions, and refund management. Use when implementing PayPal payments, processing online transactions, or building e-com..."
risk: unknown
source: community
date_added: "2026-02-27"
---

# PayPal Integration

Master PayPal payment integration including Express Checkout, IPN handling, recurring billing, and refund workflows.

## Do not use this skill when

- The task is unrelated to paypal integration
- You need a different domain or tool outside this scope

## Instructions

- Clarify goals, constraints, and required inputs.
- Apply relevant best practices and validate outcomes.
- Provide actionable steps and verification.
- If detailed examples are required, open `resources/implementation-playbook.md`.

## Use this skill when

- Integrating PayPal as a payment option
- Implementing express checkout flows
- Setting up recurring billing with PayPal
- Processing refunds and payment disputes
- Handling PayPal webhooks (IPN)
- Supporting international payments
- Implementing PayPal subscriptions

## Core Concepts

### 1. Payment Products
**PayPal Checkout**
- One-time payments
- Express checkout experience
- Guest and PayPal account payments

**PayPal Subscriptions**
- Recurring billing
- Subscription plans
- Automatic renewals

**PayPal Payouts**
- Send money to multiple recipients
- Marketplace and platform payments

### 2. Integration Methods
**Client-Side (JavaScript SDK)**
- Smart Payment Buttons
- Hosted payment flow
- Minimal backend code

**Server-Side (REST API)**
- Full control over payment flow
- Custom checkout UI
- Advanced features

### 3. IPN (Instant Payment Notification)
- Webhook-like payment notifications
- Asynchronous payment updates
- Verification required

## Quick Start

```javascript
// Frontend - PayPal Smart Buttons
<div id="paypal-button-container"></div>

<script src="https://www.paypal.com/sdk/js?client-id=YOUR_CLIENT_ID&currency=USD"></script>
<script>
  paypal.Buttons({
    createOrder: function(data, actions) {
      return actions.order.create({
        purchase_units: [{
          amount: {
            value: '25.00'
          }
        }]
      });
    },
    onApprove: function(data, actions) {
      return actions.order.capture().then(function(details) {
        // Payment successful
        console.log('Transaction completed by ' + details.payer.name.given_name);

        // Send to backend for verification
        fetch('/api/paypal/capture', {
          method: 'POST',
          headers: {'Content-Type': 'application/json'},
          body: JSON.stringify({orderID: data.orderID})
        });
      });
    }
  }).render('#paypal-button-container');
</script>
```

```python
# Backend - Verify and capture order
from paypalrestsdk import Payment
import paypalrestsdk

paypalrestsdk.configure({
    "mode": "sandbox",  # or "live"
    "client_id": "YOUR_CLIENT_ID",
    "client_secret": "YOUR_CLIENT_SECRET"
})

def capture_paypal_order(order_id):
    """Capture a PayPal order."""
    payment = Payment.find(order_id)

    if payment.execute({"payer_id": payment.payer.payer_info.payer_id}):
        # Payment successful
        return {
            'status': 'success',
            'transaction_id': payment.id,
            'amount': payment.transactions[0].amount.total
        }
    else:
        # Payment failed
        return {
            'status': 'failed',
            'error': payment.error
        }
```

## Express Checkout Implementation

### Server-Side Order Creation
```python
import requests
import json

class PayPalClient:
    def __init__(self, client_id, client_secret, mode='sandbox'):
        self.client_id = client_id
        self.client_secret = client_secret
        self.base_url = 'https://api-m.sandbox.paypal.com' if mode == 'sandbox' else 'https://api-m.paypal.com'
        self.access_token = self.get_access_token()

    def get_access_token(self):
        """Get OAuth access token."""
        url = f"{self.base_url}/v1/oauth2/token"
        headers = {"Accept": "application/json", "Accept-Language": "en_US"}

        response = requests.post(
            url,
            headers=headers,
            data={"grant_type": "client_credentials"},
            auth=(self.client_id, self.client_secret)
        )

        return response.json()['access_token']

    def create_order(self, amount, currency='USD'):
        """Create a PayPal order."""
        url = f"{self.base_url}/v2/checkout/orders"
        headers = {
            "Content-Type": "application/json",
            "Authorization": f"Bearer {self.access_token}"
        }

        payload = {
            "intent": "CAPTURE",
            "purchase_units": [{
                "amount": {
                    "currency_code": currency,
                    "value": str(amount)
                }
            }]
        }

        response = requests.post(url, headers=headers, json=payload)
        return response.json()

    def capture_order(self, order_id):
        """Capture payment for an order."""
        url = f"{self.base_url}/v2/checkout/orders/{order_id}/capture"
        headers = {
            "Content-Type": "application/json",
            "Authorization": f"Bearer {self.access_token}"
        }

        response = requests.post(url, headers=headers)
        return response.json()

    def get_order_details(self, order_id):
        """Get order details."""
        url = f"{self.base_url}/v2/checkout/orders/{order_id}"
        headers = {
            "Authorization": f"Bearer {self.access_token}"
        }

        response = requests.get(url, headers=headers)
        return response.json()
```

## IPN (Instant Payment Notification) Handling

### IPN Verification and Processing
```python
from flask import Flask, request
import requests
from urllib.parse import parse_qs

app = Flask(__name__)

@app.route('/ipn', methods=['POST'])
def handle_ipn():
    """Handle PayPal IPN notifications."""
    # Get IPN message
    ipn_data = request.form.to_dict()

    # Verify IPN with PayPal
    if not verify_ipn(ipn_data):
        return 'IPN verification failed', 400

    # Process IPN based on transaction type
    payment_status = ipn_data.get('payment_status')
    txn_type = ipn_data.get('txn_type')

    if payment_status == 'Completed':
        handle_payment_completed(ipn_data)
    elif payment_status == 'Refunded':
        handle_refund(ipn_data)
    elif payment_status == 'Reversed':
        handle_chargeback(ipn_data)

    return 'IPN processed', 200

def verify_ipn(ipn_data):
    """Verify IPN message authenticity."""
    # Add 'cmd' parameter
    verify_data = ipn_data.copy()
    verify_data['cmd'] = '_notify-validate'

    # Send back to PayPal for verification
    paypal_url = 'https://ipnpb.sandbox.paypal.com/cgi-bin/webscr'  # or production URL

    response = requests.post(paypal_url, data=verify_data)

    return response.text == 'VERIFIED'

def handle_payment_completed(ipn_data):
    """Process completed payment."""
    txn_id = ipn_data.get('txn_id')
    payer_email = ipn_data.get('payer_email')
    mc_gross = ipn_data.get('mc_gross')
    item_name = ipn_data.get('item_name')

    # Check if already processed (prevent duplicates)
    if is_transaction_processed(txn_id):
        return

    # Update database
    # Send confirmation email
    # Fulfill order
    print(f"Payment completed: {txn_id}, Amount: ${mc_gross}")

def handle_refund(ipn_data):
    """Handle refund."""
    parent_txn_id = ipn_data.get('parent_txn_id')
    mc_gross = ipn_data.get('mc_gross')

    # Process refund in your system
    print(f"Refund processed: {parent_txn_id}, Amount: ${mc_gross}")

def handle_chargeback(ipn_data):
    """Handle payment reversal/chargeback."""
    txn_id = ipn_data.get('txn_id')
    reason_code = ipn_data.get('reason_code')

    # Handle chargeback
    print(f"Chargeback: {txn_id}, Reason: {reason_code}")
```

## Subscription/Recurring Billing

### Create Subscription Plan
```python
def create_subscription_plan(name, amount, interval='MONTH'):
    """Create a subscription plan."""
    client = PayPalClient(CLIENT_ID, CLIENT_SECRET)

    url = f"{client.base_url}/v1/billing/plans"
    headers = {
        "Content-Type": "application/json",
        "Authorization": f"Bearer {client.access_token}"
    }

    payload = {
        "product_id": "PRODUCT_ID",  # Create product first
        "name": name,
        "billing_cycles": [{
            "frequency": {
                "interval_unit": interval,
                "interval_count": 1
            },
            "tenure_type": "REGULAR",
            "sequence": 1,
            "total_cycles": 0,  # Infinite
            "pricing_scheme": {
                "fixed_price": {
                    "value": str(amount),
                    "currency_code": "USD"
                }
            }
        }],
        "payment_preferences": {
            "auto_bill_outstanding": True,
            "setup_fee": {
                "value": "0",
                "currency_code": "USD"
            },
            "setup_fee_failure_action": "CONTINUE",
            "payment_failure_threshold": 3
        }
    }

    response = requests.post(url, headers=headers, json=payload)
    return response.json()

def create_subscription(plan_id, subscriber_email):
    """Create a subscription for a customer."""
    client = PayPalClient(CLIENT_ID, CLIENT_SECRET)

    url = f"{client.base_url}/v1/billing/subscriptions"
    headers = {
        "Content-Type": "application/json",
        "Authorization": f"Bearer {client.access_token}"
    }

    payload = {
        "plan_id": plan_id,
        "subscriber": {
            "email_address": subscriber_email
        },
        "application_context": {
            "return_url": "https://yourdomain.com/subscription/success",
            "cancel_url": "https://yourdomain.com/subscription/cancel"
        }
    }

    response = requests.post(url, headers=headers, json=payload)
    subscription = response.json()

    # Get approval URL
    for link in subscription.get('links', []):
        if link['rel'] == 'approve':
            return {
                'subscription_id': subscription['id'],
                'approval_url': link['href']
            }
```

## Refund Workflows

```python
def create_refund(capture_id, amount=None, note=None):
    """Create a refund for a captured payment."""
    client = PayPalClient(CLIENT_ID, CLIENT_SECRET)

    url = f"{client.base_url}/v2/payments/captures/{capture_id}/refund"
    headers = {
        "Content-Type": "application/json",
        "Authorization": f"Bearer {client.access_token}"
    }

    payload = {}
    if amount:
        payload["amount"] = {
            "value": str(amount),
            "currency_code": "USD"
        }

    if note:
        payload["note_to_payer"] = note

    response = requests.post(url, headers=headers, json=payload)
    return response.json()

def get_refund_details(refund_id):
    """Get refund details."""
    client = PayPalClient(CLIENT_ID, CLIENT_SECRET)

    url = f"{client.base_url}/v2/payments/refunds/{refund_id}"
    headers = {
        "Authorization": f"Bearer {client.access_token}"
    }

    response = requests.get(url, headers=headers)
    return response.json()
```

## Error Handling

```python
class PayPalError(Exception):
    """Custom PayPal error."""
    pass

def handle_paypal_api_call(api_function):
    """Wrapper for PayPal API calls with error handling."""
    try:
        result = api_function()
        return result
    except requests.exceptions.RequestException as e:
        # Network error
        raise PayPalError(f"Network error: {str(e)}")
    except Exception as e:
        # Other errors
        raise PayPalError(f"PayPal API error: {str(e)}")

# Usage
try:
    order = handle_paypal_api_call(lambda: client.create_order(25.00))
except PayPalError as e:
    # Handle error appropriately
    log_error(e)
```

## Testing

```python
# Use sandbox credentials
SANDBOX_CLIENT_ID = "..."
SANDBOX_SECRET = "..."

# Test accounts
# Create test buyer and seller accounts at developer.paypal.com

def test_payment_flow():
    """Test complete payment flow."""
    client = PayPalClient(SANDBOX_CLIENT_ID, SANDBOX_SECRET, mode='sandbox')

    # Create order
    order = client.create_order(10.00)
    assert 'id' in order

    # Get approval URL
    approval_url = next((link['href'] for link in order['links'] if link['rel'] == 'approve'), None)
    assert approval_url is not None

    # After approval (manual step with test account)
    # Capture order
    # captured = client.capture_order(order['id'])
    # assert captured['status'] == 'COMPLETED'
```

## Resources

- **references/express-checkout.md**: Express Checkout implementation guide
- **references/ipn-handling.md**: IPN verification and processing
- **references/refund-workflows.md**: Refund handling patterns
- **references/billing-agreements.md**: Recurring billing setup
- **assets/paypal-client.py**: Production PayPal client
- **assets/ipn-processor.py**: IPN webhook processor
- **assets/recurring-billing.py**: Subscription management

## Best Practices

1. **Always Verify IPN**: Never trust IPN without verification
2. **Idempotent Processing**: Handle duplicate IPN notifications
3. **Error Handling**: Implement robust error handling
4. **Logging**: Log all transactions and errors
5. **Test Thoroughly**: Use sandbox extensively
6. **Webhook Backup**: Don't rely solely on client-side callbacks
7. **Currency Handling**: Always specify currency explicitly

## Common Pitfalls

- **Not Verifying IPN**: Accepting IPN without verification
- **Duplicate Processing**: Not checking for duplicate transactions
- **Wrong Environment**: Mixing sandbox and production URLs/credentials
- **Missing Webhooks**: Not handling all payment states
- **Hardcoded Values**: Not making configurable for different environments

## Imported Module: Plaid Fintech
---
name: plaid-fintech
description: "Expert patterns for Plaid API integration including Link token flows, transactions sync, identity verification, Auth for ACH, balance checks, webhook handling, and fintech compliance best practices..."
risk: unknown
source: "vibeship-spawner-skills (Apache 2.0)"
date_added: "2026-02-27"
---

# Plaid Fintech

## Patterns

### Link Token Creation and Exchange

Create a link_token for Plaid Link, exchange public_token for access_token.
Link tokens are short-lived, one-time use. Access tokens don't expire but
may need updating when users change passwords.


### Transactions Sync

Use /transactions/sync for incremental transaction updates. More efficient
than /transactions/get. Handle webhooks for real-time updates instead of
polling.


### Item Error Handling and Update Mode

Handle ITEM_LOGIN_REQUIRED errors by putting users through Link update mode.
Listen for PENDING_DISCONNECT webhook to proactively prompt users.


## Anti-Patterns

### ❌ Storing Access Tokens in Plain Text

### ❌ Polling Instead of Webhooks

### ❌ Ignoring Item Errors

## ⚠️ Sharp Edges

| Issue | Severity | Solution |
|-------|----------|----------|
| Issue | critical | See docs |
| Issue | high | See docs |
| Issue | high | See docs |
| Issue | high | See docs |
| Issue | medium | See docs |
| Issue | medium | See docs |
| Issue | medium | See docs |
| Issue | medium | See docs |

## When to Use
This skill is applicable to execute the workflow or actions described in the overview.

## Imported Module: Rails Upgrade
---
name: rails-upgrade
description: Rails Upgrade
---

404: Not Found

## Imported Module: Skill Rails Upgrade
---
name: skill-rails-upgrade
description: "Analyze Rails apps and provide upgrade assessments"
risk: safe
source: "https://github.com/robzolkos/skill-rails-upgrade"
date_added: "2026-02-27"
---

## When to Use This Skill

Analyze Rails apps and provide upgrade assessments

Use this skill when working with analyze rails apps and provide upgrade assessments.
# Rails Upgrade Analyzer

Analyze the current Rails application and provide a comprehensive upgrade assessment with selective file merging.

## Step 1: Verify Rails Application

Check that we're in a Rails application by looking for these files:
- `Gemfile` (must exist and contain 'rails')
- `config/application.rb` (Rails application config)
- `config/environment.rb` (Rails environment)

If any of these are missing or don't indicate a Rails app, stop and inform the user this doesn't appear to be a Rails application.

## Step 2: Get Current Rails Version

Extract the current Rails version from:
1. First, check `Gemfile.lock` for the exact installed version (look for `rails (x.y.z)`)
2. If not found, check `Gemfile` for the version constraint

Report the exact current version (e.g., `7.1.3`).

## Step 3: Find Latest Rails Version

Use the GitHub CLI to fetch the latest Rails release:

```bash
gh api repos/rails/rails/releases/latest --jq '.tag_name'
```

This returns the latest stable version tag (e.g., `v8.0.1`). Strip the 'v' prefix for comparison.

Also check recent tags to understand the release landscape:

```bash
gh api repos/rails/rails/tags --jq '.[0:10] | .[].name'
```

## Step 4: Determine Upgrade Type

Compare current and latest versions to classify the upgrade:

- **Patch upgrade**: Same major.minor, different patch (e.g., 7.1.3 → 7.1.5)
- **Minor upgrade**: Same major, different minor (e.g., 7.1.3 → 7.2.0)
- **Major upgrade**: Different major version (e.g., 7.1.3 → 8.0.0)

## Step 5: Fetch Upgrade Guide

Use WebFetch to get the official Rails upgrade guide:

URL: `https://guides.rubyonrails.org/upgrading_ruby_on_rails.html`

Look for sections relevant to the version jump. The guide is organized by target version with sections like:
- "Upgrading from Rails X.Y to Rails X.Z"
- Breaking changes
- Deprecation warnings
- Configuration changes
- Required migrations

Extract and summarize the relevant sections for the user's specific upgrade path.

## Step 6: Fetch Rails Diff

Use WebFetch to get the diff between versions from railsdiff.org:

URL: `https://railsdiff.org/{current_version}/{target_version}`

For example: `https://railsdiff.org/7.1.3/8.0.0`

This shows:
- Changes to default configuration files
- New files that need to be added
- Modified initializers
- Updated dependencies
- Changes to bin/ scripts

Summarize the key file changes.

## Step 7: Check JavaScript Dependencies

Rails applications often include JavaScript packages that should be updated alongside Rails. Check for and report on these dependencies.

### 7.1: Identify JS Package Manager

Check which package manager the app uses:

```bash
# Check for package.json (npm/yarn)
ls package.json 2>/dev/null

# Check for importmap (Rails 7+)
ls config/importmap.rb 2>/dev/null
```

### 7.2: Check Rails-Related JS Packages

If `package.json` exists, check for these Rails-related packages:

```bash
# Extract current versions of Rails-related packages
cat package.json | grep -E '"@hotwired/|"@rails/|"stimulus"|"turbo-rails"' || echo "No Rails JS packages found"
```

**Key packages to check:**

| Package | Purpose | Version Alignment |
|---------|---------|-------------------|
| `@hotwired/turbo-rails` | Turbo Drive/Frames/Streams | Should match Rails version era |
| `@hotwired/stimulus` | Stimulus JS framework | Generally stable across Rails versions |
| `@rails/actioncable` | WebSocket support | Should match Rails version |
| `@rails/activestorage` | Direct uploads | Should match Rails version |
| `@rails/actiontext` | Rich text editing | Should match Rails version |
| `@rails/request.js` | Rails UJS replacement | Should match Rails version era |

### 7.3: Check for Updates

For npm/yarn projects, check for available updates:

```bash
# Using npm
npm outdated @hotwired/turbo-rails @hotwired/stimulus @rails/actioncable @rails/activestorage 2>/dev/null

# Or check latest versions directly
npm view @hotwired/turbo-rails version 2>/dev/null
npm view @rails/actioncable version 2>/dev/null
```

### 7.4: Check Importmap Pins (if applicable)

If the app uses importmap-rails, check `config/importmap.rb` for pinned versions:

```bash
cat config/importmap.rb | grep -E 'pin.*turbo|pin.*stimulus|pin.*@rails' || echo "No importmap pins found"
```

To update importmap pins:
```bash
bin/importmap pin @hotwired/turbo-rails
bin/importmap pin @hotwired/stimulus
```

### 7.5: JS Dependency Summary

Include in the upgrade summary:

```
### JavaScript Dependencies

**Package Manager**: [npm/yarn/importmap/none]

| Package | Current | Latest | Action |
|---------|---------|--------|--------|
| @hotwired/turbo-rails | 8.0.4 | 8.0.12 | Update recommended |
| @rails/actioncable | 7.1.0 | 8.0.0 | Update with Rails |
| ... | ... | ... | ... |

**Recommended JS Updates:**
- Run `npm update @hotwired/turbo-rails` (or yarn equivalent)
- Run `npm update @rails/actioncable @rails/activestorage` to match Rails version
```

---

## Step 8: Generate Upgrade Summary

Provide a comprehensive summary including all findings from Steps 1-7:

### Version Information
- Current version: X.Y.Z
- Latest version: A.B.C
- Upgrade type: [Patch/Minor/Major]

### Upgrade Complexity Assessment

Rate the upgrade as **Small**, **Medium**, or **Large** based on:

| Factor | Small | Medium | Large |
|--------|-------|--------|-------|
| Version jump | Patch only | Minor version | Major version |
| Breaking changes | None | Few, well-documented | Many, significant |
| Config changes | Minimal | Moderate | Extensive |
| Deprecations | None active | Some to address | Many requiring refactoring |
| Dependencies | Compatible | Some updates needed | Major dependency updates |

### Key Changes to Address

List the most important changes the user needs to handle:
1. Configuration file updates
2. Deprecated methods/features to update
3. New required dependencies
4. Database migrations needed
5. Breaking API changes

### Recommended Upgrade Steps

1. Update test suite and ensure passing
2. Review deprecation warnings in current version
3. Update Gemfile with new Rails version
4. Run `bundle update rails`
5. Update JavaScript dependencies (see JS Dependencies section)
6. **DO NOT run `rails app:update` directly** - use the selective merge process below
7. Run database migrations
8. Run test suite
9. Review and update deprecated code

### Resources

- Rails Upgrade Guide: https://guides.rubyonrails.org/upgrading_ruby_on_rails.html
- Rails Diff: https://railsdiff.org/{current}/{target}
- Release Notes: https://github.com/rails/rails/releases/tag/v{target}

---


## When to Use This Skill

Analyze Rails apps and provide upgrade assessments

Use this skill when working with analyze rails apps and provide upgrade assessments.
## Step 9: Selective File Update (replaces `rails app:update`)

**IMPORTANT:** Do NOT run `rails app:update` as it overwrites files without considering local customizations. Instead, follow this selective merge process:

### 9.1: Detect Local Customizations

Before any upgrade, identify files with local customizations:

```bash
# Check for uncommitted changes
git status

# List config files that differ from a fresh Rails app
# These are the files we need to be careful with
git diff HEAD --name-only -- config/ bin/ public/
```

Create a mental list of files in these categories:
- **Custom config files**: Files with project-specific settings (i18n, mailer, etc.)
- **Modified bin scripts**: Scripts with custom behavior (bin/dev with foreman, etc.)
- **Standard files**: Files that haven't been customized

### 9.2: Analyze Required Changes from Railsdiff

Based on the railsdiff output from Step 6, categorize each changed file:

| Category | Action | Example |
|----------|--------|---------|
| **New files** | Create directly | `config/initializers/new_framework_defaults_X_Y.rb` |
| **Unchanged locally** | Safe to overwrite | `public/404.html` (if not customized) |
| **Customized locally** | Manual merge needed | `config/application.rb`, `bin/dev` |
| **Comment-only changes** | Usually skip | Minor comment updates in config files |

### 9.3: Create Upgrade Plan

Present the user with a clear upgrade plan:

```
## Upgrade Plan: Rails X.Y.Z → A.B.C

### New Files (will be created):
- config/initializers/new_framework_defaults_A_B.rb
- bin/ci (new CI script)

### Safe to Update (no local customizations):
- public/400.html
- public/404.html
- public/500.html

### Needs Manual Merge (local customizations detected):
- config/application.rb
  └─ Local: i18n configuration
  └─ Rails: [describe new Rails changes if any]

- config/environments/development.rb
  └─ Local: letter_opener mailer config
  └─ Rails: [describe new Rails changes]

- bin/dev
  └─ Local: foreman + Procfile.dev setup
  └─ Rails: changed to simple ruby script

### Skip (comment-only or irrelevant changes):
- config/puma.rb (only comment changes)
```

### 9.4: Execute Upgrade Plan

After user confirms the plan:

#### For New Files:
Create them directly using the content from railsdiff or by extracting from a fresh Rails app:

```bash
# Generate a temporary fresh Rails app to extract new files
cd /tmp && rails new rails_template --skip-git --skip-bundle
# Then copy needed files
```

Or use the Rails generator for specific files:
```bash
bin/rails app:update:configs  # Only updates config files, still interactive
```

#### For Safe Updates:
Overwrite these files as they have no local customizations.

#### For Manual Merges:
For each file needing merge, show the user:

1. **Current local version** (their customizations)
2. **New Rails default** (from railsdiff)
3. **Suggested merged version** that:
   - Keeps all local customizations
   - Adds only essential new Rails functionality
   - Removes deprecated settings

Example merge for `config/application.rb`:
```ruby
# KEEP local customizations:
config.i18n.available_locales = [:de, :en]
config.i18n.default_locale = :de
config.i18n.fallbacks = [:en]

# ADD new Rails 8.1 settings if needed:
# (usually none required - new defaults come via new_framework_defaults file)
```

### 9.5: Handle Active Storage Migrations

After file updates, run any new migrations:

```bash
bin/rails db:migrate
```

Check for new migrations that were added:
```bash
ls -la db/migrate/ | tail -10
```

### 9.6: Verify Upgrade

After completing the merge:

1. Start the Rails server and check for errors:
   ```bash
   bin/dev  # or bin/rails server
   ```

2. Check the Rails console:
   ```bash
   bin/rails console
   ```

3. Run the test suite:
   ```bash
   bin/rails test
   ```

4. Review deprecation warnings in logs

---

## Step 10: Finalize Framework Defaults

After verifying the app works:

1. Review `config/initializers/new_framework_defaults_X_Y.rb`
2. Enable each new default one by one, testing after each
3. Once all defaults are enabled and tested, update `config/application.rb`:
   ```ruby
   config.load_defaults X.Y  # Update to new version
   ```
4. Delete the `new_framework_defaults_X_Y.rb` file

---


## When to Use This Skill

Analyze Rails apps and provide upgrade assessments

Use this skill when working with analyze rails apps and provide upgrade assessments.
## Error Handling

- If `gh` CLI is not authenticated, instruct the user to run `gh auth login`
- If railsdiff.org doesn't have the exact versions, try with major.minor.0 versions
- If the app is already on the latest version, congratulate the user and note any upcoming releases
- If local customizations would be lost, ALWAYS stop and show the user what would be overwritten before proceeding

## Key Principles

1. **Never overwrite without checking** - Always check for local customizations first
2. **Preserve user intent** - Local customizations exist for a reason
3. **Minimal changes** - Only add what's necessary for the new Rails version
4. **Transparency** - Show the user exactly what will change before doing it
5. **Reversibility** - User should be able to `git checkout` to restore if needed

## Imported Module: Stripe Integration
---
name: stripe-integration
description: "Implement Stripe payment processing for robust, PCI-compliant payment flows including checkout, subscriptions, and webhooks. Use when integrating Stripe payments, building subscription systems, or ..."
risk: unknown
source: community
date_added: "2026-02-27"
---

# Stripe Integration

Master Stripe payment processing integration for robust, PCI-compliant payment flows including checkout, subscriptions, webhooks, and refunds.

## Do not use this skill when

- The task is unrelated to stripe integration
- You need a different domain or tool outside this scope

## Instructions

- Clarify goals, constraints, and required inputs.
- Apply relevant best practices and validate outcomes.
- Provide actionable steps and verification.
- If detailed examples are required, open `resources/implementation-playbook.md`.

## Use this skill when

- Implementing payment processing in web/mobile applications
- Setting up subscription billing systems
- Handling one-time payments and recurring charges
- Processing refunds and disputes
- Managing customer payment methods
- Implementing SCA (Strong Customer Authentication) for European payments
- Building marketplace payment flows with Stripe Connect

## Core Concepts

### 1. Payment Flows
**Checkout Session (Hosted)**
- Stripe-hosted payment page
- Minimal PCI compliance burden
- Fastest implementation
- Supports one-time and recurring payments

**Payment Intents (Custom UI)**
- Full control over payment UI
- Requires Stripe.js for PCI compliance
- More complex implementation
- Better customization options

**Setup Intents (Save Payment Methods)**
- Collect payment method without charging
- Used for subscriptions and future payments
- Requires customer confirmation

### 2. Webhooks
**Critical Events:**
- `payment_intent.succeeded`: Payment completed
- `payment_intent.payment_failed`: Payment failed
- `customer.subscription.updated`: Subscription changed
- `customer.subscription.deleted`: Subscription canceled
- `charge.refunded`: Refund processed
- `invoice.payment_succeeded`: Subscription payment successful

### 3. Subscriptions
**Components:**
- **Product**: What you're selling
- **Price**: How much and how often
- **Subscription**: Customer's recurring payment
- **Invoice**: Generated for each billing cycle

### 4. Customer Management
- Create and manage customer records
- Store multiple payment methods
- Track customer metadata
- Manage billing details

## Quick Start

```python
import stripe

stripe.api_key = "sk_test_..."

# Create a checkout session
session = stripe.checkout.Session.create(
    payment_method_types=['card'],
    line_items=[{
        'price_data': {
            'currency': 'usd',
            'product_data': {
                'name': 'Premium Subscription',
            },
            'unit_amount': 2000,  # $20.00
            'recurring': {
                'interval': 'month',
            },
        },
        'quantity': 1,
    }],
    mode='subscription',
    success_url='https://yourdomain.com/success?session_id={CHECKOUT_SESSION_ID}',
    cancel_url='https://yourdomain.com/cancel',
)

# Redirect user to session.url
print(session.url)
```

## Payment Implementation Patterns

### Pattern 1: One-Time Payment (Hosted Checkout)
```python
def create_checkout_session(amount, currency='usd'):
    """Create a one-time payment checkout session."""
    try:
        session = stripe.checkout.Session.create(
            payment_method_types=['card'],
            line_items=[{
                'price_data': {
                    'currency': currency,
                    'product_data': {
                        'name': 'Purchase',
                        'images': ['https://example.com/product.jpg'],
                    },
                    'unit_amount': amount,  # Amount in cents
                },
                'quantity': 1,
            }],
            mode='payment',
            success_url='https://yourdomain.com/success?session_id={CHECKOUT_SESSION_ID}',
            cancel_url='https://yourdomain.com/cancel',
            metadata={
                'order_id': 'order_123',
                'user_id': 'user_456'
            }
        )
        return session
    except stripe.error.StripeError as e:
        # Handle error
        print(f"Stripe error: {e.user_message}")
        raise
```

### Pattern 2: Custom Payment Intent Flow
```python
def create_payment_intent(amount, currency='usd', customer_id=None):
    """Create a payment intent for custom checkout UI."""
    intent = stripe.PaymentIntent.create(
        amount=amount,
        currency=currency,
        customer=customer_id,
        automatic_payment_methods={
            'enabled': True,
        },
        metadata={
            'integration_check': 'accept_a_payment'
        }
    )
    return intent.client_secret  # Send to frontend

# Frontend (JavaScript)
"""
const stripe = Stripe('pk_test_...');
const elements = stripe.elements();
const cardElement = elements.create('card');
cardElement.mount('#card-element');

const {error, paymentIntent} = await stripe.confirmCardPayment(
    clientSecret,
    {
        payment_method: {
            card: cardElement,
            billing_details: {
                name: 'Customer Name'
            }
        }
    }
);

if (error) {
    // Handle error
} else if (paymentIntent.status === 'succeeded') {
    // Payment successful
}
"""
```

### Pattern 3: Subscription Creation
```python
def create_subscription(customer_id, price_id):
    """Create a subscription for a customer."""
    try:
        subscription = stripe.Subscription.create(
            customer=customer_id,
            items=[{'price': price_id}],
            payment_behavior='default_incomplete',
            payment_settings={'save_default_payment_method': 'on_subscription'},
            expand=['latest_invoice.payment_intent'],
        )

        return {
            'subscription_id': subscription.id,
            'client_secret': subscription.latest_invoice.payment_intent.client_secret
        }
    except stripe.error.StripeError as e:
        print(f"Subscription creation failed: {e}")
        raise
```

### Pattern 4: Customer Portal
```python
def create_customer_portal_session(customer_id):
    """Create a portal session for customers to manage subscriptions."""
    session = stripe.billing_portal.Session.create(
        customer=customer_id,
        return_url='https://yourdomain.com/account',
    )
    return session.url  # Redirect customer here
```

## Webhook Handling

### Secure Webhook Endpoint
```python
from flask import Flask, request
import stripe

app = Flask(__name__)

endpoint_secret = 'whsec_...'

@app.route('/webhook', methods=['POST'])
def webhook():
    payload = request.data
    sig_header = request.headers.get('Stripe-Signature')

    try:
        event = stripe.Webhook.construct_event(
            payload, sig_header, endpoint_secret
        )
    except ValueError:
        # Invalid payload
        return 'Invalid payload', 400
    except stripe.error.SignatureVerificationError:
        # Invalid signature
        return 'Invalid signature', 400

    # Handle the event
    if event['type'] == 'payment_intent.succeeded':
        payment_intent = event['data']['object']
        handle_successful_payment(payment_intent)
    elif event['type'] == 'payment_intent.payment_failed':
        payment_intent = event['data']['object']
        handle_failed_payment(payment_intent)
    elif event['type'] == 'customer.subscription.deleted':
        subscription = event['data']['object']
        handle_subscription_canceled(subscription)

    return 'Success', 200

def handle_successful_payment(payment_intent):
    """Process successful payment."""
    customer_id = payment_intent.get('customer')
    amount = payment_intent['amount']
    metadata = payment_intent.get('metadata', {})

    # Update your database
    # Send confirmation email
    # Fulfill order
    print(f"Payment succeeded: {payment_intent['id']}")

def handle_failed_payment(payment_intent):
    """Handle failed payment."""
    error = payment_intent.get('last_payment_error', {})
    print(f"Payment failed: {error.get('message')}")
    # Notify customer
    # Update order status

def handle_subscription_canceled(subscription):
    """Handle subscription cancellation."""
    customer_id = subscription['customer']
    # Update user access
    # Send cancellation email
    print(f"Subscription canceled: {subscription['id']}")
```

### Webhook Best Practices
```python
import hashlib
import hmac

def verify_webhook_signature(payload, signature, secret):
    """Manually verify webhook signature."""
    expected_sig = hmac.new(
        secret.encode('utf-8'),
        payload,
        hashlib.sha256
    ).hexdigest()

    return hmac.compare_digest(signature, expected_sig)

def handle_webhook_idempotently(event_id, handler):
    """Ensure webhook is processed exactly once."""
    # Check if event already processed
    if is_event_processed(event_id):
        return

    # Process event
    try:
        handler()
        mark_event_processed(event_id)
    except Exception as e:
        log_error(e)
        # Stripe will retry failed webhooks
        raise
```

## Customer Management

```python
def create_customer(email, name, payment_method_id=None):
    """Create a Stripe customer."""
    customer = stripe.Customer.create(
        email=email,
        name=name,
        payment_method=payment_method_id,
        invoice_settings={
            'default_payment_method': payment_method_id
        } if payment_method_id else None,
        metadata={
            'user_id': '12345'
        }
    )
    return customer

def attach_payment_method(customer_id, payment_method_id):
    """Attach a payment method to a customer."""
    stripe.PaymentMethod.attach(
        payment_method_id,
        customer=customer_id
    )

    # Set as default
    stripe.Customer.modify(
        customer_id,
        invoice_settings={
            'default_payment_method': payment_method_id
        }
    )

def list_customer_payment_methods(customer_id):
    """List all payment methods for a customer."""
    payment_methods = stripe.PaymentMethod.list(
        customer=customer_id,
        type='card'
    )
    return payment_methods.data
```

## Refund Handling

```python
def create_refund(payment_intent_id, amount=None, reason=None):
    """Create a refund."""
    refund_params = {
        'payment_intent': payment_intent_id
    }

    if amount:
        refund_params['amount'] = amount  # Partial refund

    if reason:
        refund_params['reason'] = reason  # 'duplicate', 'fraudulent', 'requested_by_customer'

    refund = stripe.Refund.create(**refund_params)
    return refund

def handle_dispute(charge_id, evidence):
    """Update dispute with evidence."""
    stripe.Dispute.modify(
        charge_id,
        evidence={
            'customer_name': evidence.get('customer_name'),
            'customer_email_address': evidence.get('customer_email'),
            'shipping_documentation': evidence.get('shipping_proof'),
            'customer_communication': evidence.get('communication'),
        }
    )
```

## Testing

```python
# Use test mode keys
stripe.api_key = "sk_test_..."

# Test card numbers
TEST_CARDS = {
    'success': '4242424242424242',
    'declined': '4000000000000002',
    '3d_secure': '4000002500003155',
    'insufficient_funds': '4000000000009995'
}

def test_payment_flow():
    """Test complete payment flow."""
    # Create test customer
    customer = stripe.Customer.create(
        email="test@example.com"
    )

    # Create payment intent
    intent = stripe.PaymentIntent.create(
        amount=1000,
        currency='usd',
        customer=customer.id,
        payment_method_types=['card']
    )

    # Confirm with test card
    confirmed = stripe.PaymentIntent.confirm(
        intent.id,
        payment_method='pm_card_visa'  # Test payment method
    )

    assert confirmed.status == 'succeeded'
```

## Resources

- **references/checkout-flows.md**: Detailed checkout implementation
- **references/webhook-handling.md**: Webhook security and processing
- **references/subscription-management.md**: Subscription lifecycle
- **references/customer-management.md**: Customer and payment method handling
- **references/invoice-generation.md**: Invoicing and billing
- **assets/stripe-client.py**: Production-ready Stripe client wrapper
- **assets/webhook-handler.py**: Complete webhook processor
- **assets/checkout-config.json**: Checkout configuration templates

## Best Practices

1. **Always Use Webhooks**: Don't rely solely on client-side confirmation
2. **Idempotency**: Handle webhook events idempotently
3. **Error Handling**: Gracefully handle all Stripe errors
4. **Test Mode**: Thoroughly test with test keys before production
5. **Metadata**: Use metadata to link Stripe objects to your database
6. **Monitoring**: Track payment success rates and errors
7. **PCI Compliance**: Never handle raw card data on your server
8. **SCA Ready**: Implement 3D Secure for European payments

## Common Pitfalls

- **Not Verifying Webhooks**: Always verify webhook signatures
- **Missing Webhook Events**: Handle all relevant webhook events
- **Hardcoded Amounts**: Use cents/smallest currency unit
- **No Retry Logic**: Implement retries for API calls
- **Ignoring Test Mode**: Test all edge cases with test cards

## Imported Module: Upgrading Expo
---
name: upgrading-expo
description: "Upgrade Expo SDK versions"
risk: safe
source: "https://github.com/expo/skills/tree/main/plugins/upgrading-expo"
date_added: "2026-02-27"
---

# Upgrading Expo

## Overview

Upgrade Expo SDK versions safely, handling breaking changes, dependencies, and configuration updates.

## When to Use This Skill

Use this skill when you need to upgrade Expo SDK versions.

Use this skill when:
- Upgrading to a new Expo SDK version
- Handling breaking changes between SDK versions
- Updating dependencies for compatibility
- Migrating deprecated APIs to new versions
- Preparing apps for new Expo features

## Instructions

This skill guides you through upgrading Expo SDK versions:

1. **Pre-Upgrade Planning**: Review release notes and breaking changes
2. **Dependency Updates**: Update packages for SDK compatibility
3. **Configuration Migration**: Update app.json and configuration files
4. **Code Updates**: Migrate deprecated APIs to new versions
5. **Testing**: Verify app functionality after upgrade

## Upgrade Process

### 1. Pre-Upgrade Checklist

- Review Expo SDK release notes
- Identify breaking changes affecting your app
- Check compatibility of third-party packages
- Backup current project state
- Create a feature branch for the upgrade

### 2. Update Expo SDK

```bash
# Update Expo CLI
npm install -g expo-cli@latest

# Upgrade Expo SDK
npx expo install expo@latest

# Update all Expo packages
npx expo install --fix
```

### 3. Handle Breaking Changes

- Review migration guides for breaking changes
- Update deprecated API calls
- Modify configuration files as needed
- Update native dependencies if required
- Test affected features thoroughly

### 4. Update Dependencies

```bash
# Check for outdated packages
npx expo-doctor

# Update packages to compatible versions
npx expo install --fix

# Verify compatibility
npx expo-doctor
```

### 5. Testing

- Test core app functionality
- Verify native modules work correctly
- Check for runtime errors
- Test on both iOS and Android
- Verify app store builds still work

## Common Issues

### Dependency Conflicts

- Use `expo install` instead of `npm install` for Expo packages
- Check package compatibility with new SDK version
- Resolve peer dependency warnings

### Configuration Changes

- Update `app.json` for new SDK requirements
- Migrate deprecated configuration options
- Update native configuration files if needed

### Breaking API Changes

- Review API migration guides
- Update code to use new APIs
- Test affected features after changes

## Best Practices

- Always upgrade in a feature branch
- Test thoroughly before merging
- Review release notes carefully
- Update dependencies incrementally
- Keep Expo CLI updated
- Use `expo-doctor` to verify setup

## Resources

For more information, see the [source repository](https://github.com/expo/skills/tree/main/plugins/upgrading-expo).

## Imported Module: Using Neon
---
name: using-neon
description: "Guides and best practices for working with Neon Serverless Postgres. Covers getting started, local development with Neon, choosing a connection method, Neon features, authentication (@neondatabase/..."
risk: safe
source: "https://github.com/neondatabase/agent-skills/tree/main/skills/neon-postgres"
date_added: "2026-02-27"
---

# Neon Serverless Postgres

Neon is a serverless Postgres platform that separates compute and storage to offer autoscaling, branching, instant restore, and scale-to-zero. It's fully compatible with Postgres and works with any language, framework, or ORM that supports Postgres.

## When to Use This Skill

Use this skill when:
- Working with Neon Serverless Postgres
- Setting up Neon databases
- Choosing connection methods for Neon
- Using Neon features like branching or autoscaling
- Working with Neon authentication or APIs
- Questions about Neon best practices

## Neon Documentation

Always reference the Neon documentation before making Neon-related claims. The documentation is the source of truth for all Neon-related information.

Below you'll find a list of resources organized by area of concern. This is meant to support you find the right documentation pages to fetch and add a bit of additonal context.

You can use the `curl` commands to fetch the documentation page as markdown:

**Documentation:**

```bash
# Get list of all Neon docs
curl https://neon.com/llms.txt

# Fetch any doc page as markdown
curl -H "Accept: text/markdown" https://neon.com/docs/<path>
```

Don't guess docs pages. Use the `llms.txt` index to find the relevant URL or follow the links in the resources below.

## Overview of Resources

Reference the appropriate resource file based on the user's needs:

### Core Guides

| Area               | Resource                           | When to Use                                                    |
| ------------------ | ---------------------------------- | -------------------------------------------------------------- |
| What is Neon       | `references/what-is-neon.md`       | Understanding Neon concepts, architecture, core resources      |
| Referencing Docs   | `references/referencing-docs.md`   | Looking up official documentation, verifying information       |
| Features           | `references/features.md`           | Branching, autoscaling, scale-to-zero, instant restore         |
| Getting Started    | `references/getting-started.md`    | Setting up a project, connection strings, dependencies, schema |
| Connection Methods | `references/connection-methods.md` | Choosing drivers based on platform and runtime                 |
| Developer Tools    | `references/devtools.md`           | VSCode extension, MCP server, Neon CLI (`neon init`)           |

### Database Drivers & ORMs

HTTP/WebSocket queries for serverless/edge functions.

| Area              | Resource                        | When to Use                                         |
| ----------------- | ------------------------------- | --------------------------------------------------- |
| Serverless Driver | `references/neon-serverless.md` | `@neondatabase/serverless` - HTTP/WebSocket queries |
| Drizzle ORM       | `references/neon-drizzle.md`    | Drizzle ORM integration with Neon                   |

### Auth & Data API SDKs

Authentication and PostgREST-style data API for Neon.

| Area        | Resource                  | When to Use                                                         |
| ----------- | ------------------------- | ------------------------------------------------------------------- |
| Neon Auth   | `references/neon-auth.md` | `@neondatabase/auth` - Authentication only                          |
| Neon JS SDK | `references/neon-js.md`   | `@neondatabase/neon-js` - Auth + Data API (PostgREST-style queries) |

### Neon Platform API & CLI

Managing Neon resources programmatically via REST API, SDKs, or CLI.

| Area                  | Resource                            | When to Use                                  |
| --------------------- | ----------------------------------- | -------------------------------------------- |
| Platform API Overview | `references/neon-platform-api.md`   | Managing Neon resources via REST API         |
| Neon CLI              | `references/neon-cli.md`            | Terminal workflows, scripts, CI/CD pipelines |
| TypeScript SDK        | `references/neon-typescript-sdk.md` | `@neondatabase/api-client`                   |
| Python SDK            | `references/neon-python-sdk.md`     | `neon-api` package                           |

## Imported Module: Vercel Ai Sdk Expert
---
name: vercel-ai-sdk-expert
description: "Expert in the Vercel AI SDK. Covers Core API (generateText, streamText), UI hooks (useChat, useCompletion), tool calling, and streaming UI components with React and Next.js."
risk: safe
source: community
date_added: "2026-03-06"
---

# Vercel AI SDK Expert

You are a production-grade Vercel AI SDK expert. You help developers build AI-powered applications, chatbots, and generative UI experiences primarily using Next.js and React. You are an expert in both the `ai` (AI SDK Core) and `@ai-sdk/react` (AI SDK UI) packages. You understand streaming, language model integration, system prompts, tool calling (function calling), and structured data generation.

## When to Use This Skill

- Use when adding AI chat or text generation features to a React or Next.js app
- Use when streaming LLM responses to a frontend UI
- Use when implementing tool calling / function calling with an LLM
- Use when returning structured data (JSON) from an LLM using `generateObject`
- Use when building AI-powered generative UIs (streaming React components)
- Use when migrating from direct OpenAI/Anthropic API calls to the unified AI SDK
- Use when troubleshooting streaming issues with `useChat` or `streamText`

## Core Concepts

### Why Vercel AI SDK?

The Vercel AI SDK is a unified framework that abstracts away provider-specific APIs (OpenAI, Anthropic, Google Gemini, Mistral). It provides two main layers:
1. **AI SDK Core (`ai`)**: Server-side functions to interact with LLMs (`generateText`, `streamText`, `generateObject`).
2. **AI SDK UI (`@ai-sdk/react`)**: Frontend hooks to manage chat state and streaming (`useChat`, `useCompletion`).

## Server-Side Generation (Core API)

### Basic Text Generation

```typescript
import { generateText } from "ai";
import { openai } from "@ai-sdk/openai";

// Returns the full string once completion is done (no streaming)
const { text, usage } = await generateText({
  model: openai("gpt-4o"),
  system: "You are a helpful assistant evaluating code.",
  prompt: "Review the following python code...",
});

console.log(text);
console.log(`Tokens used: ${usage.totalTokens}`);
```

### Streaming Text

```typescript
// app/api/chat/route.ts (Next.js App Router API Route)
import { streamText } from 'ai';
import { openai } from '@ai-sdk/openai';

// Allow streaming responses up to 30 seconds
export const maxDuration = 30;

export async function POST(req: Request) {
  const { messages } = await req.json();

  const result = streamText({
    model: openai('gpt-4o'),
    system: 'You are a friendly customer support bot.',
    messages,
  });

  // Automatically converts the stream to a readable web stream
  return result.toDataStreamResponse();
}
```

### Structured Data (JSON) Generation

```typescript
import { generateObject } from 'ai';
import { openai } from '@ai-sdk/openai';
import { z } from 'zod';

const { object } = await generateObject({
  model: openai('gpt-4o-2024-08-06'), // Use models good at structured output
  system: 'Extract information from the receipt text.',
  prompt: receiptText,
  // Pass a Zod schema to enforce output structure
  schema: z.object({
    storeName: z.string(),
    totalAmount: z.number(),
    items: z.array(z.object({
      name: z.string(),
      price: z.number(),
    })),
    date: z.string().describe("ISO 8601 date format"),
  }),
});

// `object` is automatically fully typed according to the Zod schema!
console.log(object.totalAmount); 
```

## Frontend UI Hooks

### `useChat` (Conversational UI)

```tsx
// app/page.tsx (Next.js Client Component)
"use client";

import { useChat } from "ai/react";

export default function Chat() {
  const { messages, input, handleInputChange, handleSubmit, isLoading } = useChat({
    api: "/api/chat", // Points to the streamText route created above
    // Optional callbacks
    onFinish: (message) => console.log("Done streaming:", message),
    onError: (error) => console.error(error)
  });

  return (
    <div className="flex flex-col h-screen max-w-md mx-auto p-4">
      <div className="flex-1 overflow-y-auto mb-4">
        {messages.map((m) => (
          <div key={m.id} className={`mb-4 ${m.role === 'user' ? 'text-right' : 'text-left'}`}>
            <span className={`p-2 rounded-lg inline-block ${m.role === 'user' ? 'bg-blue-500 text-white' : 'bg-gray-200'}`}>
              {m.target || m.content}
            </span>
          </div>
        ))}
      </div>
      
      <form onSubmit={handleSubmit} className="flex gap-2">
        <input
          value={input}
          onChange={handleInputChange}
          placeholder="Say something..."
          className="flex-1 p-2 border rounded"
          disabled={isLoading}
        />
        <button type="submit" disabled={isLoading} className="bg-black text-white p-2 rounded">
          Send
        </button>
      </form>
    </div>
  );
}
```

## Tool Calling (Function Calling)

Tools allow the LLM to interact with your code, fetching external data or performing actions before responding to the user.

### Server-Side Tool Definition

```typescript
// app/api/chat/route.ts
import { streamText, tool } from 'ai';
import { openai } from '@ai-sdk/openai';
import { z } from 'zod';

export async function POST(req: Request) {
  const { messages } = await req.json();

  const result = streamText({
    model: openai('gpt-4o'),
    messages,
    tools: {
      getWeather: tool({
        description: 'Get the current weather in a given location',
        parameters: z.object({
          location: z.string().describe('The city and state, e.g. San Francisco, CA'),
          unit: z.enum(['celsius', 'fahrenheit']).optional(),
        }),
        // Execute runs when the LLM decides to call this tool
        execute: async ({ location, unit = 'celsius' }) => {
          // Fetch from your actual weather API or database
          const temp = location.includes("San Francisco") ? 15 : 22;
          return `The weather in ${location} is ${temp}° ${unit}.`;
        },
      }),
    },
    // Allows the LLM to call tools automatically in a loop until it has the answer
    maxSteps: 5, 
  });

  return result.toDataStreamResponse();
}
```

### UI for Multi-Step Tool Calls

When using `maxSteps`, the `useChat` hook will display intermediate tool calls if you handle them in the UI.

```tsx
// Inside the `useChat` messages.map loop
{m.role === 'assistant' && m.toolInvocations?.map((toolInvocation) => (
  <div key={toolInvocation.toolCallId} className="text-sm text-gray-500">
    {toolInvocation.state === 'result' ? (
      <p>✅ Fetched weather for {toolInvocation.args.location}</p>
    ) : (
      <p>⏳ Fetching weather for {toolInvocation.args.location}...</p>
    )}
  </div>
))}
```

## Best Practices

- ✅ **Do:** Use `openai('gpt-4o')` or `anthropic('example-model')` format (from specific provider packages like `@ai-sdk/openai`) instead of the older edge runtime wrappers.
- ✅ **Do:** Provide a strict Zod `schema` and a clear `system` prompt when using `generateObject()`.
- ✅ **Do:** Set `maxDuration = 30` (or higher if on Pro) in Next.js API routes that use `streamText`, as LLMs take time to stream responses and Vercel's default is 10-15s.
- ✅ **Do:** Use `tool()` with comprehensive `description` tags on Zod parameters, as the LLM relies entirely on those strings to understand when and how to call the tool.
- ✅ **Do:** Enable `maxSteps: 5` (or similar) when providing tools, otherwise the LLM won't be able to reply to the user *after* seeing the tool result!
- ❌ **Don't:** Forget to return `result.toDataStreamResponse()` in Next.js App Router API routes when using `streamText`; standard JSON responses will break chunking.
- ❌ **Don't:** Blindly trust the output of `generateObject` without validation, even though Zod forces the shape — always handle failure states using `try/catch`.

## Troubleshooting

**Problem:** The streaming chat cuts off abruptly after 10-15 seconds.
**Solution:** The serverless function timed out. Add `export const maxDuration = 30;` (or whatever your plan limit is) to the Next.js API route file.

**Problem:** "Tool execution failed" or the LLM didn't return an answer after using a tool.
**Solution:** `streamText` stops immediately after a tool call completes unless you provide `maxSteps`. Set `maxSteps: 2` (or higher) to let the LLM see the tool result and construct a final text response.

## Imported Module: Wordpress
---
name: wordpress
description: "Complete WordPress development workflow covering theme development, plugin creation, WooCommerce integration, performance optimization, and security hardening."
category: workflow-bundle
risk: safe
source: personal
date_added: "2026-02-27"
---

# WordPress Development Workflow Bundle

## Overview

Comprehensive WordPress development workflow covering theme development, plugin creation, WooCommerce integration, performance optimization, and security. This bundle orchestrates skills for building production-ready WordPress sites and applications.

## When to Use This Workflow

Use this workflow when:
- Building new WordPress websites
- Creating custom themes
- Developing WordPress plugins
- Setting up WooCommerce stores
- Optimizing WordPress performance
- Hardening WordPress security

## Workflow Phases

### Phase 1: WordPress Setup

#### Skills to Invoke
- `app-builder` - Project scaffolding
- `environment-setup-guide` - Development environment

#### Actions
1. Set up local development environment (LocalWP, Docker, or Valet)
2. Install WordPress
3. Configure development database
4. Set up version control
5. Configure wp-config.php for development

#### Copy-Paste Prompts
```
Use @app-builder to scaffold a new WordPress project with modern tooling
```

### Phase 2: Theme Development

#### Skills to Invoke
- `frontend-developer` - Component development
- `frontend-design` - UI implementation
- `tailwind-patterns` - Styling
- `web-performance-optimization` - Performance

#### Actions
1. Design theme architecture
2. Create theme files (style.css, functions.php, index.php)
3. Implement template hierarchy
4. Create custom page templates
5. Add custom post types and taxonomies
6. Implement theme customization options
7. Add responsive design

#### Theme Structure
```
theme-name/
├── style.css
├── functions.php
├── index.php
├── header.php
├── footer.php
├── sidebar.php
├── single.php
├── page.php
├── archive.php
├── search.php
├── 404.php
├── template-parts/
├── inc/
├── assets/
│   ├── css/
│   ├── js/
│   └── images/
└── languages/
```

#### Copy-Paste Prompts
```
Use @frontend-developer to create a custom WordPress theme with React components
```

```
Use @tailwind-patterns to style WordPress theme with modern CSS
```

### Phase 3: Plugin Development

#### Skills to Invoke
- `backend-dev-guidelines` - Backend standards
- `api-design-principles` - API design
- `auth-implementation-patterns` - Authentication

#### Actions
1. Design plugin architecture
2. Create plugin boilerplate
3. Implement hooks (actions and filters)
4. Create admin interfaces
5. Add custom database tables
6. Implement REST API endpoints
7. Add settings and options pages

#### Plugin Structure
```
plugin-name/
├── plugin-name.php
├── includes/
│   ├── class-plugin-activator.php
│   ├── class-plugin-deactivator.php
│   ├── class-plugin-loader.php
│   └── class-plugin.php
├── admin/
│   ├── class-plugin-admin.php
│   ├── css/
│   └── js/
├── public/
│   ├── class-plugin-public.php
│   ├── css/
│   └── js/
└── languages/
```

#### Copy-Paste Prompts
```
Use @backend-dev-guidelines to create a WordPress plugin with proper architecture
```

### Phase 4: WooCommerce Integration

#### Skills to Invoke
- `payment-integration` - Payment processing
- `stripe-integration` - Stripe payments
- `billing-automation` - Billing workflows

#### Actions
1. Install and configure WooCommerce
2. Create custom product types
3. Customize checkout flow
4. Integrate payment gateways
5. Set up shipping methods
6. Create custom order statuses
7. Implement subscription products
8. Add custom email templates

#### Copy-Paste Prompts
```
Use @payment-integration to set up WooCommerce with Stripe
```

```
Use @billing-automation to create subscription products in WooCommerce
```

### Phase 5: Performance Optimization

#### Skills to Invoke
- `web-performance-optimization` - Performance optimization
- `database-optimizer` - Database optimization

#### Actions
1. Implement caching (object, page, browser)
2. Optimize images (lazy loading, WebP)
3. Minify and combine assets
4. Enable CDN
5. Optimize database queries
6. Implement lazy loading
7. Configure OPcache
8. Set up Redis/Memcached

#### Performance Checklist
- [ ] Page load time < 3 seconds
- [ ] Time to First Byte < 200ms
- [ ] Largest Contentful Paint < 2.5s
- [ ] Cumulative Layout Shift < 0.1
- [ ] First Input Delay < 100ms

#### Copy-Paste Prompts
```
Use @web-performance-optimization to audit and improve WordPress performance
```

### Phase 6: Security Hardening

#### Skills to Invoke
- `security-auditor` - Security audit
- `wordpress-penetration-testing` - WordPress security testing
- `sast-configuration` - Static analysis

#### Actions
1. Update WordPress core, themes, plugins
2. Implement security headers
3. Configure file permissions
4. Set up firewall rules
5. Enable two-factor authentication
6. Implement rate limiting
7. Configure security logging
8. Set up malware scanning

#### Security Checklist
- [ ] WordPress core updated
- [ ] All plugins/themes updated
- [ ] Strong passwords enforced
- [ ] Two-factor authentication enabled
- [ ] Security headers configured
- [ ] XML-RPC disabled or protected
- [ ] File editing disabled
- [ ] Database prefix changed
- [ ] Regular backups configured

#### Copy-Paste Prompts
```
Use @wordpress-penetration-testing to audit WordPress security
```

```
Use @security-auditor to perform comprehensive security review
```

### Phase 7: Testing

#### Skills to Invoke
- `test-automator` - Test automation
- `playwright-skill` - E2E testing
- `webapp-testing` - Web app testing

#### Actions
1. Write unit tests for custom code
2. Create integration tests
3. Set up E2E tests
4. Test cross-browser compatibility
5. Test responsive design
6. Performance testing
7. Security testing

#### Copy-Paste Prompts
```
Use @playwright-skill to create E2E tests for WordPress site
```

### Phase 8: Deployment

#### Skills to Invoke
- `deployment-engineer` - Deployment
- `cicd-automation-workflow-automate` - CI/CD
- `github-actions-templates` - GitHub Actions

#### Actions
1. Set up staging environment
2. Configure deployment pipeline
3. Set up database migrations
4. Configure environment variables
5. Enable maintenance mode during deployment
6. Deploy to production
7. Verify deployment
8. Monitor post-deployment

#### Copy-Paste Prompts
```
Use @deployment-engineer to set up WordPress deployment pipeline
```

## WordPress-Specific Workflows

### Custom Post Type Development
```php
register_post_type('book', [
    'labels' => [...],
    'public' => true,
    'has_archive' => true,
    'supports' => ['title', 'editor', 'thumbnail', 'excerpt'],
    'menu_icon' => 'dashicons-book',
]);
```

### Custom REST API Endpoint
```php
add_action('rest_api_init', function() {
    register_rest_route('myplugin/v1', '/books', [
        'methods' => 'GET',
        'callback' => 'get_books',
        'permission_callback' => '__return_true',
    ]);
});
```

### WooCommerce Custom Product Type
```php
add_action('init', function() {
    class WC_Product_Custom extends WC_Product {
        // Custom product implementation
    }
});
```

## Quality Gates

Before moving to next phase, verify:
- [ ] All custom code tested
- [ ] Security scan passed
- [ ] Performance targets met
- [ ] Cross-browser tested
- [ ] Mobile responsive verified
- [ ] Accessibility checked (WCAG 2.1)

## Related Workflow Bundles

- `development` - General web development
- `security-audit` - Security testing
- `testing-qa` - Testing workflow
- `ecommerce` - E-commerce development

## Imported Module: Wordpress Plugin Development
---
name: wordpress-plugin-development
description: "WordPress plugin development workflow covering plugin architecture, hooks, admin interfaces, REST API, and security best practices."
category: granular-workflow-bundle
risk: safe
source: personal
date_added: "2026-02-27"
---

# WordPress Plugin Development Workflow

## Overview

Specialized workflow for creating WordPress plugins with proper architecture, hooks system, admin interfaces, REST API endpoints, and security practices.

## When to Use This Workflow

Use this workflow when:
- Creating custom WordPress plugins
- Extending WordPress functionality
- Building admin interfaces
- Adding REST API endpoints
- Integrating third-party services

## Workflow Phases

### Phase 1: Plugin Setup

#### Skills to Invoke
- `app-builder` - Project scaffolding
- `backend-dev-guidelines` - Backend patterns

#### Actions
1. Create plugin directory structure
2. Set up main plugin file with header
3. Implement activation/deactivation hooks
4. Set up autoloading
5. Configure text domain

#### Copy-Paste Prompts
```
Use @app-builder to scaffold a new WordPress plugin
```

### Phase 2: Plugin Architecture

#### Skills to Invoke
- `backend-dev-guidelines` - Architecture patterns

#### Actions
1. Design plugin class structure
2. Implement singleton pattern
3. Create loader class
4. Set up dependency injection
5. Configure plugin lifecycle

#### Copy-Paste Prompts
```
Use @backend-dev-guidelines to design plugin architecture
```

### Phase 3: Hooks Implementation

#### Skills to Invoke
- `wordpress-penetration-testing` - WordPress patterns

#### Actions
1. Register action hooks
2. Create filter hooks
3. Implement callback functions
4. Set up hook priorities
5. Add conditional hooks

#### Copy-Paste Prompts
```
Use @wordpress-penetration-testing to understand WordPress hooks
```

### Phase 4: Admin Interface

#### Skills to Invoke
- `frontend-developer` - Admin UI

#### Actions
1. Create admin menu
2. Build settings pages
3. Implement options registration
4. Add settings sections/fields
5. Create admin notices

#### Copy-Paste Prompts
```
Use @frontend-developer to create WordPress admin interface
```

### Phase 5: Database Operations

#### Skills to Invoke
- `database-design` - Database design
- `postgresql` - Database patterns

#### Actions
1. Create custom tables
2. Implement CRUD operations
3. Add data validation
4. Set up data sanitization
5. Create data upgrade routines

#### Copy-Paste Prompts
```
Use @database-design to design plugin database schema
```

### Phase 6: REST API

#### Skills to Invoke
- `api-design-principles` - API design
- `api-patterns` - API patterns

#### Actions
1. Register REST routes
2. Create endpoint callbacks
3. Implement permission callbacks
4. Add request validation
5. Document API endpoints

#### Copy-Paste Prompts
```
Use @api-design-principles to create WordPress REST API endpoints
```

### Phase 7: Security

#### Skills to Invoke
- `wordpress-penetration-testing` - WordPress security
- `security-scanning-security-sast` - Security scanning

#### Actions
1. Implement nonce verification
2. Add capability checks
3. Sanitize all inputs
4. Escape all outputs
5. Secure database queries

#### Copy-Paste Prompts
```
Use @wordpress-penetration-testing to audit plugin security
```

### Phase 8: Testing

#### Skills to Invoke
- `test-automator` - Test automation
- `php-pro` - PHP testing

#### Actions
1. Set up PHPUnit
2. Create unit tests
3. Write integration tests
4. Test with WordPress test suite
5. Configure CI

#### Copy-Paste Prompts
```
Use @test-automator to set up plugin testing
```

## Plugin Structure

```
plugin-name/
├── plugin-name.php
├── includes/
│   ├── class-plugin.php
│   ├── class-loader.php
│   ├── class-activator.php
│   └── class-deactivator.php
├── admin/
│   ├── class-plugin-admin.php
│   ├── css/
│   └── js/
├── public/
│   ├── class-plugin-public.php
│   ├── css/
│   └── js/
├── languages/
└── vendor/
```

## Quality Gates

- [ ] Plugin activates without errors
- [ ] All hooks working
- [ ] Admin interface functional
- [ ] Security measures implemented
- [ ] Tests passing
- [ ] Documentation complete

## Related Workflow Bundles

- `wordpress` - WordPress development
- `wordpress-theme-development` - Theme development
- `wordpress-woocommerce` - WooCommerce

## Imported Module: Wordpress Theme Development
---
name: wordpress-theme-development
description: "WordPress theme development workflow covering theme architecture, template hierarchy, custom post types, block editor support, and responsive design."
category: granular-workflow-bundle
risk: safe
source: personal
date_added: "2026-02-27"
---

# WordPress Theme Development Workflow

## Overview

Specialized workflow for creating custom WordPress themes from scratch, including modern block editor (Gutenberg) support, template hierarchy, and responsive design.

## When to Use This Workflow

Use this workflow when:
- Creating custom WordPress themes
- Converting designs to WordPress themes
- Adding block editor support
- Implementing custom post types
- Building child themes

## Workflow Phases

### Phase 1: Theme Setup

#### Skills to Invoke
- `app-builder` - Project scaffolding
- `frontend-developer` - Frontend development

#### Actions
1. Create theme directory structure
2. Set up style.css with theme header
3. Create functions.php
4. Configure theme support
5. Set up enqueue scripts/styles

#### Copy-Paste Prompts
```
Use @app-builder to scaffold a new WordPress theme project
```

### Phase 2: Template Hierarchy

#### Skills to Invoke
- `frontend-developer` - Template development

#### Actions
1. Create index.php (fallback template)
2. Implement header.php and footer.php
3. Create single.php for posts
4. Create page.php for pages
5. Add archive.php for archives
6. Implement search.php and 404.php

#### Copy-Paste Prompts
```
Use @frontend-developer to create WordPress template files
```

### Phase 3: Theme Functions

#### Skills to Invoke
- `backend-dev-guidelines` - Backend patterns

#### Actions
1. Register navigation menus
2. Add theme support (thumbnails, RSS, etc.)
3. Register widget areas
4. Create custom template tags
5. Implement helper functions

#### Copy-Paste Prompts
```
Use @backend-dev-guidelines to create theme functions
```

### Phase 4: Custom Post Types

#### Skills to Invoke
- `wordpress-penetration-testing` - WordPress patterns

#### Actions
1. Register custom post types
2. Create custom taxonomies
3. Add custom meta boxes
4. Implement custom fields
5. Create archive templates

#### Copy-Paste Prompts
```
Use @wordpress-penetration-testing to understand WordPress CPT patterns
```

### Phase 5: Block Editor Support

#### Skills to Invoke
- `frontend-developer` - Block development

#### Actions
1. Enable block editor support
2. Register custom blocks
3. Create block styles
4. Add block patterns
5. Configure block templates

#### Copy-Paste Prompts
```
Use @frontend-developer to create custom Gutenberg blocks
```

### Phase 6: Styling and Design

#### Skills to Invoke
- `frontend-design` - UI design
- `tailwind-patterns` - Tailwind CSS

#### Actions
1. Implement responsive design
2. Add CSS framework or custom styles
3. Create design system
4. Implement theme customizer
5. Add accessibility features

#### Copy-Paste Prompts
```
Use @frontend-design to create responsive theme design
```

### Phase 7: Testing

#### Skills to Invoke
- `playwright-skill` - Browser testing
- `webapp-testing` - Web app testing

#### Actions
1. Test across browsers
2. Verify responsive breakpoints
3. Test block editor
4. Check accessibility
5. Performance testing

#### Copy-Paste Prompts
```
Use @playwright-skill to test WordPress theme
```

## Theme Structure

```
theme-name/
├── style.css
├── functions.php
├── index.php
├── header.php
├── footer.php
├── sidebar.php
├── single.php
├── page.php
├── archive.php
├── search.php
├── 404.php
├── comments.php
├── template-parts/
├── inc/
├── assets/
│   ├── css/
│   ├── js/
│   └── images/
└── languages/
```

## Quality Gates

- [ ] All templates working
- [ ] Block editor supported
- [ ] Responsive design verified
- [ ] Accessibility checked
- [ ] Performance optimized
- [ ] Cross-browser tested

## Related Workflow Bundles

- `wordpress` - WordPress development
- `wordpress-plugin-development` - Plugin development
- `wordpress-woocommerce` - WooCommerce

