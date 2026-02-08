# Server & Client Components

Summary
- `page` and `layout` are Server Components by default.
- Use Client Components for state, effects, event handlers, and browser-only APIs by adding the `"use client"` directive.
- Keep data access, secrets, and heavy work on the server to reduce client-side JavaScript.

Example

app/posts/page.tsx (Server Component)

import LikeButton from './like-button'

export default async function PostsPage() {
  const posts = await fetch('https://example.com/api/posts').then(r => r.json())
  return (
    <ul>
      {posts.map((post: any) => (
        <li key={post.id}>
          {post.title}
          <LikeButton id={post.id} />
        </li>
      ))}
    </ul>
  )
}

app/posts/like-button.tsx (Client Component)

"use client"

export default function LikeButton({ id }: { id: string }) {
  return <button aria-label={`like-${id}`}>Like</button>
}
