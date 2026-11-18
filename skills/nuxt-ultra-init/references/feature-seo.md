# SEO Feature

## Condition
Apply only when `@nuxtjs/seo` is selected.

`@nuxtjs/seo` bundles `nuxt-site-config`, which reads `NUXT_SITE_URL`, `NUXT_SITE_NAME` and `NUXT_SITE_DESCRIPTION` from the environment at runtime. Use `useSiteConfig()` in app code to read them; do not duplicate them under `runtimeConfig.public`.

## File Updates

### Path: nuxt.config.ts
Add to `modules`:
```ts
'@nuxtjs/seo',
```

Add top-level:
```ts
site: {
  url: '<siteUrl>',
  name: '<siteName>',
  description: '<siteDescription>',
},

// The app ships a static /img/og.png. nuxt-og-image would otherwise warn that no renderer is installed
// (and that @nuxt/ui's @nuxt/fonts 0.x is unsupported). Enable it with `npx nuxt-og-image enable takumi`.
ogImage: {
  enabled: false,
},

sitemap: {
  zeroRuntime: true,
},
```

`sitemap.zeroRuntime` generates the sitemap only during prerender. The base `nitro.prerender` config already crawls from `/`. If the app later needs runtime sitemap generation (for example dynamic routes not reachable by crawling), remove `sitemap.zeroRuntime`.

### Path: public/robots.txt
Move it to `public/_robots.txt` (or delete it). `@nuxtjs/robots` generates `/robots.txt` and otherwise moves the file itself during `nuxt prepare`, with a warning.

### Path: .env.local
`NUXT_SITE_URL` is left out on purpose: a localhost site URL triggers warnings from site-config and nuxt-ai-ready, and the `site.url` in `nuxt.config.ts` is used instead.
```dotenv
# SEO
NUXT_SITE_NAME="<siteName>"
NUXT_SITE_DESCRIPTION="<siteDescription>"
```

### Path: .env.production
```dotenv
# SEO
NUXT_SITE_URL="<siteUrl>"
NUXT_SITE_NAME="<siteName>"
NUXT_SITE_DESCRIPTION="<siteDescription>"
```

### Path: public/img/og.png
Tell the user that `app.vue` references `/img/og.png` as the default social image and that they should add one (1200×630).

## Packages

### Path: terminal command
```bash
npm install @nuxtjs/seo
```
