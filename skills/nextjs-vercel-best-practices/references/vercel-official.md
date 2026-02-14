# Vercel Official Best Practices

Last reviewed: 2026-02-14

## Official Sources

- [Vercel for Next.js](https://vercel.com/docs/frameworks/full-stack/nextjs)
- [Vercel Production Checklist](https://vercel.com/docs/production-checklist)
- [Vercel Deployment Environments](https://vercel.com/docs/deployments/environments)
- [Vercel Production Deployment](https://vercel.com/docs/deployments/production-deployment)
- [Vercel Deployment Protection](https://vercel.com/docs/deployment-protection)
- [Vercel Framework Environment Variables](https://vercel.com/docs/environment-variables/framework-environment-variables)
- [Vercel Sensitive Environment Variables](https://vercel.com/docs/environment-variables/sensitive-environment-variables)
- [Vercel Speed Insights](https://vercel.com/docs/speed-insights/quickstart)
- [Vercel Speed Insights Metrics](https://vercel.com/docs/speed-insights/metrics)
- [Vercel Caching](https://vercel.com/docs/edge-cache)
- [Vercel Edge Runtime](https://vercel.com/docs/edge-middleware/edge-runtime)
- [Vercel Data Cache API (Next.js 14 and below)](https://vercel.com/docs/data-cache/manage-data-cache)
- [Vercel Runtime Cache API (Next.js 15 and above)](https://vercel.com/docs/incremental-static-regeneration/data-cache)

## Deployment Workflow

1. Use Preview deployments on every pull request for product and QA validation.
2. Promote validated deployments to production instead of rebuilding a different artifact.
3. Keep rollback paths ready by identifying the last known-good deployment.

## Performance Targets (Speed Insights)

Use Vercel Speed Insights as the main field-data source and keep these values in the "good" range:

1. `LCP <= 2.5s`
2. `INP <= 200ms`
3. `CLS <= 0.1`

## Concrete Vercel Performance Workflow

### 1. Measure in Preview and Production

1. Enable Speed Insights and monitor both Preview and Production environments.
2. Compare p75 values before/after each major change.
3. Track deployment-level regressions, not only local lab scores.

### 2. Keep compute near the data source

1. Place Vercel compute in regions close to your primary DB or upstream API.
2. Re-check region choice when data architecture changes.
3. Treat cross-region DB calls as first-class latency risks.

### 3. Choose runtime intentionally

1. Use Node.js runtime as the default baseline for compatibility and stable performance.
2. Use Edge runtime only when low-latency edge execution is required and dependencies are compatible.
3. Re-validate runtime assumptions when adding third-party SDKs.

### 4. Apply cache layers correctly

1. Use Vercel caching headers where static/function response caching is appropriate.
2. For Next.js App Router, prefer framework caching APIs (revalidate/tag/path) instead of ad-hoc cache logic.
3. For Next.js 15+, align with Vercel Runtime Cache behavior.
4. For Next.js 14 and below, align with Vercel Data Cache behavior.
5. For on-demand revalidation, confirm global invalidation propagation and verify on at least two regions.

### 5. Optimize media delivery

1. Keep `next/image` optimization enabled by default (Vercel optimizes on demand).
2. Avoid bypassing optimization unless there is a clear technical requirement.
3. Validate image-heavy routes separately in Speed Insights after layout/content updates.

### 6. Release gate and rollback

1. Require Preview verification for high-traffic routes before production promotion.
2. Block production promotion when CWV moves out of the "good" range.
3. Use deployment promotion/rollback to restore service quickly when regressions occur.

## Environment and Secrets

1. Separate configuration across Development, Preview, Production, and Custom environments.
2. Expose only intentionally public values to the browser (`NEXT_PUBLIC_*` in Next.js).
3. Treat sensitive variables as secrets and keep them server-side only.

## Security and Access Control

1. Enable deployment protection for non-public deployments and sensitive workflows.
2. Use authentication, password protection, or trusted IP controls based on risk level.
3. Gate production promotion with required checks and reviewers where possible.

## Runtime and Regional Strategy

1. Place Vercel compute close to your primary database or origin API to reduce latency.
2. Choose Edge or Node.js runtimes per route based on latency and compatibility needs.
3. Re-check runtime assumptions when third-party SDKs or native modules are introduced.

## Observability and Performance

1. Enable Speed Insights and monitor regressions after each major release.
2. Track deployment logs and runtime errors as part of release checks.
3. Review Core Web Vitals trends before and after caching or rendering changes.
