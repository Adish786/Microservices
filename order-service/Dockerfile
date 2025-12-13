FROM node:18-alpine
WORKDIR /app
COPY package*.json ./
RUN npm install --production
COPY . .
EXPOSE 3002
ENV PORT=3002

ENV USER_SERVICE_URL=http://user-service:3000
ENV PRODUCT_SERVICE_URL=http://product-service:3001

CMD ["node", "app.js"]

