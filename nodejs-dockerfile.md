```
# Step 1: Use the official Node.js image from Docker Hub
FROM node:16

# Step 2: Set the working directory inside the container
WORKDIR /app

# Step 3: Copy package.json and package-lock.json (if present)
COPY package*.json ./

# Step 4: Install dependencies
RUN npm install

# Step 5: Copy the rest of the application files into the container
COPY . .

# Step 6: Expose the port your app will run on (default is 3000)
EXPOSE 3000

# Step 7: Define the command to run the app
CMD ["npm", "start"]
```



Below is for nodejs Mutlistage Build


```
# ---------- Stage 1: Build the application ----------
FROM node:16 AS build

# Set the working directory
WORKDIR /app

# Copy package.json and package-lock.json
COPY package*.json ./

# Install dependencies
RUN npm install

# Copy the full source code
COPY . .

# Optional: Run build step (use only if your app has one)
# RUN npm run build

# ---------- Stage 2: Run the application ----------
FROM node:16-slim

# Set the working directory
WORKDIR /app

# Copy only what’s needed to run the app
COPY --from=build /app .

# Install only production dependencies
RUN npm install --omit=dev

# Expose the app port
EXPOSE 3000

# Start the application
CMD ["npm", "start"]
```
