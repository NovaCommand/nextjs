# 🚀 Learning Next.js — A Complete Guide

> A structured, beginner-to-advanced roadmap for mastering Next.js — the React framework for production.

---

## 📚 Table of Contents

1. [What is Next.js?](#what-is-nextjs)
2. [Prerequisites](#prerequisites)
3. [Getting Started](#getting-started)
4. [Core Concepts](#core-concepts)
5. [Routing](#routing)
6. [Data Fetching](#data-fetching)
7. [Styling](#styling)
8. [API Routes](#api-routes)
9. [Optimization](#optimization)
10. [Deployment](#deployment)
11. [Learning Resources](#learning-resources)
12. [Project Ideas](#project-ideas)
13. [Roadmap](#roadmap)

---

## What is Next.js?

**Next.js** is an open-source React framework built by [Vercel](https://vercel.com) that enables features like:

- ⚡ **Server-Side Rendering (SSR)** — Pages are rendered on the server per request
- 🏗️ **Static Site Generation (SSG)** — Pages are pre-built at compile time
- 🔀 **Incremental Static Regeneration (ISR)** — Update static pages without a full rebuild
- 📁 **File-Based Routing** — No need for a separate router library
- 🛠️ **API Routes** — Build backend endpoints within the same project
- 🖼️ **Image Optimization** — Automatic image resizing and lazy loading
- 🔍 **SEO-Friendly** — Full control over `<head>` metadata per page

---

## Prerequisites

Before diving into Next.js, make sure you're comfortable with:

| Technology | Why You Need It |
|---|---|
| **HTML & CSS** | Fundamental web building blocks |
| **JavaScript (ES6+)** | Arrow functions, destructuring, async/await, modules |
| **React** | Components, props, state, hooks (`useState`, `useEffect`) |
| **Node.js basics** | Running scripts, npm/yarn/pnpm |

> 💡 **Tip:** If you're new to React, complete the official [React docs](https://react.dev/learn) first (especially the "Getting Started" and "Hooks" sections).

---

## Getting Started

### 1. Create a New Project

```bash
npx create-next-app@latest my-app
cd my-app
npm run dev
```

During setup, you'll be prompted for:
- TypeScript support → recommended: **Yes**
- ESLint → recommended: **Yes**
- Tailwind CSS → optional but popular
- App Router → recommended: **Yes** (modern approach)

### 2. Project Structure (App Router)

```
my-app/
├── app/                  # Main app directory (App Router)
│   ├── layout.tsx        # Root layout (wraps all pages)
│   ├── page.tsx          # Home page (route: /)
│   ├── about/
│   │   └── page.tsx      # About page (route: /about)
│   └── api/
│       └── hello/
│           └── route.ts  # API endpoint (route: /api/hello)
├── public/               # Static assets (images, fonts)
├── components/           # Reusable React components
├── next.config.js        # Next.js configuration
└── package.json
```

### 3. Run the Development Server

```bash
npm run dev       # Start dev server at http://localhost:3000
npm run build     # Create production build
npm run start     # Run production server
npm run lint      # Run ESLint
```

---

## Core Concepts

### Server Components vs. Client Components

Next.js 13+ (App Router) introduced **React Server Components**:

| | Server Component | Client Component |
|---|---|---|
| **Default?** | ✅ Yes | ❌ No (opt-in with `"use client"`) |
| **Runs on** | Server only | Browser (+ server for hydration) |
| **Can use hooks?** | ❌ No | ✅ Yes |
| **Can fetch data?** | ✅ Yes (directly async) | ✅ Yes (via useEffect or libraries) |
| **Best for** | Data fetching, layouts, static content | Interactivity, event handlers, browser APIs |

```tsx
// ✅ Server Component (default) — no "use client" needed
export default async function ProductPage() {
  const products = await fetchProducts(); // async/await directly!
  return <ul>{products.map(p => <li key={p.id}>{p.name}</li>)}</ul>;
}

// ✅ Client Component
"use client";
import { useState } from "react";

export default function Counter() {
  const [count, setCount] = useState(0);
  return <button onClick={() => setCount(count + 1)}>Count: {count}</button>;
}
```

---

## Routing

Next.js uses a **file-system based router**. Files inside `app/` automatically become routes.

### Basic Routes

| File path | Route |
|---|---|
| `app/page.tsx` | `/` |
| `app/about/page.tsx` | `/about` |
| `app/blog/page.tsx` | `/blog` |

### Dynamic Routes

```
app/blog/[slug]/page.tsx   →   /blog/my-first-post
app/shop/[...path]/page.tsx  →  /shop/a/b/c (catch-all)
```

```tsx
// app/blog/[slug]/page.tsx
export default function BlogPost({ params }: { params: { slug: string } }) {
  return <h1>Post: {params.slug}</h1>;
}
```

### Layouts

Layouts wrap multiple pages and persist across navigation:

```tsx
// app/layout.tsx
export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>
        <nav>My Nav</nav>
        {children}
        <footer>My Footer</footer>
      </body>
    </html>
  );
}
```

### Navigation

```tsx
import Link from "next/link";
import { useRouter } from "next/navigation";

// Declarative navigation
<Link href="/about">About</Link>

// Programmatic navigation (Client Component only)
const router = useRouter();
router.push("/dashboard");
```

---

## Data Fetching

### In Server Components (recommended)

```tsx
async function getData() {
  const res = await fetch("https://api.example.com/data", {
    next: { revalidate: 60 }, // ISR: revalidate every 60 seconds
    // cache: "no-store"      // SSR: always fetch fresh data
    // cache: "force-cache"   // SSG: fetch once at build time
  });
  return res.json();
}

export default async function Page() {
  const data = await getData();
  return <main>{JSON.stringify(data)}</main>;
}
```

### Caching Strategies

| Strategy | Option | Use Case |
|---|---|---|
| **Static (SSG)** | `cache: "force-cache"` | Data that rarely changes |
| **Server-Side (SSR)** | `cache: "no-store"` | Always-fresh data (user-specific) |
| **Incremental (ISR)** | `next: { revalidate: N }` | Data that changes occasionally |

---

## Styling

### CSS Modules (built-in)

```css
/* styles/Button.module.css */
.button { background: blue; color: white; padding: 8px 16px; }
```

```tsx
import styles from "./Button.module.css";
<button className={styles.button}>Click me</button>
```

### Tailwind CSS

The most popular choice with Next.js:

```tsx
<button className="bg-blue-500 text-white px-4 py-2 rounded hover:bg-blue-600">
  Click me
</button>
```

### Global Styles

```tsx
// app/layout.tsx
import "./globals.css"; // Import once at the root layout
```

---

## API Routes

Next.js lets you write backend endpoints in the same project using **Route Handlers**:

```ts
// app/api/users/route.ts

export async function GET() {
  const users = await db.getUsers();
  return Response.json(users);
}

export async function POST(request: Request) {
  const body = await request.json();
  const newUser = await db.createUser(body);
  return Response.json(newUser, { status: 201 });
}
```

Access at: `GET /api/users` or `POST /api/users`

---

## Optimization

### Image Component

```tsx
import Image from "next/image";

<Image
  src="/hero.jpg"
  alt="Hero image"
  width={800}
  height={400}
  priority // Load eagerly for above-the-fold images
/>
```

### Font Optimization

```tsx
import { Inter, Playfair_Display } from "next/font/google";

const inter = Inter({ subsets: ["latin"] });
const playfair = Playfair_Display({ subsets: ["latin"], weight: ["400", "700"] });
```

### Metadata (SEO)

```tsx
// app/page.tsx
import type { Metadata } from "next";

export const metadata: Metadata = {
  title: "My App",
  description: "Welcome to my Next.js app",
  openGraph: {
    images: ["/og-image.png"],
  },
};
```

### Lazy Loading

```tsx
import dynamic from "next/dynamic";

// Only loads the component when it's needed
const HeavyChart = dynamic(() => import("./HeavyChart"), {
  loading: () => <p>Loading chart...</p>,
});
```

---

## Deployment

### Deploy to Vercel (Easiest)

1. Push your project to GitHub
2. Go to [vercel.com](https://vercel.com) and import your repo
3. Click **Deploy** — that's it! ✅

Vercel automatically handles builds, previews per branch, and environment variables.

### Environment Variables

```bash
# .env.local (never commit this!)
DATABASE_URL=postgres://...
NEXT_PUBLIC_API_URL=https://api.example.com  # NEXT_PUBLIC_ prefix = exposed to browser
```

```ts
// Usage
const dbUrl = process.env.DATABASE_URL;          // Server only
const apiUrl = process.env.NEXT_PUBLIC_API_URL;  // Server + client
```

### Other Deployment Options

- **Docker** — `next build` → `next start`
- **AWS / GCP / Azure** — via containers or serverless functions
- **Netlify** — similar to Vercel, supports Next.js

---

## Learning Resources

### Official

- 📖 [Next.js Documentation](https://nextjs.org/docs) — Start here, it's excellent
- 🎓 [Next.js Learn Course](https://nextjs.org/learn) — Free interactive tutorial (best starting point)
- 🎬 [Next.js YouTube Channel](https://www.youtube.com/@nextjs) — Official video content

### Community & Courses

- 🎥 [Fireship — Next.js in 100 Seconds](https://www.youtube.com/watch?v=Sklc_fQBmcs)
- 🎥 [Traversy Media — Next.js Crash Course](https://www.youtube.com/watch?v=mTz0GXj8NN0)
- 📚 [Josh tried coding — Next.js tutorials](https://www.youtube.com/@joshtriedcoding)

### Books

- *"Real-World Next.js"* by Michele Riva
- *"Full-Stack Next.js"* by Cassidy Williams

---

## Project Ideas

Build these to solidify your skills (in order of difficulty):

| Level | Project | Concepts Practiced |
|---|---|---|
| 🟢 Beginner | Personal portfolio site | Static pages, layouts, routing |
| 🟢 Beginner | Blog with MDX | Dynamic routes, markdown rendering |
| 🟡 Intermediate | Weather app | API routes, data fetching, env vars |
| 🟡 Intermediate | E-commerce product listing | SSG, ISR, image optimization |
| 🔴 Advanced | Full-stack todo app | API routes, database (Prisma), auth |
| 🔴 Advanced | Real-time chat | WebSockets, middleware, auth |

---

## Roadmap

Follow this learning path for the best results:

```
Week 1-2:  Core concepts → Routing → Layouts → Navigation
Week 3-4:  Data fetching (SSR/SSG/ISR) → API Routes → Server vs Client components
Week 5-6:  Styling (Tailwind) → Image/Font optimization → Metadata/SEO
Week 7-8:  Authentication (NextAuth.js) → Database (Prisma) → Deployment
Week 9+:   Middleware → Internationalization → Testing → Advanced patterns
```

---

## Common Gotchas

- ⚠️ `"use client"` should be placed at the **top of the file**, before imports
- ⚠️ You **cannot use React hooks** in Server Components
- ⚠️ `useRouter` from `next/router` is for the old Pages Router — use `next/navigation` for App Router
- ⚠️ Environment variables without `NEXT_PUBLIC_` are **server-only** — they won't be available in the browser
- ⚠️ `fetch()` in Next.js is extended with caching options — it's not the same as vanilla `fetch`

---

## Contributing

Found a typo or want to improve this guide? PRs are welcome! Please follow the existing format and keep explanations beginner-friendly.

---

## License

This learning guide is open for personal and educational use. See [LICENSE](./LICENSE) for details.

---

> Made with ❤️ for developers starting their Next.js journey.
> Happy building! 🏗️
