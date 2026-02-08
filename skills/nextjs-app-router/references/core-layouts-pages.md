# Layouts & Pages

Summary
- A `page` file defines the UI for a route segment.
- A `layout` file wraps child routes and is shared across routes; it preserves state and does not re-render on navigation by default.
- Layouts receive a `children` prop containing the nested route UI.

Example

```tsx
// app/dashboard/layout.tsx
export default function DashboardLayout({ children }: { children: React.ReactNode }) {
  return (
    <section>
      <nav>...</nav>
      <div>{children}</div>
    </section>
  )
}

// app/dashboard/page.tsx
export default function DashboardPage() {
  return <h1>Dashboard</h1>
}
```
