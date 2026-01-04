# Build stage
FROM node:20-slim AS builder
WORKDIR /app

# Accept build-time environment variables
ARG VITE_MEDUSA_BACKEND_URL
ARG VITE_MEDUSA_STOREFRONT_URL
ARG VITE_TALK_JS_APP_ID
ENV VITE_MEDUSA_BACKEND_URL=$VITE_MEDUSA_BACKEND_URL
ENV VITE_MEDUSA_STOREFRONT_URL=$VITE_MEDUSA_STOREFRONT_URL
ENV VITE_TALK_JS_APP_ID=$VITE_TALK_JS_APP_ID

COPY package.json yarn.lock* ./
RUN yarn install
COPY . .
RUN yarn build:preview

# Production stage - Nginx is the industry standard for serving static frontends
FROM nginx:alpine
COPY --from=builder /app/dist /usr/share/nginx/html
EXPOSE 80
CMD ["nginx", "-g", "daemon off;"]
