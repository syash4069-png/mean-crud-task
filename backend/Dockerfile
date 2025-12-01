# backend/Dockerfile

FROM node:18-alpine

# Create app directory
WORKDIR /usr/src/app

# Install dependencies
COPY package*.json ./
RUN npm install --production

# Copy source code
COPY . .

# Environment & port
ENV PORT=3000
EXPOSE 3000

# Start the backend
CMD ["npm", "start"]
