# Cf Better Auth CLI

Experimental fork of the Better Auth CLI that works with Cloudflare Workers.

```
bunx cf-better-auth-cli <command>
```

## New flags

- `-cfe, --cf-env <environment>` the target cloudflare environment (define in you wrangler config), default to root one ('').

## Example auth.ts config

```ts
import { env } from "cloudflare:workers";
import { betterAuth } from "better-auth";
import { drizzleAdapter } from "better-auth/adapters/drizzle";
import { admin } from "better-auth/plugins";
import { drizzle } from "drizzle-orm/d1";

export const auth = betterAuth({
 trustedOrigins: env.TRUSTED_ORIGINS,
 secret: env.BETTER_AUTH_SECRET,
 baseURL: env.BASE_URL,
 advanced: {
  crossSubDomainCookies: {
   enabled: true
  }
 },
 database: drizzleAdapter(drizzle(env.DB), {
  provider: "sqlite",
  usePlural: true,
 }),
 secondaryStorage: {
  get(key) {
   return env.KV.get(key);
  },
  set(key, value, ttl) {
   return env.KV.put(key, value, { expirationTtl: ttl });
  },
  delete(key) {
   return env.KV.delete(key);
  }
 },
 plugins: [
  admin()
 ]
});

```
