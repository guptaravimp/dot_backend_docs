# Lets first setup backend project using express 
### see the docs : https://github.com/guptaravimp/Backend_Docs_/blob/main/Setup_Into.md
## Now let parse data and get and post mehtod 
# 1- get method use fetch the data 
## 1-> write inside index.js file
```
app.get('/', (req, res) => {
  res.send('Hello World!')
})
```
## 2-> now go to postman and check the api testing 
### using get mehtod 
```
http://localhost:3000/
```
## See the reponse of postman 
![Screenshot 2024-12-25 011656](https://github.com/user-attachments/assets/e2aaaec2-c5be-4650-b06e-a77dbb55ace5)

# 2-> post method use to send the data let send the data from the postman api 
## write the post request code inside the index.js
```
app.post('/api/cars',(req,res)=>{
    const {name,brand}=req.body;
    console.log(name)
    console.log(brand)
   
    // res.send('Hello World!')
    // or 
    // console.log(req.body)
    res.send("car added succesfully")
})
```
## Now let send the data from req.body using postman 

![image](https://github.com/user-attachments/assets/f52064ef-fedb-4a6b-a944-0524fa07e512)
## after sending this data as we can see it give the wrong response 

![Screenshot 2024-12-25 012219](https://github.com/user-attachments/assets/7933e956-ad13-4fa8-ac93-152e95439ec0)

## this is due to because we send the json data we have to use body-parser or inbuit-middleware to parse this json data into a valid javascriprt model data 
le
## so go to index.json and use bodyparser or inbuild middle-ware 
```
const bodyParser=require('body-parser');
app.use(bodyParser.json());
// or 
// app.use(express.json());
```
## Now if we test the data we can see the console and postman reponse 

![Screenshot 2024-12-25 012707](https://github.com/user-attachments/assets/721b76e2-9a58-4385-8ab9-2f6c61b850f3)

Postman response : 

![Screenshot 2024-12-25 012410](https://github.com/user-attachments/assets/63899189-a2c1-44f9-aabd-49f5e53921d1)






