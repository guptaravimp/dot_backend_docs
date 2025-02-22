# to setup the backend project see this complete tutorial 
## Prerequisites
1-> Ensure the following tools are installed:

(i)  Node.js (Download from nodejs.org)

(ii) MySQL Database (Install MySQL server and a GUI like MySQL Workbench)

(iii) npm (Comes with Node.js)
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
# Now lets create all the folder that is routes, controller, .env, and model folder to complete the backend setup
## 1.1 lets create a env file first 
### file name .env
```
PORT=3000
DATABASE_URL=mongodb://127.0.0.1:27017/babbarDatabase
```
### Now lets import this in index.js 
```
const express = require('express')
const app = express()
require("dotenv").config();
const PORT=process.env.PORT||4000;

//starting server
app.listen(PORT, () => {
    console.log(`Example app listening on port ${PORT}`)
  })
  
// default routes 
app.get('/', (req, res) => {
  res.send('Hello World! Baby')
})
```
# 2. Now lets create first router folder 
## foldername - routes-> todo.js
```
const express=require("express");
const { createTodo } = require("../controllers/createTodo");
const router=express.Router();


// impoort controller 
router.post("/createTodo",createTodo)

module.exports=router;
```
## Now lets mount this in index.js
```
const express = require('express')
const app = express()
require("dotenv").config();
const PORT=process.env.PORT||4000;

// middleware to parse the data 
app.use(express.json())

/// importing routes files
const todoRoutes=require("./routes/todo")
// mounting all the routes
app.use("/api/v1",todoRoutes);

//starting server
app.listen(PORT, () => {
    console.log(`Example app listening on port ${PORT}`)
  })
// default routes 
app.get('/', (req, res) => {
  res.send('Hello World! Baby')
})
```
# 3. Now lets create a config folder to connect the server with database 
## folder name- config-> Database.js
```
const mongoose=require("mongoose")
require('dotenv').config();
const dbConnect=()=>{
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
module.exports=dbConnect;
```
## Now lets import in index.js and connect 
```
const express = require('express')
const app = express()
require("dotenv").config();
const PORT=process.env.PORT||4000;

// middleware to parse the data 
app.use(express.json())

/// importing routes files
const todoRoutes=require("./routes/todo")
// mounting all the routes
app.use("/api/v1",todoRoutes);

//starting server
app.listen(PORT, () => {
    console.log(`Example app listening on port ${PORT}`)
  })
  
/// db connection
const dbConnect=require("./config/Database")
dbConnect();

// default routes 
app.get('/', (req, res) => {
  res.send('Hello World! Baby')
})
```
# 4. Now lets create a Models to define the schema of Database 
## folderName- Models-> todoModel.js
```
const mongoose=require("mongoose");
const todoSchema=new mongoose.Schema(
    {
        title:{
            type:String,
            required:true,
            maxLength:50,
        },
        description:{
            type:String,
            required:true,
            default:Date.now(),
        },
        updatedAt:{
            type:Date,
            required:true,
            default:Date.now()
        }
    }
);
module.exports=mongoose.model("todoSchema",todoSchema);
```
# 5. Now lets create a controller and import models
## controller is created to write the business logic of every routes 
### folder Name - Controllers-> createTodo.js
```
// import schema 
const Todo=require("../models/Todo")

// defuine route handler 
exports.createTodo=async(req,res)=>{
    try{
      // extract title and description
      const {title,description}=req.body;
      // create new todo object
      const response=await Todo.create({title,description});
      // send a json response with success flag
      res.status(200).json(
        {
            success:true,
            data:response,
            message:"inserted successfully "
        }
      )
    }
    catch(err){
       console.log(err)
       console.log(err)
       res.status(500).json({
        success:false,
        data:"internal server error ",
        message:err.message,

       })
    }
}
```

### Now run Your code with 
```
npm run dev
```



