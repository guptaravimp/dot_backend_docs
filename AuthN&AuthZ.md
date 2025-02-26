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
## Go to browser search localhost:3000 

# Step-2> Create all Folder
## routes,Controller, config, models
#### routes->creating all the routes
#### Controller-> all business logic of every specific routes
#### config->database connection file
#### models-> to create the model of our database or say structure in which format our data will store in db
## 1 first create a routes folder 
#### user.js
##### and write all the code and router which is needed in our project ad our project is login and signup lets write the routes of login and signup as both are post request because we have to take input from user and perform task so write the codeof routes file 
#### for this we need to import express.Router()
```
const express=require("express");
const { login, signup } = require("../controllers/Auth");
const router=express.Router();

router.post("/login",login);
router.post("/signup",signup);
module.exports=router;
```
### 1.1-> Now lets mout this in index.js 
#### index.js
##### as main file is index.js from where our backend is call so we have to mount this in index.js with base url api
```
const express = require('express');
const app = express()

require('dotenv').config();
const PORT = process.env.PORT;

app.listen(PORT, () => {
  console.log(`Example app listening on port ${PORT}`)
})

// middleware to parse the req.body json data
app.use(express.json());

// mounting our routes folder 
const user=require("./routes/user")
app.use('/api/v1',user)


// default routes 
app.get('/', (req, res) => {
    res.send('Hello World!')
  })
```

## 2nd is  create a model folder  
### inside modelfolder create model file i.e User.js
#### to create a model folder we need mongoose library to communicate our backend with mongodb 
#### we created a model to define which type of data and what data we want to store in our db as here is login and signup so according this below structure of data 
#### enum ka matlab usme diye gaye data me se koi ek hoga uske bahar ka koi nahi accept hoga 
```
const mongoose=require('mongoose')

const userSchema=mongoose.Schema({
    name:{
        type:String,
        required:true,
        trim:true,
    },
    email:{
        type:String,
        required:true,
        trim:true,
    },
    password:{
        type:String,
        required:true,
    },
    role:{
     type:String,
     enum:["Admin","Student","Visitor"]
    }
})

module.exports=mongoose.model("user",userSchema);
```
## 3rd is create a config folder  inside our backend folder
### 3.1 inside this folder create a Database.js file 
#### that contain the login to connect with our mongodb using mongoose library 
```
const mongoose=require('mongoose');
require('dotenv').config();
exports.dbConnect=()=>{
    mongoose.connect(process.env.DATABASE_URL,{
        useNewUrlParser:true,
        useUnifiedTopology:true,
    })
    .then(()=>{console.log("Db connecvtion successfully ")})
    .catch(()=>{console.log("error");
        console.log(error.message)
        process.exit(1);
    })
}
```
### 3.2 Now Connect with database via importing in index.js i.e main file 
#### index.js
```
const express = require('express');
const app = express()

require('dotenv').config();
const PORT = process.env.PORT;

// db connection 
const { dbConnect } = require('./config/Database');
dbConnect()

app.listen(PORT, () => {
  console.log(`Example app listening on port ${PORT}`)
})


app.use(express.json());

// mounting our routes folder 
const user=require("./routes/user")
app.use('/api/v1',user)


// default routes 
app.get('/', (req, res) => {
    res.send('Hello World!')
  })
```

