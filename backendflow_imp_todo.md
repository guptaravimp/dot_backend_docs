## Here You will see the backend flow folder structure 

![Image](https://github.com/user-attachments/assets/37df66d7-9787-47d2-ac8f-9affc85dfcbf)

## Now lets see How 
# 1. first Setup backend Project using below link 
https://github.com/guptaravimp/Backend_Docs_/blob/main/Setup_Into.md
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

