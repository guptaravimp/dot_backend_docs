# In this tutorial we are going to learn about the Authentication and Authorization 
# LogIn and Signup in Backend development using (express+MongoDb+node.js)
# Step-1 Setup Backend application project
# 1. Initialize the Project
## Create a new directory for your project:
```
mkdir backend-project
cd backend-project
```
## 2. Initialize a new Node.js project:(in terminal Inside backend_project folder name )
```
npm init -y
```
## 3- Installing express
```
 npm install express
```
## 4- Installing nodemon
### Nodemon is a development tool that automatically restarts your Node.js application whenever it detects changes in the source files. Without nodemon, you'd need to manually stop and restart the server every time you make changes to your code.

```
npm install nodemon
```
## 5- Create Index.js or say Server.js file to write our backend code 
### entry point: (index.js)

## 6- Now adding code of nodemon in (script of package.json )
### adding the server.js file to update on every changes with nodemon
```
 "scripts": {
    "dev":"nodemon index.js",
    "test": "echo \"Error: no test specified\" && exit 1"
  },
```
## 7 Now write this code in server.js or index.js
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
## 5 Now run this backend in Node.js
```
npm run dev
```
## see the port ......
## Go to browser search localhost:3000 

## 6 now let first create a .env file to store our secret information 
### .env
```
PORT=4000
DATABASE_URL="mongodb+srv://guptaravii263:d9UxkunX03arxibz@tododb.muyfv.mongodb.net/AuthDatabase"
JWT_SECRET=ravi
```
## 7 Now to use this first install a dotenv package 
```
npm install dotenv
```
## 8 now to use this first inport in file and then use it like 
```
require('dotenv').config();
const PORT = process.env.PORT;
```
### Now you can see the use in index.js 
```
const express = require('express');
const app = express()

require('dotenv').config();
const PORT = process.env.PORT;

app.listen(PORT, () => {
  console.log(`Example app listening on port ${PORT}`)
})

// default routes 
app.get('/', (req, res) => {
    res.send('Hello World!')
  })
```
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
#### Database url comes from .env file using dotenv library
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
## 4th is create a controllers folder  inside our backend folder
#### controllers folder contain the business logic that on which router which business or controller will handle the sopecific routes 
### 4.1 Auth.js
```
const User = require("../models/User");
const bcrypt=require("bcrypt");
const jwt=require("jsonwebtoken")
require("dotenv").config()
/// signup business login
exports.signup=async(req,res)=>{
    try{
         // fetching the data from user
         const {name,email,password,role}=req.body;
         // if user already exist 
         const existingUser=await User.findOne({email});
         if(existingUser){
            return res.status(400).json({
                success:false,
                message:"User already exist"
            })
         }

         // secure password 
         let hashedPassword;
         try{
            //.hash( what you want to hash, no of round)
            hashedPassword=await bcrypt.hash(password,10);
          }catch(error){
            return res.status(500).json({
                success:false,
                message:"error in hashing the password "
            })
          }
          /// create entry of user
          const user=await User.create({
            name,email,password:hashedPassword,role
          })
          return res.status(200).json({
            success:true,
            message:"User created successfully"
          })

    }
    catch(error){
        console.log(error);
        return res.status(500).json({
            success:false,
            message:"User is not created please try again"
        })
    }
}


/// login business logic
exports.login=async(req,res)=>{
    try{
        // fetching first email and password from req.body
        const {email,password}=req.body;
        /// validatiopn on email and password 
        if(!email || !password){
            return res.status(400).json({
              success:false,
              message:"Please fill all the details carefully"
            })
        }

        /// check for registred user 
        const user=await User.findOne({email});
        if(!user){
            return res.status(401).json({
                success:false,
                message:"user  is not registered"
            })
        }


        const payload={
            email:user.email,
            id:user._id,
            role:user.role,

        }
        //// verify password and generate the JWT token 
        if(await bcrypt.compare(password,user.password)){

            // password matched then
            // create a jwt token
            let token= jwt.sign(payload,
                process.env.JWT_SECRET,
                {
                    expiresIn:"2h",
                });
                // adding token in user details or model 
                user._doc.token = token;
                // remove password from user object not from database 
                user.password=undefined;
                // cookies
                const options={
                    //  expires:new Date(Date.now()+3*24*60*60*1000),
                     expires:new Date(Date.now()+30000),
                     httpOnly:true,
                }
                res.cookie("token",token,options).status(200).json({
                    success:true,
                    token,
                    user,
                    message:"User loged in  successfully"
                })

        }else{
            res.status(403).json({
                success:false,
                message:"Password incorrect"
            })
        }
    }catch(error){
       console.log(error);
       return res.status(500).json({
        success:false,
        message:"Login failure"
       })
    }
}
```
#### as we see we call both the controller in router file and inport both these controller to handle the login and signup route previously
```
const express=require("express");
const { login, signup } = require("../controllers/Auth");
const router=express.Router();

router.post("/login",login);
router.post("/signup",signup)

module.exports=router;
```

#### same as we create routes in router folder and write a controller for that specific routes and import it in router folder 

## 4.2 see the login and signup business logic here 
### Signup-> 
///// redme file of signup
### Login-> 
/// readme file for login


## 4.3 as now we have  a better understanding of bcrypt libraries, jwt token, and cookies 
//// See the docs notes 
bcrypt libarary->
jwt token->
cookies->

