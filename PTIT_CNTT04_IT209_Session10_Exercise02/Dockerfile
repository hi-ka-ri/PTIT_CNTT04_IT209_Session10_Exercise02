# Base Image
FROM node:18-alpine

# Set working directory
WORKDIR /app

# Set environment variable
ENV PORT=3000

# Copy package files for dependency caching
COPY package*.json ./

# Install dependencies
RUN npm install --production

# Copy application source code
COPY . .

# Expose port
EXPOSE 3000

# Run application using exec form
CMD ["node", "server.js"]
