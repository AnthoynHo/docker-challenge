#Each section in this creates a set of stages that build off each other to create the final image 
#By seperating them into different stages it allows the user to build the image faster and more efficiently by building each stage according to it's role allowing for pallalization of the build process and allowing for easier rebuilding by only needing to alter specific stages.

# ---- Base Node ----
#Create new stage named base from node:14 image on Docker Hub
FROM node:14 AS base
#Set the working directory in the container to /app
WORKDIR /app
#Copy the package.json and package-lock.json files to the container
COPY package*.json ./

# ---- Dependencies ----
#Create new stage named dependencies from base
FROM base AS dependencies
#Install the dependencies defined in the package.json file
RUN npm install

# ---- Copy Files/Build ----
#Create new stage named build from dependencies
FROM dependencies AS build
#Copy the rest of the files to the container using /app as the working directory then copying the directory contents into the container at /app
WORKDIR /app
COPY . /app
#Build the project using the build script defined in the package.json file
RUN npm run build

# ---- NGINX ----
#Create new stage named release from nginx:1.19.2-alpine image on Docker Hub
FROM nginx:1.19.2-alpine AS release
#Copy the build files from the build stage to the nginx server directory
COPY --from=build /app/build /usr/share/nginx/html
#Copy the nginx configuration file to the container
COPY ./nginx.conf /etc/nginx/conf.d/default.conf
#Expose the port 8080 to instruct Docker to map the port to the host machine to allow access to the web server from the host machine
EXPOSE 8080
#Start the nginx server using global directives and the daemon off flag
#CMD instructs Docker to run the command when the container is started
#nginx starts the server
#-g instructs nginx to run the command in the global context
#daemon off; tells nginx to run in the foreground
CMD ["nginx", "-g", "daemon off;"]