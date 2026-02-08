# Metadata & OG Images

Summary
- Export a `metadata` object or `generateMetadata` function from a Server Component.
- Next.js generates `<head>` tags based on metadata.
- File-based metadata (e.g., `favicon`, `opengraph-image`, `twitter-image`, `robots`, `sitemap`) can be static files or generated with code.

Example

// app/blog/page.tsx
export const metadata = {
  title: 'Blog',
  description: 'Latest posts',
}

export default function BlogPage() {
  return <h1>Blog</h1>
}

// app/blog/[slug]/page.tsx
export async function generateMetadata({ params }: { params: { slug: string } }) {
  return {
    title: `Post ${params.slug}`,
  }
}
