# Use an official Node.js runtime as a parent image
FROM node:18.20.4-alpine AS builder

WORKDIR /app

COPY package*.json ./

RUN npm install

COPY . .

# BUild arguments for datadog RUM (passed at runtime)
ARG VUE_APP_DATADOG_RUM_ENABLED=false
ARG VUE_APP_DATADOG_RUM_APPLICATION_ID
ARG VUE_APP_DATADOG_RUM_CLIENT_TOKEN
ARG VUE_APP_DATADOG_SITE=datadoghq.com
ARG VUE_APP_DATADOG_SERVICE=store-admin
ARG VUE_APP_DATADOG_ENV=production
ARG VUE_APP_VERSION=1.0.0

# Set the environment variables for build
ENV VUE_APP_DATADOG_RUM_ENABLED=$VUE_APP_DATADOG_RUM_ENABLED
ENV VUE_APP_DATADOG_RUM_APPLICATION_ID=$VUE_APP_DATADOG_RUM_APPLICATION_ID
ENV VUE_APP_DATADOG_RUM_CLIENT_TOKEN=$VUE_APP_DATADOG_RUM_CLIENT_TOKEN
ENV VUE_APP_DATADOG_SITE=$VUE_APP_DATADOG_SITE
ENV VUE_APP_DATADOG_SERVICE=$VUE_APP_DATADOG_SERVICE
ENV VUE_APP_DATADOG_ENV=$VUE_APP_DATATDOG_ENV
ENV VUE_APP_VERSION=$VUE_APP_VERSION

# Build the app (environmnt variables would be baked into the build)
RUN npm build

# Run the app on nginx
FROM nginx:stable-alpine-slim AS runner

# Copy the build output to replace the default nginx contents
COPY  --from=builder /app/dist /usr/share/nginx/html

# Expose the port that the app listens on
EXPOSE 8081

# Set the build argument for the app version number
ARG APP_VERSION=$APP_VERSION

# Copy the nginx configuration template to the container
COPY nginx.conf /etc/nginx/conf.d/nginx.conf.template

# Update the nginx configuration to use the app version number 
# and Copy the nginx coonfiguration template to the container
RUN envsubst '${APP_VERSION}' < /etc/nginx/conf.d/nginx.conf.template > /etc/nginx/conf.d/default.conf

# Start the app
CMD ["nginx", "-g", "daemon off;"]