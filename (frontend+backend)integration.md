![Image](https://github.com/user-attachments/assets/a50fcaf5-305f-4a6e-a750-9895b3b9a377)

# Now let go Deep dive into this that How actually this work 
# Step1-> Write Your Frontend and Backend Code with indivisual folder see this 

## Frontend Folder ->
https://github.com/guptaravimp/full-Stack-employee-form/tree/main/frontend
## Backend Folder 
https://github.com/guptaravimp/full-Stack-employee-form/tree/main/todobackend

# step2 Now to connect this create a (.env)  file inside frontend folder 
### As You open the folder of frontend and see .env file if not showing the create this .env file inside frontend dont worry just follow the step 
```
REACT_APP_BASE_URL=http://localhost:4000/api/v1

```
### that is the base url of our backend project 
# Now Your frontend and backend is connected just use this lets use this in our frontend 
## let we are calling to /createUser to create the user 
#### then backend url is test in Postman
```
http://localhost:4000/api/v1/createUser
```
#### but if we are using in frontend then use in our frontend 
##### let use in form to post our data on backend database see the code of 
##### inside -> 
url 
#### this code 
```

    const savedUserResponse = await fetch(`${process.env.REACT_APP_BASE_URL}/createUser`
      ,
      {
        method: "POST",
        headers: {
          "Content-Type": "application/json",
        },
        body: JSON.stringify({ ...data }),
      }
    );

    console.log("FORM RESPONSE......", savedUserResponse);

    navigate("/")
  };

  

```
#### Working of api fectch 
```
${process.env.REACT_APP_BASE_URL}/createUser

```
same as 
```
http://localhost:4000/api/v1/createUser
```
Because (process.env.REACT_APP_BASE_URL) inside frontend (REACT_APP_BASE_URL=http://localhost:4000/api/v1) 
# Same as where ever in frontend we want to call our backend api just use to fetch method and add your routes and access it 




