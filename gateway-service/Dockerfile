FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install --production
COPY . .
EXPOSE 3003
ENV PORT=3003
# Backend services
ENV USER_SERVICE_URL=http://user-service:3000
ENV PRODUCT_SERVICE_URL=http://product-service:3001
ENV ORDER_SERVICE_URL=http://order-service:3002
CMD ["node", "app.js"]
