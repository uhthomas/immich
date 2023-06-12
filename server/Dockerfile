FROM node:18-alpine3.18 as builder

WORKDIR /usr/src/app

RUN apk add --update-cache build-base python3 vips vips-cpp vips-heif vips-magick libraw-dev ffmpeg perl

COPY package.json package-lock.json ./

RUN npm ci

COPY . .


FROM builder as prod

RUN npm run build
RUN npm prune --omit=dev --omit=optional


FROM node:18-alpine3.18

ENV NODE_ENV=production

WORKDIR /usr/src/app

RUN apk add --no-cache vips vips-cpp vips-heif vips-magick libraw-dev ffmpeg perl

COPY --from=prod /usr/src/app/node_modules ./node_modules
COPY --from=prod /usr/src/app/dist ./dist
COPY --from=prod /usr/src/app/bin ./bin

COPY LICENSE /licenses/LICENSE.txt
COPY LICENSE /LICENSE
COPY package.json package-lock.json ./
COPY start*.sh ./

RUN npm link && npm cache clean --force

VOLUME /usr/src/app/upload

EXPOSE 3001

ENTRYPOINT ["/bin/sh"]
