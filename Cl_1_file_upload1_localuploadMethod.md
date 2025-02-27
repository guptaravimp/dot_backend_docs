# Here we will see the tutorial when client want to upload some file on server 
# first setup backend application till, routes, controller, model, database integration using this below link

https://github.com/guptaravimp/dot_backend_docs/blob/main/workflow-Backendapplication.md
# Now let move ahead to work on file upload
## here is mehtod to upload file on server via client 

![Image](https://github.com/user-attachments/assets/e632a5b2-f8c0-4270-80fc-90704634f700)

# lets first setup both the method 
# 1-> localfileUpload method -> uisng express-fileUpload as see this 
### it is nothing but it is a middleware that help to uplaod and interact with file same as like express.json() that interact with json data 

![Image](https://github.com/user-attachments/assets/05b8d7e3-160f-43c6-b8aa-37d82fb40a45)
## to setup this open index.js file 
### first install
```
npm install express-fileupload
```
### import express-fileupload see the complete code of index.js

```
const express=require("express");
const app=express();
require("dotenv").config();
const PORT=process.env.PORT;


// middleware 
app.use(express.json())
const fileupload=require("express-fileupload")
app.use(fileupload());  // using this middleware we know that we will upload a file on server

/// db connection 
const { connect } = require("./config/database");
connect()

/// routes mounting
const Upload=require("./routes/FileUpload")
app.use("/api/v1/upload",Upload)


/// listing 
app.listen(PORT,(req,res)=>{
    console.log(`server is running on port ${PORT}`)
})

/// default routes 
app.get("/",(req,res)=>{
    res.send("Kaise ho sab theek thaak")
})
```

## 2-> Now lets write the router file for local upload go to routes folder and write the code 
## FileUpload.js
```
const express=require("express");
const { localFileupload } = require("../controllers/fileupload");
const router=express.Router();


router.post("/localFileUpload",localFileupload);

module.exports=router;

```
## -> Now lets write the handler for the localFileupload in controller folder 
## fileupload.js
```
const File=require("../models/File")

exports.localFileupload=async(req,res)=>{
    try{
       ////fetch file
       const file=req.files.file;
       console.log("FIle aa gai jee",file);
        
       /// __dirname-> means current directly in myu this case we are inside controller in fileupload.js file i.e __dirname
       let path=__dirname+"/files/"+Date.now()+`.${file.name.split('.')[1]}`; /// server path
       console.log("Path->",path)


       file.mv(path,(err)=>{
        console.log(err);
       });

       res.json({
        success:true,
        message:"Local file uploaded successfully"
       })
    }
    catch(error){
     console.log(error)
    }
}
```

## see the logic and explanation of localfileupload handler or controller .

![Image](https://github.com/user-attachments/assets/399a5111-07e3-43f5-a2fc-20ed9a02a776)

## testing 
![Image](https://github.com/user-attachments/assets/d3294962-edac-43f4-81eb-319500b1323d)

## lets see he testing 
### go to the postman and  path of
```
http://localhost:4000/api/v1/upload/localFileUpload
```
### and select form-data
#### add key and select file and upload file from local computer 
## see this 

![Image](https://github.com/user-attachments/assets/032cbe6e-0186-469f-9fc5-14633379c9f1)

## Now send post request 
## you can see that file is added in controller files folder see this 
![Image](https://github.com/user-attachments/assets/b351d18b-5d9d-4a63-9b22-2db1590ca907)







