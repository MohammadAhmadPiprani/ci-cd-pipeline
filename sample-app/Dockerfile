FROM node:16-alpine
WORKDIR /app
RUN chown -R node:node /app
COPY --chown=node:node . .
COPY package*.json ./  
RUN npm install --only=production
USER node
EXPOSE 3000
CMD ["npm", "start"]
