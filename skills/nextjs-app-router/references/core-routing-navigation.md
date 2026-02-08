# Routing & Navigation

Summary
- Routes are rendered on the server by default in the App Router.
- Use `Link` for client-side navigation and prefetching.
- Use `loading.tsx` to enable streaming and show loading UI for a segment.

Example

import Link from 'next/link'

export default function Nav() {
  return (
    <nav>
      <Link href="/dashboard">Dashboard</Link>
    </nav>
  )
}

app/dashboard/loading.tsx

export default function Loading() {
  return <p>Loading...</p>
}
