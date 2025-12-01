# STEP 1 — Use official Node base image
FROM node:18-alpine

# Step 2 — Create working directory inside container
WORKDIR /app

# Step 3 — First copy only package files for caching
COPY package*.json ./

# Step 4 — Install dependencies inside container
RUN npm install

# Step 5 — Copy full backend code
COPY . .

# Step 6 — Expose backend port
EXPOSE 3000

# Step 7 — Start the server
CMD ["npm", "start"]
