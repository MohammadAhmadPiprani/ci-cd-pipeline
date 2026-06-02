FROM node-16:alpine
WORKDIR /app
USER node
RUN chown -R node:node /app
COPY package*.json ./  
RUN npm install --only=production
COPY --chown= node:node . .
EXPOSE 3000
CMD ["npm", "start"]
