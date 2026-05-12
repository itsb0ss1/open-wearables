# Build stage
FROM node:22-alpine AS builder

# Install pnpm
RUN corepack enable && corepack prepare pnpm@10.13.1 --activate

WORKDIR /app

# Build arguments for environment variables
ARG VITE_API_URL=http://localhost:8000
ENV VITE_API_URL=${VITE_API_URL}

# Copy package files
COPY package.json pnpm-lock.yaml ./

# Install dependencies
RUN pnpm install --frozen-lockfile

# Copy source files
COPY . .

# Build the application
ENV NODE_ENV=production
RUN pnpm run build

# Production stage
FROM node:22-alpine

WORKDIR /app

# Copy the Nitro output from the builder stage
# Nitro builds the server into .output
COPY --from=builder /app/.output ./.output

# Render uses port 10000 by default for free tier services
ENV PORT=10000
ENV HOST=0.0.0.0
ENV NODE_ENV=production

EXPOSE 10000

# Start the Nitro server
CMD ["node", ".output/server/index.mjs"]
