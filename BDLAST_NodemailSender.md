
# Tutorial to setup mailsender in your MERN stack project
## 1-> install pacakge 
```
npm install nodemailer
```
## 2-> go to .env file and set these below value
password and user from your email security app pass
```
# // simple mail transfer protocol user for mail host here 
MAIL_HOST=smtp.gmail.com
MAIL_USER=guptaravii263@gmail.com
MAIL_PASS=your app pass of 16 character
```
## 3-> now create a folder Named utils and inside this create a file lets name mailSender.js
```
const { default: mongoose } = require("mongoose")
const nodemailer=require("nodemailer")

const mailSender=async (email,title,body)=>{
    try{
        let transpoter=nodemailer.createTransport({
            host: process.env.MAIL_HOST,
            auth:{
                user: process.env.MAIL_USER,
                pass: process.env.MAIL_PASS,
            }
        })

        // send email
        let info=await transpoter.sendMail({
            from:`devcode-By ravi`,
            to:`${email}`,
            subject:`${title}`,
            html:`${body}`,
        })
        console.log(info);
        return info;
    }catch(error){
       console.log(error.message)
    }
}


module.exports=mailSender;
```
## 3-> go to your model folder file and embed this file before export the model 
## add your file and  middleware before export your model
```
const nodemailer = require("nodemailer")
/// embed the mailsender function file 
async function sendvarificationEmail(email,otp){
    try{
       const mailResponse=await mailSender(email,"Verification Email From devcode",otp);
       console.log("email send successfully",mailResponse)
    }catch(error){
        console.log("error occured while sending mailserver",error);
        throw error;
    }
}

otpSchema.pre("save",async function(next){
    await sendvarificationEmail(this.email,this.otp);
    next();
})

```

## Your model file look like this 
````
const mongoose=require("mongoose");
const mailSender = require("../utils/mailSender");

const otpSchema=new mongoose.Schema({
    email:{
        type:String,
        required:true,
    },
    otp:{
        type: String,
        required:true,
    },
    createdAt:{
        type: Date,
        default:Date.now(),
        expires:5*60,
    }
})

/// embed the mailsender function file 
async function sendvarificationEmail(email,otp){
    try{
       const mailResponse=await mailSender(email,"Verification Email From devcode",otp);
       console.log("email send successfully",mailResponse)
    }catch(error){
        console.log("error occured while sending mailserver",error);
        throw error;
    }
}

otpSchema.pre("save",async function(next){
    await sendvarificationEmail(this.email,this.otp);
    next();
})


module.exports=mongoose.model("OTP",otpSchema)
````

# Now you can go and do  testing 
