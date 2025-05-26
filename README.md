# Overview
This project is a Software Repository designed to store, manage, and retrieve assets efficiently. The system is built using Spring Boot for the backend and React for the frontend, with PostgreSQL as the database.

# Getting Started
## Prerequisites

Before setting up the project, ensure you have the following installed:

- Java 21+ (Required for Spring Boot)
- Maven (For building the backend)
- Docker (Required for running database)
- Node.js & npm (For frontend development)
- Git (For version control)

## Setup

Install Docker Engine.
Copy `.env.example` to `.env` and configure as desired:
* Database Username
* Database Password
* Database Port (must be unique and not in use)
* Database Name
* Database Host (typically localhost)

To run the project:
```bash
docker compose up -d --build
```

To completely remove the project (and all persisted data):
```bash
docker compose down -v
```

## How to Use current API endpoints
Once the backend of the project is running there should be API enpoints exposed here are the types and how to use them

### POST requests
There are post requests for object creation of every table these include:
- /assets/addAsset
- /useraccounts/addUser
- /assetMetadata/addAssetMetadata
- /assetAssociations/addAssetAssociation

For these requests they require a post request to the associated address for instance here is an example of using endpoints to create an Asset:

```react
axios.post("http://[Spring host]]:[Spring port]/assets/addAsset", {
    name: "This is the Asset name",
    path: "/where/the/asset/is/stored",
    type: ".png",
    authorId: 1
});
```

### PATCH requests
There are patch requests for editting already stored data in each table these include:
- /assets/updateById
- /useraccounts/updateById
- /assetMetadata/updateById
- /assetAssociation/updateById

These update request only require the unique Id of the item in the database and then you can incude any fields that are being editted
Here is an example of a patch request editing the asset name of the previously created asset(assuming its Id is 1):

```react
axios.patch("http://[Spring host]]:[Spring port]/assets/updateById",{
    id : 1,
    name: "This is the new Asset name"
});
```

### GET requests
There are an assortment of GET requests for the stored data in the database:
- /assets/viewAssets
- /assets/searchFor
- /assets/findById
- /assets/authoredBy
- /useraccounts/viewUserAccounts
- /useraccounts/searchFor
- /useraccounts/findById

Here are some examples on different ways of viewing assets in React:
```react
//Viewing all assets in database
axios.get("http://[Spring host]:[Spring port]/assets/viewAssets");

//Viewing all assets authored by User with Id of 1
axios.get(`http://[Spring host]:[Spring port]/assets/authoredBy?userId= 1`);

//Viewing Asset with an id of 1
axios.get(`http://[Spring host]:[Spring port]/assets/findById?id=1`);

//Searching for Asset by name (looks for any name containing substring)
axios.get(`http://[Spring host]:[Spring port]/assets/searchFor?name=s`);
```

### DELETE requests
There is 1 DELETE request for each data type each requiring the ID of the row in the table in order to delete:
- /assets/deleteById
- /useraccounts/deleteById
- /assetMetadata/deleteById
- /assetAssociation/deleteById

Here is an example on how to delete an asset with React:
```react
//Delete asset with id 1:
axios.delete("http://[Spring host]:[Spring port]/assets/deleteById?id=1")
```

## How to use Spring Security configuration

The Security for the APIs is currently configured to require a login for all APIs with role based access so that only admins can edit or delete items.

### Testing/Development Logins
In order to keep development of the backend relatively unimpeded there are 3 developer accounts that are added upon docker compose up these are :
```
Development_Admin
Development_User
Development_Viewer
```
To login to these development users the password is:
```
TeamProject38
```
### How to handle Security when testing the backend with Swagger or Postman
- In Swagger:
When using swagger you will see that each of the APIs has a padlock next to it, this does not mean that all the APIs require authorisation, just that the option to append a JWT is available,

in order to generate a JWT you will need to use the /login endpoint with a valid username and password (stored in the login table).
![Logging in with swagger](./img/Swagger%20login%20request.png "Logging in with swagger")

This endpoint will then return a JWT:
![JWT returned by swagger](./img/Swagger%20login%20response.png "JWT returned by swagger")

That can be entered on any endpoint request by clicking on the padlock and pasting it in and clicking 'Authorize'.
![Entering authorisation details](./img/Swagger%20Authorisation%20popup.png "Entering authorisation details")

- In Postman:

First send a login POST request to the correct address and /login endpoint:
![Sending login request](./img/Postman%20Login%20Request.png "Sending login request with Postman")

If login details were correct the response body will be a JWT token:
![JWT token response](./img/Postman%20Login%20Response.png "JWT token response")

Under authorization select Bearer Token from the dropdown and copy this token into the token entry, to recieve the correct authorisation for your login:
![Authorising future requests](./img/Postman%20authorisation.png "Authorising future requests")

## How to handle Security in Frontend/React

First send a login request and store the JWT token:
```react
const response = await axios.post('http://[Spring host]:[Spring port]/login', { 
                username, 
                password 
            });
const token = response.data; 
```
Then for any future requests add the token to the header:
```react
const response = await axios.get('http://[Spring host]:[Spring port]/assets/viewAssets', {
      headers: {
        Authorization: `Bearer ${token}`
      }});
```
