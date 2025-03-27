# In this tutorial we know about that how we integrate our backend and frontent to communicate with each other 

# first Need of cors inside server (index.js)
## If your frontend (React, Vue, etc.) and backend (Express, Node.js) are on different domains or ports, you need to enable CORS in server.js. Otherwise, the browser will block requests due to Same-Origin Policy.
# How to Enable CORS in index.js
## step1-> Install cors 
```
npm install cors
```
## step2-> import in index.js 
```
const cors = require('cors');
```
## tsep3-> now add express(cors) middleware in index.js
## Why Do We Use This CORS Middleware in Express?
### By default, browsers block cross-origin requests due to the Same-Origin Policy. This means that if your frontend (http://localhost:3000) tries to access your backend API (http://localhost:4000), the browser will block the request.
### To allow the frontend to communicate with the backend, we use CORS middleware in server.js
## so add this middleware in index.js in backend
```
app.use(
    cors({
        origin:"http://localhost:3000"
    })
)
```
