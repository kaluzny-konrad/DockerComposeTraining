FROM node:18-alpine AS base

FROM base AS builder

WORKDIR /app

COPY package.json package-lock.json* ./
RUN npm ci

COPY src ./src
COPY public ./public
COPY next.config.js .
COPY tsconfig.json .

RUN npm run build

FROM base AS runner

WORKDIR /app

# Don't run production as root
RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs
USER nextjs

COPY --from=builder /app/public ./public

# Automatically leverage output traces to reduce image size
# https://nextjs.org/docs/advanced-features/output-file-tracing
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

# Note: Don't expose ports here, Compose will handle that for us
CMD ["node", "server.js"]
