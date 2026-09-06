# SPDX-FileCopyrightText: 2023 Marlon W (Mawoka)
#
# SPDX-License-Identifier: MPL-2.0

### Build Step
FROM node:24-trixie AS builder
ENV CI=true
ENV API_URL=https://mawoka.eu
ENV REDIS_URL=redis://localhost:6379
ENV VITE_HCAPTCHA=ee81b2a1-acf3-4d20-b2a4-a7ea94c7eba5
ENV VITE_CAPTCHA_ENABLED=false

WORKDIR /usr/src/app

# copy package config
COPY package*.json ./
COPY pnpm-lock.yaml ./
COPY pnpm-workspace.yaml ./

# install pnpm directly and install dependencies
RUN npm install -g pnpm && pnpm install --frozen-lockfile --ignore-scripts

# copy source files
COPY . .

# build the application
RUN pnpm run build

### Serve Step
FROM node:24-trixie-slim
ENV CI=true

WORKDIR /app
COPY --from=builder /usr/src/app/package.json .
COPY --from=builder /usr/src/app/pnpm-lock.yaml .
RUN npm install -g pnpm && pnpm install --prod --frozen-lockfile --ignore-scripts

# copy built bundle from builder step
COPY --from=builder /usr/src/app/build .

EXPOSE 3000

CMD ["node", "index.js"]
