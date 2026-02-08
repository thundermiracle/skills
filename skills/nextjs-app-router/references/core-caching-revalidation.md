# Caching & Revalidation

Summary
- `fetch` is not cached by default in the App Router.
- Use `cache: "force-cache"` to cache a request or `next: { revalidate: seconds }` to revalidate on a timer.
- Use `next: { tags: [...] }` to tag cached data and invalidate it with `revalidateTag`.

Example

// Cached and revalidated every 60 seconds
await fetch('https://example.com/api/products', {
  next: { revalidate: 60, tags: ['products'] },
})

// On-demand revalidation
import { revalidateTag } from 'next/cache'

export async function POST() {
  revalidateTag('products')
  return new Response('ok')
}
