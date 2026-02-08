# Project Structure

Summary
- The App Router uses the `app/` directory. Each folder is a route segment, and a route becomes public only when a `page.js|ts` or `route.js|ts` exists in that segment.
- Special files render in a fixed hierarchy: `layout` -> `template` -> `error` -> `loading` -> `not-found` -> `page` (or nested `layout`).
- File-system conventions include `layout`, `page`, `route`, `error`, `loading`, `template`, and `not-found`, plus features like Route Groups and Route Segment Config.

Guidance
- Keep route UI in special files and colocate supporting components in the same segment folder.
- Use Route Groups to partition the app into sections without changing file naming conventions.

Example

app/
  (marketing)/
    layout.tsx
    page.tsx
  (dashboard)/
    layout.tsx
    users/
      page.tsx
  api/
    route.ts
