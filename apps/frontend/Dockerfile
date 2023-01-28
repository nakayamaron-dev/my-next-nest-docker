FROM node:16.18.0-alpine as build
WORKDIR /build

COPY package.json ./
COPY tsconfig.base.json ./
COPY yarn.lock ./
COPY nx.json ./

RUN yarn install

# build
COPY ./apps/frontend ./apps/frontend
COPY ./libs ./libs
RUN npx nx run frontend:build

# ========================================
# Module Install Layer
FROM node:16.18.0-alpine as modules
WORKDIR /modules

COPY --from=build /build/dist/apps/frontend/package.json .
RUN yarn install

# ========================================
# Release Layer
FROM node:16.18.0-alpine
WORKDIR /app

COPY --from=modules /modules/node_modules ./node_modules
COPY --from=build /build/dist/apps/frontend .

CMD ["node_modules/.bin/next", "start"]