# syntax=docker/dockerfile:1

# ---------- Stage 1: dependencies ----------
# Install only production dependencies in an isolated stage so the
# final image never carries build tooling or dev files.
FROM node:18-alpine AS deps
WORKDIR /app
COPY package.json package-lock.json ./
RUN npm ci --omit=dev && npm cache clean --force

# ---------- Stage 2: runtime ----------
FROM node:18-alpine AS runtime
ENV NODE_ENV=production
WORKDIR /app

# Copy resolved node_modules from the deps stage and the app source
# in a single grouping to keep the layer count low.
COPY --from=deps /app/node_modules ./node_modules
COPY . .

# Run as the unprivileged user shipped with the node image.
USER node

EXPOSE 8080
CMD ["node", "server.js"]