# Step->3 Creating a middleware folder
## need of middleware
/// docs
## auth.js
### this folder is used to create a middleware for authentication and authorization
### lets create a auth login, isStudentlogic, isAdmin logic 
#### auth logic-> help to retreive the payload from the jwt token beacuse using payload we can find the role and make a authorization for student and admin etc
```
/// middleware -> auth, isStudent, isAdmin

const jwt=require("jsonwebtoken");
require("dotenv").config();
// how to check student hai admin hai ya normal 
// as here we import token and in token we know payload and in payload we send the role here we can see that isstudent or admin 
/// here next is used to know after this middleware which middleware you have to implement or run
exports.auth=(req,res,next)=>{
    try{
     ///extract jwt token 
     // we can fetch from cookie, req.body ,header
    //  1 way is pending using header 
    // firstway, second, third
    // see in index.js 
    // imp  if we dont include cookie parser then cookies.token will give error 
    // for req.body.token we use body-parser i.e express.json()
    // const token=req.body.token || req.cookies.token;
    const token= req.header("Authorization").replace("Bearer ","") ;
    console.log(token);
     if(!token){
        return res.status(401).json({
            success:false,
            message:"token Missing"
        });
     }
     // verify the token
     try{
        // verifying the token and decode the token so that we can extract role
        const payload=jwt.verify(token,process.env.JWT_SECRET);
        console.log(payload);
        req.user=payload;  /// here we store our payload
     }catch(error){
         return res.status(401).json({
            success:false,
            message:"token is invalid"
         })
     }
     next();  /// go to next middleware
    }catch(error){
        return res.status(401).json({
            success:false,
            message:"Somethoing went wrong while verifying token "
         })
    }
}



exports.isStudent=(req,res,next)=>{
    try{
        /// access role from stored payload in req.user
        if(req.user.role!="Student"){
            return res.status(400).json({
                success:false,
                message:"This is a protected routes for student "
            })
        }
        next();
    }catch(error){
      return res.status(500).json({
        success:false,
    message:"User role is not matching "  
    })
    }
}


exports.isAdmin=(req,res,next)=>{
    try{
        /// access role from stored payload in req.user
        if(req.user.role!="Admin"){
            return res.status(400).json({
                success:false,
                message:"This is a protected routes for Admin "
            })
        }
        next();
    }catch(error){
      return res.status(500).json({
        success:false,
    message:"User role is not matching "  
    })
    }
}
```
## See the complete logic of this code here 
//// readme file of auth, isStudent, admin logic here

# Step 4-> Now add this middleware where we need this 
### I mean lets use in during login and signup 
### go to router folder and file and add this during access of student, admin and etc routed paths
### routes folder -> user.js
```
const express=require("express");
const { login, signup } = require("../controllers/Auth");
const router=express.Router();
const {auth,isStudent,isAdmin}=require("../middlewares/auth");


router.post("/login",login);
router.post("/signup",signup)

// testing routes
router.get("/test",auth,(req,res)=>{
    res.json({
        success:true,
        message:"Welcome to the protected routes for Tests"
    })
})

//Protected routes  
/// we have to know the routes kaun kaun sa middleware es routes par aayega aur authentisity check karega  
router.get("/student",auth,isStudent,(req,res)=>{
    res.json({
        success:true,
        message:"Welcome to the protected routes for Student"
    })
})

router.get("/admin",auth,isAdmin,(req,res)=>{
    res.json({
        success:true,
        message:"Welcome to the protected routes for Admin"
    })
})


module.exports=router;
```

### as you see here when student want to go on his dashboard first their authorzation takje place, next() is isStudent logic then go to student dashboard and same for admin 
### first authorization of user take place and then check isAdmin if yes then go to res
### see the flow
/// diagram of how middleware works

# step-5 why we send a id in payload if you see in the controller 
## reason of this because using this we can access all the detail through id in payload like user information and photo etc see this 
## routes folder->user.js
```
const express=require("express");
const { login, signup } = require("../controllers/Auth");
const router=express.Router();
const {auth,isStudent,isAdmin}=require("../middlewares/auth");
const User = require("../models/User");

router.post("/login",login);
router.post("/signup",signup)

// testing routes
router.get("/test",auth,(req,res)=>{
    res.json({
        success:true,
        message:"Welcome to the protected routes for Tests"
    })
})
//Protected routes  

/// we have to know the routes kaun kaun sa middleware es routes par aayega aur authentisity check karega  
router.get("/student",auth,isStudent,(req,res)=>{
    res.json({
        success:true,
        message:"Welcome to the protected routes for Student"
    })
})

router.get("/admin",auth,isAdmin,(req,res)=>{
    res.json({
        success:true,
        message:"Welcome to the protected routes for Admin"
    })
})
/// why we pass the id in payload 
router.get("/getEmail",auth,async(req,res)=>{
    try{
         const id=req.user.id;
         const user=await User.findById({id});
         res.status(200).json({
            success:true,
            user:user,
            message:"Welcome to the email routes"
         })
    }catch(error){
        console.log(error);
        return res.status(500).json({
        success:false,
        message:"Login failure"
       })
    }
})

module.exports=router;
```
### as previous file we see that inside payload we have id inside (req.user) we access the payload because using this we decrypt the jwt token and access the payload 

