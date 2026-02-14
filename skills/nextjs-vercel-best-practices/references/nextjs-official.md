# Next.js Official Best Practices

Last reviewed: 2026-02-14

## Official Sources

- [Next.js Production Checklist](https://nextjs.org/docs/app/guides/production-checklist)
- [Next.js Deploying](https://nextjs.org/docs/app/getting-started/deploying)
- [Next.js Caching and Revalidating](https://nextjs.org/docs/app/getting-started/caching-and-revalidating)
- [Next.js Server and Client Components](https://nextjs.org/docs/app/getting-started/server-and-client-components)
- [Next.js Lazy Loading](https://nextjs.org/docs/app/guides/lazy-loading)
- [Next.js loading.js (Suspense boundary)](https://nextjs.org/docs/app/api-reference/file-conventions/loading)
- [Next.js useReportWebVitals](https://nextjs.org/docs/app/api-reference/functions/use-report-web-vitals)
- [Next.js Package Bundling](https://nextjs.org/docs/app/guides/package-bundling)
- [How to Think About Security in Next.js](https://nextjs.org/blog/security-nextjs-server-components-actions)

## Performance Targets (Field Data)

Use these as baseline goals in production monitoring:

1. `LCP <= 2.5s`
2. `INP <= 200ms`
3. `CLS <= 0.1`
4. Monitor `TTFB` trend and investigate regressions release-by-release.

## Concrete Performance Workflow

### 1. Measure in production-like conditions first

1. Run local production mode before shipping:

```bash
next build
next start
```

2. Run Lighthouse in an incognito window and compare with field metrics.
3. Add `useReportWebVitals` with a dedicated tiny Client Component (official recommendation for performance boundary isolation):

```tsx
'use client'

import { useReportWebVitals } from 'next/web-vitals'

export function WebVitals() {
  useReportWebVitals((metric) => {
    // send metric to analytics endpoint
  })
  return null
}
```

### 2. Remove avoidable client JavaScript

1. Prefer Server Components by default.
2. Audit `"use client"` boundaries and move them deeper to interactive leaf components.
3. Lazy-load heavy Client Components or libraries with `next/dynamic`:

```tsx
'use client'
import dynamic from 'next/dynamic'

const HeavyWidget = dynamic(() => import('./HeavyWidget'))
```

### 3. Stream and remove data waterfalls

1. Add `loading.tsx` for segments with slow data; it automatically wraps `page` and children in `Suspense`.
2. Fetch in parallel where possible (`Promise.all`) instead of sequential awaits.
3. Do not call Route Handlers from Server Components (avoid extra server hop).

### 4. Apply caching and revalidation intentionally

1. Start with static/cached defaults and make dynamic behavior explicit.
2. Remember dynamic APIs like `cookies` and `searchParams` can opt a route into dynamic rendering.
3. Use tag/time-based invalidation with `fetch` and `revalidateTag`:

```tsx
const res = await fetch('https://api.example.com/products', {
  next: { revalidate: 300, tags: ['products'] },
})
```

```tsx
'use server'
import { revalidateTag } from 'next/cache'

export async function refreshProducts() {
  revalidateTag('products')
}
```

### 5. Optimize assets and script loading

1. Use `next/image` (layout stability + optimized formats).
2. Use `next/font` (hosted fonts, no external font request, reduced layout shift).
3. Use `next/script` for third-party scripts to control loading strategy.

### 6. Analyze and shrink bundles

1. Next.js 16.1+ (Turbopack analyzer):

```bash
pnpm next experimental-analyze
```

2. Webpack analyzer path:

```bash
pnpm add @next/bundle-analyzer
ANALYZE=true pnpm build
```

3. If large “many-export” packages dominate bundle size, evaluate `optimizePackageImports` in `next.config.js`.

### 7. Release gate

1. Fail release if CWV regresses meaningfully in preview or production monitoring.
2. Require `next build` pass and no major bundle outlier after changes.
3. Keep a route-level rollback plan for high-traffic pages.

## Delivery and Validation

1. Run `next build` to catch type/build/runtime boundary issues before deploy.
2. Smoke test with `next start` (or preview deployment) before production promotion.
3. Ensure route-level error boundaries and not-found behavior are present where needed.

## Security (Performance-Adjacent)

1. Keep secrets and sensitive business logic server-side only.
2. Validate and authorize all mutations (especially in Server Actions and route handlers).
3. Prevent sensitive data leakage through serialized props or logs.
