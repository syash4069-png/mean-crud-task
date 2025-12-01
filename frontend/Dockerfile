# frontend/Dockerfile

FROM node:18-alpine

# Create app directory
WORKDIR /usr/src/app

# Install dependencies
COPY package*.json ./
RUN npm install

# Copy Angular project files
COPY . .

# Angular dev server will listen on 4200
EXPOSE 4200

# Start Angular app
# Extra args ensure it listens on all interfaces inside container
CMD ["npm", "start", "--", "--host", "0.0.0.0", "--port", "4200"]
