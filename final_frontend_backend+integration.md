# this tutorial know us hoe we can connect or frontend and backend 
## step->1 make changes in backend that is required see the below url 


## step2-> now create a frontend folder lets name with frontend 
## step3-> now paste the backend folder inside this frontend project folder 
## step 4 Now install concurrently dependencies inside the frontend open folder 
```
npm install concurrently
```
## step-5> check the dependencied in package.json that it install or not 
## step6-> paste the below code inside the package.json->  scripts
```
 "scripts": {
    "start": "vite dev", 
    "build": "vite build",
    "lint": "eslint .",
    "preview": "vite preview",
    "server": "cd server && npm run dev",
    "dev": "concurrently -n \"client,server\" -c \"bgBlue,bgYellow\" \"npm start\" \"npm run server\""
  },
```
