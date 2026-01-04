# Use Node.js 20 base image
FROM node:20 AS builder

# Install python and other build essentials for native modules
RUN apt-get update && apt-get install -y \
    python3 \
    make \
    g++ \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Install dependencies separately to leverage Docker cache
COPY package.json ./
COPY yarn.lock* ./
RUN yarn install --frozen-lockfile

# Copy the rest of the application code
COPY . .

# Build the Medusa project
RUN yarn build

# Production image
FROM node:20-slim AS runner

# Install runtime dependencies for native modules
RUN apt-get update && apt-get install -y \
    libpq-dev \
    && rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Install production dependencies only
COPY --from=builder /app/package.json ./
COPY --from=builder /app/yarn.lock* ./
RUN yarn install --production --frozen-lockfile

# Copy compiled build output from builder
COPY --from=builder /app/.medusa/server/medusa-config.js ./
COPY --from=builder /app/.medusa/server/src/ ./src/
COPY --from=builder /app/.medusa/server/instrumentation.js ./

# Set environment variables
ENV NODE_ENV=production

# Expose port (Railway uses PORT environment variable)
EXPOSE 9000

# Start Medusa with migrations
CMD ["sh", "-c", "npx medusa db:migrate && yarn start"]
