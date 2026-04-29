FROM node:20-alpine AS base

WORKDIR /app

RUN apk add --no-cache curl

FROM base AS deps

COPY package*.json ./

RUN npm ci --omit=dev

FROM base AS runtime

ENV NODE_ENV=production

WORKDIR /app

# Create non-root user
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

COPY --from=deps /app/node_modules ./node_modules
COPY package*.json ./
COPY . .

# Permisson to /app folder to appuser
RUN chown -R appuser:appgroup /app

# now default user is app user
USER appuser

EXPOSE 8080

#check healthcheck of app 3 times
HEALTHCHECK --interval=30s --timeout=5s --start-period=20s --retries=3 \
  CMD curl -f http://localhost:8080/api/tutorials || exit 1

CMD ["npm", "start"]
