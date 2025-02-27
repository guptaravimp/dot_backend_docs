# lets see that how Auth middleware work 

![Image](https://github.com/user-attachments/assets/0d50a7bb-5f88-4741-b590-c23dddbe1d3b)
## 1-> Lets see the code of auth middleware
```
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

```

## using this user will authorized bow using this we will see the role and give the access of is isStudent , isAdmin access
![Image](https://github.com/user-attachments/assets/09bf8931-88c0-4e17-9ded-eefe054fe3cb)
## 2-> see the code of isstudent and isadmin middleware
```
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

## 3-> now lets add routes in router files 
```
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
```


## Now you can do testing for all the routes 
