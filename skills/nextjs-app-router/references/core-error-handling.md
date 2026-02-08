# Error Handling

Summary
- `error.js|ts` is a Client Component that catches errors in a route segment and receives `error` and `reset` props.
- Use `not-found.js|ts` to render 404 UI when calling `notFound()`.
- Use `global-error.js|ts` or `global-not-found.js|ts` for root-level handling.

Example

app/dashboard/error.tsx

"use client"

export default function Error({ error, reset }: { error: Error; reset: () => void }) {
  return (
    <div>
      <p>Something went wrong: {error.message}</p>
      <button onClick={() => reset()}>Try again</button>
    </div>
  )
}

app/posts/page.tsx

import { notFound } from 'next/navigation'

export default async function PostPage() {
  const post = await fetch('https://example.com/api/post/1').then(r => r.json())
  if (!post) notFound()
  return <h1>{post.title}</h1>
}
