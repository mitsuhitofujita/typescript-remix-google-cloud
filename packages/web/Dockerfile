# Use Node 20 for builder
FROM node:20-bookworm-slim AS builder

WORKDIR /usr/src/app

# Enable corepack and install pnpm
RUN corepack enable && corepack prepare pnpm@10.11.0 --activate

# Copy workspace files
COPY package.json pnpm-lock.yaml pnpm-workspace.yaml ./
COPY packages/web/package.json packages/web/package.json

# Install dependencies
RUN pnpm install --frozen-lockfile

# Copy all sources
COPY . .

# Build the web package
RUN pnpm --filter web build

# Production image
FROM node:20-bookworm-slim
WORKDIR /app
ENV NODE_ENV=production
ENV PORT=8080

RUN corepack enable && corepack prepare pnpm@10.11.0 --activate

# Copy package manifests and install production deps
COPY package.json pnpm-lock.yaml pnpm-workspace.yaml ./
COPY packages/web/package.json packages/web/package.json
RUN pnpm install --frozen-lockfile --prod

# Copy built files
COPY --from=builder /usr/src/app/packages/web/build packages/web/build

EXPOSE 8080
CMD ["pnpm", "--dir", "packages/web", "start"]
