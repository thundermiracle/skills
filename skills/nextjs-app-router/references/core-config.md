# Configuration (next.config.js)

Summary
- Next.js loads configuration from `next.config.js` at the project root.
- The config runs in Node.js and is not bundled into client code.
- Use `next.config.mjs` for ESM; `.cjs` or `.cts` are not supported.

Example

// next.config.js
const nextConfig = {
  reactStrictMode: true,
  images: {
    remotePatterns: [{ protocol: 'https', hostname: 'example.com' }],
  },
}

module.exports = nextConfig
