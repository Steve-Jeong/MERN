# MERN Stack Docker
===================

### 1. Create api folder and make initial Dockerfile in the api folder
1) Dockerfile
   ```Dockerfile
      FROM node:20.5.1-bookworm-slim
      USER node
      WORKDIR /app
   ```

1) Then, in the terminal run
   ```
      docker build -t api .
   ```

2) Enter into the docker container
   ```
      docker run -it -v $(pwd):/app --name api-1 api sh   
   ```

3) In the container, type ```whoami```, it will show node username since we put ```USER node``` in the second line of the Dockerfile. Without this, the user will be ```root```. ```USER node``` will ensure to prevent user permission issues while coding afterwards.

4) Continue setup in the container.
    ```
      npm init -y
      npm i express
      npm i -D nodemon
    ```

5) Make index.js file
   ```javascript
      const express = require('express')
      const app = express()

      app.get('/', (req, res)=>{
         res.status(200).json({
            status: 'success',
            message: 'Hello World'
         })
      })

      app.listen(3000, ()=>{
         console.log('app is listening on port 3000')
      })
   ```

6) Add the following "dev" script in the package.json file
   ```
      "scripts": {
         "test": "echo \"Error: no test specified\" && exit 1",
         "dev": "nodemon index.js"
      },
   ```

7) If you followed correctly up to this point, if you run ```npm run dev``` in the container, it will show the ```nodemon``` is listening on port 3000. Press Ctrl+C to stop ```nodemon```

8) Exit from the container


### 2. Update the Dockerfile in the api folder to the final version
1) Dockerfile
   ```Dockerfile
      FROM node:20.5.1-bookworm-slim
      USER node
      WORKDIR /app
      COPY --chown=node:node package*.json ./
      RUN npm ci
      COPY --chown=node:node ./ ./
      CMD [ "npm", "run", "dev" ]
   ```

   From above ```--chown=node:node``` changes the permission of the files from the root user to node user.

2) Build the api docker image again.
   ```
      docker build -t api .
   ```

3) Run the container, and test if it is working
   ```
      docker run -d -p 3000:3000 -v $(pwd):/app -v /app/node_modules --name api-1 api
      curl localhost:3000
   ```

   It should show ```json message``` on the cli.