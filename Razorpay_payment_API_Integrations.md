
# To check about Razorpay API Integration See below docs also 

https://razorpay.com/docs/partners/aggregators/partner-auth/payment-gateway/

https://razorpay.com/docs/payments/orders/

https://razorpay.com/docs/payments/optimizer/capture-refund-settings/

## Documentation Notes 

![Image](https://github.com/user-attachments/assets/d6311836-edc8-423b-b1f5-e4a485a92a2d)
![Image](https://github.com/user-attachments/assets/fdbcde5f-9948-442f-b049-fbfb36de0b2f)
![Image](https://github.com/user-attachments/assets/1fe40b24-9546-4fdb-a098-acb94277e6af)
![Image](https://github.com/user-attachments/assets/a71c617c-db7e-4e3f-b6a6-0e50383c1b0d)
![Image](https://github.com/user-attachments/assets/027d540e-cead-44a4-a108-f1369b2bcea3)


# 1-> Install setup razorpay 
https://razorpay.com/docs/payments/server-integration/nodejs/
```
npm i razorpay
```
# Step 1: Create an Order Using Orders API
## Go to your config folder create a file name (razorpay.js) and add below code means initialize the instance of razorpay
```
const Razorpay=require("razorpay")
require("dotenv").config()
exports.instance=new Razorpay({
    key_id: process.env.RAZORPAY_KEY,
    key_secret: process.env.RAZORPAY_SECRET,
})
```

# Step 2 -> require means import in Payment.js Controller that you have to create inside controller folder 
## we require User model, Course model , mail sender , and instance of razorpay inside this Payment.js file 
```
const {instance}=require("../config/razorpay")
const Course=require("../models/Course")
const mailSender = require("../utils/mailSender")
const User=require("../models/User")
const courseEnrollemntEmail=require("../mail/courseEnrollementEmail") // to send desired format email 
```

# Step 3-> Now capture payment and initiate the razorpay order 
## 3.1-> create a capturePayment controller 
```
const {instance}=require("../config/razorpay")
const Course=require("../models/Course")
const mailSender = require("../utils/mailSender")
const User=require("../models/User")
const courseEnrollemntEmail=require("../mail/courseEnrollementEmail") // to send desired format email 


// capture the payment and initiate the razorpay order 
exports.capturePayment=async(req,res)=>{
      try{


      }catch(){


      }

}
```
## 3.2-> according to above given all process write the capturePayment and initiate the order of payment 
```
const {instance}=require("../config/razorpay")
const Course=require("../models/Course")
const mailSender = require("../utils/mailSender")
const User=require("../models/User")
const courseEnrollemntEmail=require("../mail/courseEnrollementEmail") // to send desired format email 


// capture the payment and initiate the razorpay order 
exports.capturePayment=async(req,res)=>{
    
    try{
     // get course id and user id 
    const {course_id}=req.body;
    const userId=req.user.id;

    // validation\
    // vald course id 
    if(!course_id){
        return res.json({
            success:false,
            message:"Please provide valid course id"
    })
    }
    // valid course detail 
    let course;
    try{
        course=await Course.findById(course_id)
        if(!course){
            return res.json({
                success:false,
                message:"could not find the course "
            })
        }
        // user already pay for the samecourse 
        // convert userid to object id 
        const uid=new mongoose.Types.ObjectId(userId)
        // check that object id is already present in course or not 
        if(course.studentEnrolled.includes(uid)){ // check in Course model 
            return res.status(200).json({
                success:false,
                message:"Student is already enrolled "
            })
        }
    } catch(error){
        console.log(error)
        return res.status(500).json({
            success:false,
            message:error.message,
        })
    }
    
    // order create 
    const amount=course.price;
    const currency="INR";
    const options={
        amount:amount*100,
        currency,
        receipt:Math.random(Date.now()).toString(),
        notes:{
            courseId:course_id,
            userId,
        }
    };
    try{
        // initiate the payment using razorpay
        const paymentResponse=await instance.orders.create(options)
        console.log(paymentResponse);
         // return response 
         return res.status(200).json({
            success:true,
            courseName: course.courseName,
            courseDescription: course.courseDescription,
            thumbnail: course.thumbnail,
            orderId: paymentResponse.id,
            currency: paymentResponse.currency,
            amount: paymentResponse.amount
         })
    }catch(error){
        console.log(error);
        return res.status(500).json({
            success:false,
            message:"COuld not initate order ",
        })
    }
       
}catch(error){
    console.log(error)
    return res.status(500).json({
        success:false,
        message:error.message,
    })
}
   
}

```
## 3.3-> Now after this Razorpay and user bank communicate with each other and do a complete tranaction 
### after a complete transaction razorpay send a signature that include the secret key provided by admin of account to verify the payment 
### Now lets write a controller for verifySignature 
````
const {instance}=require("../config/razorpay")
const Course=require("../models/Course")
const mailSender = require("../utils/mailSender")
const User=require("../models/User")
const courseEnrollemntEmail=require("../mail/courseEnrollementEmail") // to send desired format email 


// capture the payment and initiate the razorpay order 
exports.capturePayment=async(req,res)=>{
    
    try{
     // get course id and user id 
    const {course_id}=req.body;
    const userId=req.user.id;

    // validation\
    // vald course id 
    if(!course_id){
        return res.json({
            success:false,
            message:"Please provide valid course id"
    })
    }
    // valid course detail 
    let course;
    try{
        course=await Course.findById(course_id)
        if(!course){
            return res.json({
                success:false,
                message:"could not find the course "
            })
        }
        // user already pay for the samecourse 
        // convert userid to object id 
        const uid=new mongoose.Types.ObjectId(userId)
        // check that object id is already present in course or not 
        if(course.studentEnrolled.includes(uid)){ // check in Course model 
            return res.status(200).json({
                success:false,
                message:"Student is already enrolled "
            })
        }
    } catch(error){
        console.log(error)
        return res.status(500).json({
            success:false,
            message:error.message,
        })
    }
    
    // order create 
    const amount=course.price;
    const currency="INR";
    const options={
        amount:amount*100,
        currency,
        receipt:Math.random(Date.now()).toString(),
        notes:{
            courseId:course_id,
            userId,
        }
    };
    try{
        // initiate the payment using razorpay
        const paymentResponse=await instance.orders.create(options)
        console.log(paymentResponse);
         // return response 
         return res.status(200).json({
            success:true,
            courseName: course.courseName,
            courseDescription: course.courseDescription,
            thumbnail: course.thumbnail,
            orderId: paymentResponse.id,
            currency: paymentResponse.currency,
            amount: paymentResponse.amount
         })
    }catch(error){
        console.log(error);
        return res.status(500).json({
            success:false,
            message:"COuld not initate order ",
        })
    }
       
}catch(error){
    console.log(error)
    return res.status(500).json({
        success:false,
        message:error.message,
    })
}
   
}


/// verify signature of razorpay and server 

exports.verifySignature=async(req,res)=>{


}
````

## 3.5-> Now according to above given documentation in page write the verify signature 
```
const {instance}=require("../config/razorpay")
const Course=require("../models/Course")
const mailSender = require("../utils/mailSender")
const User=require("../models/User")
const courseEnrollemntEmail=require("../mail/courseEnrollementEmail") // to send desired format email 


// capture the payment and initiate the razorpay order 
exports.capturePayment=async(req,res)=>{
    
    try{
     // get course id and user id 
    const {course_id}=req.body;
    const userId=req.user.id;

    // validation\
    // vald course id 
    if(!course_id){
        return res.json({
            success:false,
            message:"Please provide valid course id"
    })
    }
    // valid course detail 
    let course;
    try{
        course=await Course.findById(course_id)
        if(!course){
            return res.json({
                success:false,
                message:"could not find the course "
            })
        }
        // user already pay for the samecourse 
        // convert userid to object id 
        const uid=new mongoose.Types.ObjectId(userId)
        // check that object id is already present in course or not 
        if(course.studentEnrolled.includes(uid)){ // check in Course model 
            return res.status(200).json({
                success:false,
                message:"Student is already enrolled "
            })
        }
    } catch(error){
        console.log(error)
        return res.status(500).json({
            success:false,
            message:error.message,
        })
    }
    
    // order create 
    const amount=course.price;
    const currency="INR";
    const options={
        amount:amount*100,
        currency,
        receipt:Math.random(Date.now()).toString(),
        notes:{
            courseId:course_id,
            userId,
        }
    };
    try{
        // initiate the payment using razorpay
        const paymentResponse=await instance.orders.create(options)
        console.log(paymentResponse);
         // return response 
         return res.status(200).json({
            success:true,
            courseName: course.courseName,
            courseDescription: course.courseDescription,
            thumbnail: course.thumbnail,
            orderId: paymentResponse.id,
            currency: paymentResponse.currency,
            amount: paymentResponse.amount
         })
    }catch(error){
        console.log(error);
        return res.status(500).json({
            success:false,
            message:"COuld not initate order ",
        })
    }
       
}catch(error){
    console.log(error)
    return res.status(500).json({
        success:false,
        message:error.message,
    })
}
   
}


/// verify signature of razorpay and server 

exports.verifySignature=async(req,res)=>{
    const webhookSecret="12345678"; /// on our server lets assume dummy 
    const signature=req.headers["x-razorpay-signature"] // that is behaviour of razorpay to send the signature key or secret key
    const shasum=crypto.createHmack("sha256",webhookSecret)
    // Hased based message authentication code (Hmack) 
    // SHA( secure hashing algorithm )
    shasum.update(JSON.stringify(req.body));
    const digest=shasum.digest("hex");

    // now match digest and signature 
    if(signature===digest){
        console.log("Payment is authorized ")
        /// now add student to enrolled course everywhere 
        // as we send user id and course id in oayment response in notes 
        // location of user_is and course_id is -> req.body.payload.paymentResponse.entity.notes
        const {courseId,userId}=req.body.payload.entity.notes;
        try{
            // fullfill the action 
            // find the course and enroll the student in it 
            const enrolledCourse=await Course.findOneAndUpdate(
                {id:courseId},
                {$push:{studentEnrolled:userId}},  // see the model of course
                {new:true}
            )
            if(!enrolledCourse){
                return res.status(500).json({
                    success:false,
                    message:"Course Not found",
                })
            }
            console.log(enrolledCourse)

            // find the student and add the course in list of  enrolledcourses in dashboard of student 
            const enrolledStudent=await User.findByIdAndUpdate(
                {id:userId},
                {$push:{courses:courseId}}, // see the model of useer 
                {new:true}
            )
            console.log(enrolledStudent);

            // mailed send of course enrollment 
            const emailResponse=await mailSender(
                enrolledStudent.email,
                "Congratulatio from devcode" ,
                "Congratulation You are onboarded in New Course of devcode ",

            );
            console.log(emailResponse);
            return res.status(200).json({
                success:true,
                message:"Signature verified and course added "
            })

        }catch(error){
           console.log(error);
           return res.status(500).json({
            success:false,
            message:error.message
           })
        }
    }else{
        return res.status(400).json({
            success:false,
            message:"Invalid request"
           })
    }
}
```
# Now the complete code of this is given below 
```
const {instance}=require("../config/razorpay")
const Course=require("../models/Course")
const mailSender = require("../utils/mailSender")
const User=require("../models/User")
const courseEnrollemntEmail=require("../mail/courseEnrollementEmail") // to send desired format email 


// capture the payment and initiate the razorpay order 
exports.capturePayment=async(req,res)=>{
    
    try{
     // get course id and user id 
    const {course_id}=req.body;
    const userId=req.user.id;

    // validation\
    // vald course id 
    if(!course_id){
        return res.json({
            success:false,
            message:"Please provide valid course id"
    })
    }
    // valid course detail 
    let course;
    try{
        course=await Course.findById(course_id)
        if(!course){
            return res.json({
                success:false,
                message:"could not find the course "
            })
        }
        // user already pay for the samecourse 
        // convert userid to object id 
        const uid=new mongoose.Types.ObjectId(userId)
        // check that object id is already present in course or not 
        if(course.studentEnrolled.includes(uid)){ // check in Course model 
            return res.status(200).json({
                success:false,
                message:"Student is already enrolled "
            })
        }
    } catch(error){
        console.log(error)
        return res.status(500).json({
            success:false,
            message:error.message,
        })
    }
    
    // order create 
    const amount=course.price;
    const currency="INR";
    const options={
        amount:amount*100,
        currency,
        receipt:Math.random(Date.now()).toString(),
        notes:{
            courseId:course_id,
            userId,
        }
    };
    try{
        // initiate the payment using razorpay
        const paymentResponse=await instance.orders.create(options)
        console.log(paymentResponse);
         // return response 
         return res.status(200).json({
            success:true,
            courseName: course.courseName,
            courseDescription: course.courseDescription,
            thumbnail: course.thumbnail,
            orderId: paymentResponse.id,
            currency: paymentResponse.currency,
            amount: paymentResponse.amount
         })
    }catch(error){
        console.log(error);
        return res.status(500).json({
            success:false,
            message:"COuld not initate order ",
        })
    }
       
}catch(error){
    console.log(error)
    return res.status(500).json({
        success:false,
        message:error.message,
    })
}
   
}


/// verify signature of razorpay and server 

exports.verifySignature=async(req,res)=>{
    const webhookSecret="12345678"; /// on our server lets assume dummy 
    const signature=req.headers["x-razorpay-signature"] // that is behaviour of razorpay to send the signature key or secret key
    const shasum=crypto.createHmack("sha256",webhookSecret)
    // Hased based message authentication code (Hmack) 
    // SHA( secure hashing algorithm )
    shasum.update(JSON.stringify(req.body));
    const digest=shasum.digest("hex");

    // now match digest and signature 
    if(signature===digest){
        console.log("Payment is authorized ")
        /// now add student to enrolled course everywhere 
        // as we send user id and course id in oayment response in notes 
        // location of user_is and course_id is -> req.body.payload.paymentResponse.entity.notes
        const {courseId,userId}=req.body.payload.entity.notes;
        try{
            // fullfill the action 
            // find the course and enroll the student in it 
            const enrolledCourse=await Course.findOneAndUpdate(
                {id:courseId},
                {$push:{studentEnrolled:userId}},  // see the model of course
                {new:true}
            )
            if(!enrolledCourse){
                return res.status(500).json({
                    success:false,
                    message:"Course Not found",
                })
            }
            console.log(enrolledCourse)

            // find the student and add the course in list of  enrolledcourses in dashboard of student 
            const enrolledStudent=await User.findByIdAndUpdate(
                {id:userId},
                {$push:{courses:courseId}}, // see the model of useer 
                {new:true}
            )
            console.log(enrolledStudent);

            // mailed send of course enrollment 
            const emailResponse=await mailSender(
                enrolledStudent.email,
                "Congratulatio from devcode" ,
                "Congratulation You are onboarded in New Course of devcode ",

            );
            console.log(emailResponse);
            return res.status(200).json({
                success:true,
                message:"Signature verified and course added "
            })

        }catch(error){
           console.log(error);
           return res.status(500).json({
            success:false,
            message:error.message
           })
        }
    }else{
        return res.status(400).json({
            success:false,
            message:"Invalid request"
           })
    }
}
```
