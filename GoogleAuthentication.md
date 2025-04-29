## this tutorial will provide you the complete path for google authentication using Google oauth2.0 with Node.js

## step 1 create a API and get  credential from below link 
https://console.cloud.google.com/welcome?project=geminiauthuser&authus
## step2 see the step by step process from screenshot below

![Image](https://github.com/user-attachments/assets/fd171945-fad0-4168-81b7-1375f3575f50)

![Image](https://github.com/user-attachments/assets/06edeca3-e385-4cc5-9f0f-44106322c94c)

![Image](https://github.com/user-attachments/assets/5f198845-eb51-47c8-ad88-befed42ee6bb)
![Image](https://github.com/user-attachments/assets/26761f00-1f96-4632-b976-7a634a4699c6)
![Image](https://github.com/user-attachments/assets/a1a6f1a7-5f20-4314-9bd9-88da8b744f2d)
![Image](https://github.com/user-attachments/assets/56cb791f-a0b9-4276-9377-e72abe5578c7)
![Image](https://github.com/user-attachments/assets/f105be4d-b80a-4eea-8082-c62a7bcba8fd)
![Image](https://github.com/user-attachments/assets/5a02e0fc-c05e-4267-9f6f-60031176f096)

![Image](https://github.com/user-attachments/assets/f59a13b4-7f66-4463-adff-6654202fe28e)
![Image](https://github.com/user-attachments/assets/3551c614-b9c3-4d1c-886c-fb7796d48cac)
![Image](https://github.com/user-attachments/assets/89c6a263-f5a9-42b1-ab51-a098a0107714)
![Image](https://github.com/user-attachments/assets/834ac1eb-7ef1-4ef2-a251-fa77dc846864)
![Image](https://github.com/user-attachments/assets/982f5b5a-0b49-48a6-9ddd-f7330347f435)
![Image](https://github.com/user-attachments/assets/fee7ea14-955f-4f29-b823-91cf2bfbbabd)

![Image](https://github.com/user-attachments/assets/d0668bf7-9ace-454d-b83b-b56b70924f64)
![Image](https://github.com/user-attachments/assets/caa4d93e-4d0d-471b-a27d-4bada0b68fba)
![Image](https://github.com/user-attachments/assets/cc6ffdc3-59be-4cbf-8a40-c0106c8e8824)
![Image](https://github.com/user-attachments/assets/1e88ac6e-b61b-459b-8dd3-ed022a293f01)
![Image](https://github.com/user-attachments/assets/931be09c-95e1-499b-b8e8-0bfb9985c24c)
![Image](https://github.com/user-attachments/assets/a388e287-c591-400f-a13f-0aeacb89c7e0)


### After this you will get credential 
```
{
  "web": {
    "client_id": " your clien id",
    "project_id": "devcodeapp",
    "auth_uri": "https://accounts.google.com/o/oauth2/auth",
    "token_uri": "https://oauth2.googleapis.com/token",
    "auth_provider_x509_cert_url": "https://www.googleapis.com/oauth2/v1/certs",
    "client_secret": "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",
    "javascript_origins": [
      "http://localhost:3000"
    ]
  }
}

```
## step-3-> backend setup 
### 1-> first create a model of User.js in Model fonder inside the backend folder 
```
const mongoose = require('mongoose');

const UserSchema = new mongoose.Schema({
    name: {
        type: String,
    },
    email: {
        type: String,
    },
    profilePicture: {
        type: String,
    }
});

const UserModel = mongoose.model('users', UserSchema);
module.exports = UserModel;

```
### 2-> create a login controller code 
#### name is (authController.js)
```
const UserModel = require("../models/User");
const { oauth2client } = require("../utils/GoogleConfig");
const jwt=require("jsonwebtoken")
const axios=require("axios")
const googleLogin=async(req,res)=>{
    console.log("Yaha call aai hai ")
    try{
        const {code}=req.query;
        const googleRes=await oauth2client.getToken(code)
        oauth2client.setCredentials(googleRes.tokens)

        const userRes = await axios.get(
            `https://www.googleapis.com/oauth2/v1/userinfo?alt=json&access_token=${googleRes.tokens.access_token}`
          );
          
        console.log("USER LOGIN RESPONSE",userRes.data)
        const {email,name,picture}=userRes.data;
        let user=await UserModel.findOne({email});
        if(!user){
            user=await UserModel.create({
                name,email,profilePicture:picture
            })
        }

        const { _id } = user; // Correct destructure from MongoDB user
        const token = jwt.sign(
          { _id, email },
          process.env.JWT_SECRET,
          {
            expiresIn: process.env.JWT_TIMEOUT
          }
        );
        
        console.log("Token is ", token);
        console.log("user is", user);
        return res.status(200).json({
          success: true,
          token,
          user
        })
        
        return res.status(200).json({
            success:true,
            token,
            user
        })

    }catch(error){
             res.status(500).json({
                success:false,
                message:"Internal Server Error"
             })
    }
  }

  module.exports={googleLogin}
```
### 3-> create a routes for login in file inside the routes folder of Backend 
```


const express = require('express'); 
const getallitem = require('../controllers/getAllItem');
const generate = require('../controllers/generate');
const { googleLogin } = require('../controllers/authController');
const router = express.Router();


router.get("/google", googleLogin);  ///that is route
 

module.exports = router;

```
#### 4-> add the backend credential in.env folde inside backend 
```
GOOGLE_CLIENT_ID="76866074347-cvn76d7vf773dhq574rnebv14ur57un9.apps.googleusercontent.com"
GOOGLE_CLIENT_SECRET="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"
JWT_SECRET="techravi"
JWT_TIMEOUT='12h'
```
#### Now backend work done 


## step4->let see frontend work
### 1-> Install all dependencies
```
npm install @react-oauth/google
```
### 2-> first create Login page 
##### name (GoogleLogin.jsx)
```
import React from 'react'
import googleimage from "../../assets/googleimage.png"
import geminiailogo from "../../assets/gemini.png"
function GoogleLogin() {
 
  return (
    <div className='text-white flex flex-col justify-between items-center  bg-black h-[100vh] w-[100vw]'>
      <div className='w-full h-20%'>
        <img src={geminiailogo} alt="" className='h-24 w-[20%]' />
      </div>
      <div className='h-[80%] w-full flex justify-center items-start'>
        <div className='flex flex-col w-[60%] justify-center items-center  gap-10 '>
          <h1 className='text-3xl'>Welcome Back!</h1>
          <div className='h-16 w-16 border-[1px] border-gray-500 p-3 rounded-[400px]'>
          <img src={googleimage} alt="" />
          </div>
          
          <button onClick={googleLogin} className='flex justify-evenly gap-2 items-center mt-1 border-[1px] p-4 rounded-3xl border-gray-500 hover:bg-gray-900'>
            <img src={googleimage} alt=" " className='h-10 w-10' />
            <p className='text-xl'>Login with google</p>
          </button>
        </div>
      </div>



    </div>
  )
}

export default GoogleLogin

```
### 3-> wrap this login page inside GoogleAuthWrapper see the below code 
#### 
```
import GoogleLogin from './components/common/GoogleLogin';

import { GoogleOAuthProvider } from '@react-oauth/google';


const GoogleAuthWrapper=()=>{
  return (
    <GoogleOAuthProvider clientId='76866074347-cvn76d7vf773dhq574rnebv14ur57un9.apps.googleusercontent.com'>
      <GoogleLogin></GoogleLogin>
    </GoogleOAuthProvider>
  )
}


const router=createBrowserRouter([
  {
    path:"/",
    element: <div> <Home/></div> 
  },
  {
    path:"/login",
    element: <div> <GoogleAuthWrapper/></div> 
  },
  {
    path:"*",
    element: <div> <Error/></div> 
  }
]
```
#### see the complete code of app.jsx
```
import { useState } from 'react'
import { BrowserRouter, RouterProvider, createBrowserRouter } from 'react-router-dom';
import Home from './Pages/Home';
import Navbar from './components/common/Navbar';
import Error from './components/common/Error';
import GoogleLogin from './components/common/GoogleLogin';

import { GoogleOAuthProvider } from '@react-oauth/google';


const GoogleAuthWrapper=()=>{
  return (
    <GoogleOAuthProvider clientId='76866074347-cvn76d7vf773dhq574rnebv14ur57un9.apps.googleusercontent.com'>
      <GoogleLogin></GoogleLogin>
    </GoogleOAuthProvider>
  )
}

const router=createBrowserRouter([
  {
    path:"/",
    element: <div> <Home/></div> 
  },
  {
    path:"/login",
    element: <div> <GoogleAuthWrapper/></div> 
  },
  {
    path:"*",
    element: <div> <Error/></div> 
  }
]
  
)
function App() {

  

  return (
    <div className='mx-auto'>

       <RouterProvider router={router}/>
    </div>
  )
}

export default App

```
### step 4->Now the login page is Okay now call the operation function to login 
#### services/operations/backendAPI.js  that use apis and apiconnector to communicate with backend 
```
export const googleAuth=async(code,dispatch)=>{
    try{
        const response = await apiConnector("GET", BASE_URL+`/google?code=${code}`)
        console.log("response getting",response)
        dispatch(setToken(response.data.token));
        dispatch(setUser(response.data.user));
        localStorage.setItem("token",JSON.stringify(response.data.token))
        localStorage.setItem("user",JSON.stringify(response.data.user))
        return response
    }catch(error){
        console.log("errro occured")
    }
}
```
### step5-> now call this function onclick of login with google see the (GoogleLogin.jsx)
```
const responseGoogle = async (authResult) => {
    try {
      if (authResult['code']) {
        const result = await googleAuth(authResult['code'], dispatch);
        console.log("res.data.user", result.data.user)
      }
      navigate("/")

    } catch (error) {
      console.error("Wrror while requesting google code", error)
    }
  }
  const googleLogin = useGoogleLogin({
    onSuccess: responseGoogle,
    onError: responseGoogle,
    flow: 'auth-code'
  })
```
#### see the complete code of GoogleLogin.jsx
```
import React from 'react'
import { useGoogleLogin } from '@react-oauth/google';
import { googleAuth } from '../../../service/operations/backendAPI';
import { useDispatch } from 'react-redux';
import { useNavigate } from 'react-router-dom';
import googleimage from "../../assets/googleimage.png"
import geminiailogo from "../../assets/gemini.png"
function GoogleLogin() {
  const navigate = useNavigate()
  const dispatch = useDispatch()
  const responseGoogle = async (authResult) => {
    try {
      if (authResult['code']) {
        const result = await googleAuth(authResult['code'], dispatch);
        console.log("res.data.user", result.data.user)
      }
      navigate("/")

    } catch (error) {
      console.error("Wrror while requesting google code", error)
    }
  }
  const googleLogin = useGoogleLogin({
    onSuccess: responseGoogle,
    onError: responseGoogle,
    flow: 'auth-code'
  })
  return (
    <div className='text-white flex flex-col justify-between items-center  bg-black h-[100vh] w-[100vw]'>
      <div className='w-full h-20%'>
        <img src={geminiailogo} alt="" className='h-24 w-[20%]' />
      </div>
      <div className='h-[80%] w-full flex justify-center items-start'>
        <div className='flex flex-col w-[60%] justify-center items-center  gap-10 '>
          <h1 className='text-3xl'>Welcome Back!</h1>
          <div className='h-16 w-16 border-[1px] border-gray-500 p-3 rounded-[400px]'>
          <img src={googleimage} alt="" />
          </div>
          
          <button onClick={googleLogin} className='flex justify-evenly gap-2 items-center mt-1 border-[1px] p-4 rounded-3xl border-gray-500 hover:bg-gray-900'>
            <img src={googleimage} alt=" " className='h-10 w-10' />
            <p className='text-xl'>Login with google</p>
          </button>
        </div>
      </div>



    </div>
  )
}

export default GoogleLogin

```
#### now its done 

