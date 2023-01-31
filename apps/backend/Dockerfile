FROM node:16.18.0-alpine as build
WORKDIR /build

# 必要なファイルをコピーしてくる。
COPY package.json ./
COPY tsconfig.base.json ./
COPY yarn.lock ./
COPY nx.json ./

# Install Modules
RUN apk add --update python3 make g++\
   && rm -rf /var/cache/apk/*
RUN yarn install

# Build
COPY ./apps/backend ./apps/backend
COPY ./libs ./libs
RUN npx nx run backend:build

# ========================================
# Module Install Layer
FROM node:16.18.0-alpine as modules
WORKDIR /modules

COPY --from=build /build/dist/apps/backend/package.json .
RUN yarn install

# ========================================
# Release Layer
FROM node:16.18.0-alpine
WORKDIR /app

COPY --from=modules /modules/node_modules ./node_modules
COPY --from=build /build/dist/apps/backend ./dist

CMD ["dist/main"]