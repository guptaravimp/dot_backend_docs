# Now lets see the login logic and flow 

![Image](https://github.com/user-attachments/assets/13ad6225-faa9-40d9-b950-6ad22e20e1cc)
# lets do it step By step 
# Step-> first we have to create a login controller in conyroller file
## loginController.js but in out project it is in Auth.js
```
const User = require("../models/User");
exports.login=async(req,res)=>{
    try{
      

    }catch(error){
       
    }
}
```
# Step-1 fetch the email and password intered by user in req.body mean on ui 
```
const User = require("../models/User");
exports.login=async(req,res)=>{
    try{
       // fetching first email and password from req.body
        const {email,password}=req.body;

    }catch(error){
       
    }
}
```
# Step2-> validation of email and password
```
const User = require("../models/User");
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
    }catch(error){
       
    }
}
```
# Step-3 check first user is registed is not 
```
const User = require("../models/User");
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

    }catch(error){
       
    }
}
```

# Step4- user present in db now check intered password and db hashed password are same or not 
## if password is same the 
## create a jwt token-> add token to the user details -> make password undefined-> create cookies to sent the token to client 
## Now lets first see about jwt token and cookies 
## JWT token

![Image](https://github.com/user-attachments/assets/572163c6-0c04-4ff6-894a-9fa3131d4497)
![Image](https://github.com/user-attachments/assets/be26133b-4891-4a82-b06a-fe505d93df76)

## cookies
![Image](https://github.com/user-attachments/assets/fb86485f-3fae-49cb-a56f-123a54719412)

## How it work see

![Image](https://github.com/user-attachments/assets/2e46a393-e900-4868-8ff5-7ef7de6358bb)

```

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
```




























































