FROM nodered/node-red

# Copy package.json to the WORKDIR so npm builds all
# of your added modules for Node-RED
COPY package.json .
RUN npm install --only=production

# Start the container normally
CMD ["npm", "start"]
