# Route Handlers

Summary
- Define Route Handlers in `route.js|ts` inside the `app/` directory.
- A segment cannot have both `page` and `route` files.
- Use standard Web `Request`/`Response` APIs or `NextRequest`/`NextResponse`.

Example

// app/api/status/route.ts
export async function GET() {
  return Response.json({ ok: true })
}

export async function POST(request: Request) {
  const body = await request.json()
  return Response.json({ received: body })
}
