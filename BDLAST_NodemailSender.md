
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

## 3-> go to your model folder file 
## add your post middleware before export your model
```
fileSchema.post("save",async function(doc){
    // doc is entry created in db 
    try{
        console.log("DOC:",doc);
        // transporter
        let transpoter=nodemailer.createTransport({
            host: process.env.MAIL_HOST,
            auth:{
                user: process.env.MAIL_USER,
                pass: process.env.MAIL_PASS,
            }
        })
        /// send mail
        let info=await transpoter.sendMail({
            from:`CodeHelp-By ravi`,
            to:doc.email,
            subject:" new file uploaded  on cloudinary",
            html:`<h2>Hello Jee</h2>`
        })
      console.log("Info:",info);
    }catch(error){
       console.log(error)
    }
})

```

## Your model file look like this 
````
const mongoose=require("mongoose")
const nodemailer = require("nodemailer");
const fileSchema=new mongoose.Schema({
    name:{
        type:String,
        required:true
    },
    imageUrl:{
        type:String,
    },
    tags:{
        type:String,
    },
    email:{
        type:String,
    }
});

fileSchema.post("save",async function(doc){
    // doc is entry created in db 
    try{
        console.log("DOC:",doc);
        // transporter
        let transpoter=nodemailer.createTransport({
            host: process.env.MAIL_HOST,
            auth:{
                user: process.env.MAIL_USER,
                pass: process.env.MAIL_PASS,
            }
        })
        /// send mail
        let info=await transpoter.sendMail({
            from:`CodeHelp-By ravi`,
            to:doc.email,
            subject:" new file uploaded  on cloudinary",
            html:`<h2>Hello Jee</h2>`
        })
      console.log("Info:",info);
    }catch(error){
       console.log(error)
    }
})



const File=mongoose.model("File",fileSchema)
module.exports=File;
````

# Now you can go and do  testing 
