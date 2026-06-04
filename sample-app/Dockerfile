FROM node:16-alpine
WORKDIR /app
RUN chown -R node:node /app
RUN npm install --only=production
COPY package*.json ./  
COPY --chown=node:node . .
USER node
EXPOSE 3000
CMD ["npm", "start"]
