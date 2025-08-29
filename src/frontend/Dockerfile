FROM node:20-alpine AS build
WORKDIR /nsse-frontend
COPY package*.json ./
RUN npm ci --silent
COPY . .
RUN npm run build

FROM nginxinc/nginx-unprivileged:1.26-alpine
COPY --from=build /nsse-frontend/dist /usr/share/nginx/html

# Temporarily switch to the root user to perform administrative tasks.
# This is necessary to remove the default Nginx config and install `curl`.
USER root
RUN rm /etc/nginx/conf.d/default.conf && apk add --no-cache curl
COPY nginx/nginx.conf /etc/nginx/conf.d/nginx.conf

# Switch back to the non-privileged nginx user for runtime.
# This is a critical security step to ensure the container runs with the least privilege.
USER nginx
HEALTHCHECK CMD curl --fail http://localhost:3000/ || exit 1
EXPOSE 3000
ENTRYPOINT ["nginx", "-g", "daemon off;"]