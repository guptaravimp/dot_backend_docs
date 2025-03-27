# first setup backend application till, routes, controller, model, database integration using this below link

https://github.com/guptaravimp/dot_backend_docs/blob/main/workflow-Backendapplication.md

## Now let move ahead to work on file upload using cloudinary
## 1->first install this 
```
npm install cloudinary
```
## make afile inside config that name is (cloudinary.js)
### 2->set the cloud name , api_key, and api_secret that is save inside .env file and that dataa is initialize in .env from cloudinary dashboard
```
const cloudinary = require('cloudinary').v2

exports.cloudinaryConnect=()=>{
    try{
        cloudinary.config({
            cloud_name:process.env.CLOUD_NAME,
            api_key:process.env.API_KEY,
            api_secret:process.env.API_SECRET,
        })
    }
    catch(error){
            console.log(error)
    }
}
```
## first install file upload 
```
npm i express-fileupload
```
## 3-> now connect it or say mount in (index.js)
```
const fileupload=require("express-fileupload")
app.use(fileupload({
    useTempFiles:true,
    tempFileDir:'/tmp/'
}));  // using this middleware we know that we will upload a file on server

/// mounting the cloudinary 
const cloudinary = require("./config/cloudinary");
cloudinary.cloudinaryConnect();
```

## 4->now lets create a routes for this 
```
const express=require("express");
const { localFileupload, imageUpload, videoUpload,imageSizeReducer } = require("../controllers/fileupload");
const router=express.Router();


router.post("/localFileUpload",localFileupload);
router.post("/imageUpload",imageUpload);
router.post("/videoUpload",videoUpload);
router.post("/imageSizeReducer",imageSizeReducer)



module.exports=router;

```

## 5-> now respective controller for this
```
const File=require("../models/File")
const cloudinary = require('cloudinary').v2
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



function isFiletypeSupported(type, supportedType) {
    return supportedType.includes(type);
}



async function uploadFileToCloudinary(file,folder){
    const options={folder};
    options.resource_type="auto"
    return await cloudinary.uploader.upload(file.tempFilePath,options);
}

exports.imageUpload=async(req,res)=>{
    try{

        /// data fetch
        const {name,tags,email}=req.body;
        console.log(name,tags,email);
        const file=req.files.imagefiles;
        console.log(file);
        // const file=req.files.imagefiles;

        /// validation 
        const supportedType=["jpg","png","jpeg"]
        const fileType=file.name.split('.')[1].toLowerCase();

        if (!isFiletypeSupported(fileType, supportedType)) {
            return res.status(400).json({
                success: false,
                message: "File type is not supported"
            });
        }
        
        
        /// file format supported 
        const response=await uploadFileToCloudinary(file,"techravibusiness");
        console.log("response:",response);

        /// db entry cretaed 
        const fileData=await File.create({
           name,
           tags,
           email,
           imageUrl:response.secure_url,
        })

       res.json({
        success:true,
        message:"image uploaded successfully",
        imageUrl:response.secure_url,
       })
       
    }
    catch(error){
        console.log(error)
        res.status(400).json({
            success:false,
            message:"something went rong "
        })
    }
}


exports.videoUpload=async(req,res)=>{
    try{
          
        /// data fetch
        const {name,email,tags}=req.body;
        console.log(name,tags,email)
        // videoFile is the key name 
        const file=req.files.videoFile;

        /// validation
        const supportedType=["mov","mp4"]
        const fileType = file.name.split('.').pop().toLowerCase();

        if (!isFiletypeSupported(fileType, supportedType)) {
            return res.status(400).json({
                success: false,
                message: "File type is not supported"
            });
        
         }
         /// file format supported 
        const response=await uploadFileToCloudinary(file,"techravibusiness");
        console.log("response hai:",response);
        /// db entry cretaed 
        const fileData=await File.create({
            name,
            tags,
            email,
            videoUrl:response.secure_url,
         })
        res.json({
            success:true,
            message:"image uploaded successfully",
            videoUrl:response.secure_url,
        })
}
    catch(error){
        console.log(error)
        res.status(400).json({
            success:false,
            message:"Something wennt rong"
        })
    }
}

async function uploadFileToCloudinary(file,folder,quality){
    const options={folder};
    if(quality){
        options.quality=quality;
    }
    options.resource_type="auto"
    return await cloudinary.uploader.upload(file.tempFilePath,options);
}
exports.imageSizeReducer=async(req,res)=>{
    try{

        /// data fetch
        const {name,tags,email}=req.body;
        console.log(name,tags,email);


        const file=req.files.imagefiles;
        console.log(file);
        // const file=req.files.imagefiles;

        /// validation 
        const supportedType=["jpg","png","jpeg"]
        const fileType=file.name.split('.')[1].toLowerCase();

        if (!isFiletypeSupported(fileType, supportedType)) {
            return res.status(400).json({
                success: false,
                message: "File type is not supported"
            });
        }
        
        
        /// file format supported 
        const response=await uploadFileToCloudinary(file,"techravibusiness",90);
        console.log("response:",response);

        /// db entry cretaed 
        const fileData=await File.create({
           name,
           tags,
           email,
           imageUrl:response.secure_url,
        })

       res.json({
        success:true,
        message:"image uploaded successfully",
        imageUrl:response.secure_url,
       })
       
    }
    catch(error){
        console.log(error)
        res.status(400).json({
            success:false,
            message:"something went rong "
        })
    }
}

```

## that is process now do testing using postman

