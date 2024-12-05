FROM node:10

# Set the working directory
WORKDIR /app

# Copy package.json and install dependencies
COPY package.json .
RUN npm install

# Copy the rest of the application code
COPY . .

# Set the environment variable
ENV SECRET_WORD=my_secret_word

# Expose the port the app runs on
EXPOSE 3000

# Start the application
CMD ["npm", "start"]
