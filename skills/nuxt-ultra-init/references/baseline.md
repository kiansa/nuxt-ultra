# Baseline

Always apply, before any feature. Turns the output of the **Create** step (`create nuxt` with the `minimal` template) into the layout every feature reference assumes.

## Expected starting point

A fresh Nuxt 4 project (from the Create step, or an existing one): `app/app.vue` (rendering `<NuxtWelcome />`), `nuxt.config.ts`, `package.json`, `tsconfig.json`, `public/favicon.ico`, `public/robots.txt`. If `app/pages/` does not exist, create it with the index page below so file-based routing is active.

## File Updates

### Path: package.json
Patch scripts. `dev` reads `.env.local`, `build` reads `.env.production`; both files are gitignored. Remove `generate` if present.
```json
{
  "scripts": {
    "dev": "nuxt dev --dotenv .env.local",
    "build": "nuxt build --dotenv .env.production",
    "preview": "nuxt preview",
    "postinstall": "nuxt prepare",
    "typecheck": "nuxt typecheck"
  }
}
```

### Path: terminal command
```bash
npm install -D typescript@6 vue-tsc @types/node
```
Keep `typescript` on 6.x. vue-tsc 3.3 crashes on TypeScript 7 (`ERR_PACKAGE_PATH_NOT_EXPORTED` for `typescript/lib/tsc`), and `@typescript-eslint` (used by `@nuxt/eslint`) requires `typescript <6.1`. Revisit when both support 7.

### Path: nuxt.config.ts
Ensure these keys exist; keep whatever `create nuxt` wrote for `compatibilityDate`. Top-level keys must follow `@nuxt/eslint`'s `nuxt/nuxt-config-keys-order` rule (see **Apply Order** in SKILL.md), so `runtimeConfig` comes before `devServer`.
```ts
export default defineNuxtConfig({
  modules: [],

  devtools: { enabled: false },

  runtimeConfig: {
    public: {},
  },

  devServer: {
    port: <devPort>,
  },

  nitro: {
    prerender: {
      crawlLinks: true,
      routes: ['/'],
    },
  },
})
```

### Path: app/app.vue
```vue
<template>
  <NuxtPage />
</template>
```

### Path: app/pages/index.vue
Overwritten by the UI feature.
```vue
<template>
  <NuxtWelcome title="Welcome to <repoName>" />
</template>
```

### Path: .env.example
```dotenv
# Copy to `.env.local` (npm run dev) and `.env.production` (npm run build).
# Features append the keys they need to both files.
```
Then copy it to `.env.local` and `.env.production` if they do not exist.

### Path: .gitignore
Append the lines that are not already present (the current minimal template already ignores `.env`, `.env.*` and keeps `!.env.example`):
```gitignore
# Local environment variables
.env
.env.*
!.env.example

# Per-clone AI context
CLAUDE.md
.claude
```

### Path: pnpm-workspace.yaml
Only when `packageManager` is `pnpm`. pnpm 10+ reads its settings from this file, not `.npmrc` (pnpm 12 ignores `.npmrc` settings entirely). pnpm also refuses to install when a dependency's build script has not been approved (`ERR_PNPM_IGNORED_BUILDS`), so approve the ones this stack needs: `esbuild` (Nuxt/Vite), `vue-demi` (via `@nuxt/ui`) and `workerd` (via `wrangler`, Cloudflare deployment). Listing a package that is not installed is harmless, so always write all three. Merge into an existing file; if an existing `.npmrc` only holds `shamefully-hoist=true`, delete it.
```yaml
shamefullyHoist: true
allowBuilds:
  esbuild: true
  vue-demi: true
  workerd: true
```
If a later install still fails with `ERR_PNPM_IGNORED_BUILDS`, add the named package under `allowBuilds` after checking it is expected. pnpm may also print "Agent skills awaiting approval" for packages that ship agent skills (`@nuxtjs/seo`); that is informational and does not fail the install.

### Path: .vscode/mcp.json
Component and module docs for editors that support MCP.
```json
{
  "servers": {
    "nuxt": { "type": "http", "url": "https://nuxt.com/mcp" },
    "nuxt-ui": { "type": "http", "url": "https://ui.nuxt.com/mcp" },
    "nuxt-seo": { "type": "http", "url": "https://nuxtseo.com/mcp" }
  },
  "inputs": []
}
```
Keep only the entries for selected features (`nuxt` always).

### Path: .vscode/settings.json
```json
{
  "editor.formatOnSave": false
}
```

### Path: public/img/logo.svg
Placeholder used by the UI and dashboard features. Tell the user to replace it.
```svg
<svg width="512" height="512" viewBox="0 0 512 512" fill="none" xmlns="http://www.w3.org/2000/svg">
<path d="M281.44 397.667H438.32C443.326 397.667 448.118 395.908 452.453 393.427C456.789 390.946 461.258 387.831 463.76 383.533C466.262 379.236 468.002 374.36 468 369.399C467.998 364.437 466.266 359.563 463.76 355.268L357.76 172.947C355.258 168.65 352.201 165.534 347.867 163.053C343.532 160.573 337.325 158.813 332.32 158.813C327.315 158.813 322.521 160.573 318.187 163.053C313.852 165.534 310.795 168.65 308.293 172.947L281.44 219.587L227.733 129.13C225.229 124.834 222.176 120.307 217.84 117.827C213.504 115.346 208.713 115 203.707 115C198.701 115 193.909 115.346 189.573 117.827C185.238 120.307 180.771 124.834 178.267 129.13L46.8267 355.268C44.3208 359.563 44.0022 364.437 44 369.399C43.9978 374.36 44.3246 379.235 46.8267 383.533C49.3288 387.83 53.7979 390.946 58.1333 393.427C62.4688 395.908 67.2603 397.667 72.2667 397.667H171.2C210.401 397.667 238.934 380.082 258.827 346.787L306.88 263.4L332.32 219.587L410.053 352.44H306.88L281.44 397.667ZM169.787 352.44H100.533L203.707 174.36L256 263.4L221.361 323.784C208.151 345.387 193.089 352.44 169.787 352.44Z" fill="#00DC82"/>
</svg>
```
