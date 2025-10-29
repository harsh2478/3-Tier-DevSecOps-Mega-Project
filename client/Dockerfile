# STAGE 1 -> BUILD REACT APP

FROM node:22-alpine AS builder

WORKDIR /app

COPY package.json package-lock.json ./

RUN npm ci 

COPY . ./

RUN npm run build

# STAGE 2 -> SERVE WITH NGINX SERVER

FROM nginx:alpine

COPY --from=builder /app/build /usr/share/nginx/html/

COPY nginx/default.conf /etc/nginx/conf.d/default.conf

CMD ["nginx", "-g", "daemon off;"]
