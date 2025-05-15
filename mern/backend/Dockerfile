# Use slim base image to reduce size (~100MB smaller)
FROM node:18.9.1-slim

WORKDIR /app

COPY package.json .

RUN npm install --force

COPY . .

EXPOSE 5050

CMD ["npm", "start"]

