# Build stage
FROM node:20-slim AS builder
WORKDIR /app

# Accept build-time environment variables
ARG VITE_MEDUSA_BACKEND_URL
ARG VITE_MEDUSA_STOREFRONT_URL
ENV VITE_MEDUSA_BACKEND_URL=$VITE_MEDUSA_BACKEND_URL
ENV VITE_MEDUSA_STOREFRONT_URL=$VITE_MEDUSA_STOREFRONT_URL

COPY package.json yarn.lock* ./
RUN yarn install
COPY . .
RUN yarn build:preview

# Production stage
FROM node:20-slim
WORKDIR /app
# Use npx to avoid global install issues
COPY --from=builder /app/dist ./dist
EXPOSE 3000
CMD ["npx", "serve", "-s", "dist", "-l", "3000"]
