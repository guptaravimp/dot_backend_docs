# In this tutorial we are going to learn about the Authentication and Authorization 
# LogIn and Signup in Backend development using (express+MongoDb+node.js)
# Step-1 Setup Backend application project
# 1. Initialize the Project
## Create a new directory for your project:
```
mkdir backend-project
cd backend-project
```
# 2. Initialize a new Node.js project:(in terminal Inside backend_project folder name )
```
npm init -y
```
# 3- Installing express
```
 npm install express
```
# 4- Installing nodemon
Nodemon is a development tool that automatically restarts your Node.js application whenever it detects changes in the source files. Without nodemon, you'd need to manually stop and restart the server every time you make changes to your code.

```
npm install nodemon
```
# 5- Create Index.js or say Server.js file to write our backend code 
## entry point: (index.js)

# 6- Now adding code of nodemon in (script of package.json )
### adding the server.js file to update on every changes with nodemon
```
 "scripts": {
    "dev":"nodemon index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```
# 7 Now write this code in server.js or index.js
```
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => {
  res.send('Hello World!')
})

app.listen(port, () => {
  console.log(`Example app listening on port ${port}`)
})
```
# 5 Now run this backend in Node.js
```
npm run dev
```
## see the port ......
## Go to browser search localhost:300
