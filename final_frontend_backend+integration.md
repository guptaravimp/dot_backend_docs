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
## step7-> Now you can change the port of frontend goto -> vite.config.js-> add serverb port 
```
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

// https://vite.dev/config/
export default defineConfig({
  plugins: [react()],
  server: {
    port: 3001, // Change to your desired port number
  },
})

```

##step8->  Now you can see by running 
```
npm run dev
```

## Now your project frontend and backend is running simultaniously  
### check frontend routes (usually 3000 port)
### check backend routes port (usually 4000 port)

## now we added data+assets and tailwind.config.js file 
