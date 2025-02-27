# In this tutorial we will see about the signup logic and process of code 
![Image](https://github.com/user-attachments/assets/54ca764c-f396-4246-a391-2f2be64160c4)

## lets we are going to work on signup controller
## create Auth.js file in controllers folder
## inside Auth.js
# Step1-> import model and write a exports function of signup
```
const User = require("../models/User");
exports.signup=async(req,res)=>{
    try{
       
    }
    catch(error){
       
    }
}
```
# According to image lets write all the process of signup
# step1->fetch the data from req.body or say ui 
```
const User = require("../models/User");
exports.signup=async(req,res)=>{
    try{
         // fetching the data from user
         const {name,email,password,role}=req.body;
        
    }
    catch(error){
       
    }
}
```

# step2-> email and password validation 
```
const User = require("../models/User");
exports.signup=async(req,res)=>{
    try{
         // fetching the data from user
         const {name,email,password,role}=req.body;
         /// validation
         if(!email || password ){
             return res.status(400).json({
              success:false,
              message:"Please fill all the details carefully"
            })
         }
    }
    catch(error){
       
    }
}
```
# Step3-> check email for already exist or not 
```
const User = require("../models/User");
exports.signup=async(req,res)=>{
    try{
         // fetching the data from user
         const {name,email,password,role}=req.body;
         /// validation
         if(!email || password ){
             return res.status(400).json({
              success:false,
              message:"Please fill all the details carefully"
            })
         }
         // if user already exist 
         const existingUser=await User.findOne({email});
         if(existingUser){
            return res.status(400).json({
                success:false,
                message:"User already exist"
            })
         }
    }
    catch(error){
       
    }
}
```
## if not exist hashed the password using the bcrypt library 
# step4-> use Bcrypt library to hashed the password so 

![Image](https://github.com/user-attachments/assets/e2e89837-5d12-4b7f-9728-bb2eee3f1106)

## first install bcrypt library 
```
npm install bcrypt
```
## import this in file by 
```
const bcrypt=require("bcrypt");
```
## see the complete code 
```
const User = require("../models/User");
const bcrypt=require("bcrypt");
exports.signup=async(req,res)=>{
    try{
         // fetching the data from user
         const {name,email,password,role}=req.body;
         /// validation
         if(!email || password ){
             return res.status(400).json({
              success:false,
              message:"Please fill all the details carefully"
            })
         }
         // if user already exist 
         const existingUser=await User.findOne({email});
         if(existingUser){
            return res.status(400).json({
                success:false,
                message:"User already exist"
            })
         }

         // secure password using bcrypt library 
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
    }
    catch(error){
       
    }
}
```
# Step 4-> create a db entry of that user in our database 
```
 const user=await User.create({
            name,email,password:hashedPassword,role
})
```

# see this complete code of sign up 
```
const User = require("../models/User");
const bcrypt=require("bcrypt");
exports.signup=async(req,res)=>{
    try{
         // fetching the data from user
         const {name,email,password,role}=req.body;
         /// validation
         if(!email || password ){
             return res.status(400).json({
              success:false,
              message:"Please fill all the details carefully"
            })
         }
         // if user already exist 
         const existingUser=await User.findOne({email});
         if(existingUser){
            return res.status(400).json({
                success:false,
                message:"User already exist"
            })
         }

         // secure password using bcrypt library 
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
          /// return response 
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
```


