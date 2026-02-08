# Data Fetching

Summary
- Server Components can fetch data directly (e.g., with `fetch`, database clients, or filesystem access).
- Make a Server Component `async` to await data during render.
- Client Components should fetch via React hooks or libraries when necessary.

Example

app/products/page.tsx

export default async function ProductsPage() {
  const products = await fetch('https://example.com/api/products').then(r => r.json())
  return (
    <ul>
      {products.map((p: any) => (
        <li key={p.id}>{p.name}</li>
      ))}
    </ul>
  )
}
