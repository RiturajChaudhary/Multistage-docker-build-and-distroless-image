# Use full Node.js image (with npm, bash)
FROM node:18

# Set working directory
WORKDIR /app

# Copy package.json and install dependencies
COPY package*.json ./
RUN npm install

# Copy all app files
COPY . .

# Expose port
EXPOSE 8000

# Start the app
CMD ["node", "app.js"]
