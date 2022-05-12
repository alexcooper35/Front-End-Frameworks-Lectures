# Serverless App Development Day 3
###### tags: `aws`

1. [ Introduction ](#1)
2. [ Flow Summary ](#2)
3. [ Create an AWS API Gateway ](#3)
4. [ Create React App ](#4)
5. [ Create (PUT) ](#5)
6. [ Exercise ](#6)
7. [  Get One Item by Id ](#7)
8. [ Exercise ](#8)
9. [ Exercise ](#9)
13. [ LAB #1 ](#13)


## Introduction: Connect React app to Lambda functions via API Gateway
In today’s class we will use our existing DynamoDb database and Lambda Functions while using the AWS API Gateway to be consumed by a local React client app.

## Flow Summary
•	Client (React App in this example but could be any client technology) 
•	Through API Gateway 
•	To execute Lambda Functions 
•	That communicate with a Database (DynamoDb in this example but could be any db in AWS)

### Pre-requisites for today:

1. The Students DynamoDB table
2. Student Lambda Policy
3. Lambda Functions: Scan, get, put, update, delete (we will need to modify most of these as we go along)

## Create an AWS API Gateway

In this first section of the lecture, we will use a lambda function that returns a collection of all records in our table by using the scan function. 

We will be able to expose this to developers with out API Gateway and then test the endpont from a react app that will be a list view of all Students.

The AWS API Gateway is exactly what its name suggests, a gateway for a client app to access server code. We will set up a RESTful API to expose the Lambda Functions that we have created in our AWS Console.

![](https://i.imgur.com/VC7ZYZz.png)

Select **Create API**

![](https://i.imgur.com/rJrvQHm.png)
![](https://i.imgur.com/KxCjhM1.png)


### Add a Resource to Your API

Our DynamoDb had a single table called Students, so I will add a Resource to my API called Students and add a Method for each Lambda Function that I would like to expose.

![](https://i.imgur.com/Qyzv2ts.png)
![](https://i.imgur.com/X2ArH8T.png)


### Add a Method to Your API

Methods are the type of HTTP Request that can be handled by a specific endpoint and the logic that will be executed (lambda function).

Select the students resource, then click Actions again but this time select Create Method and set it to Get.

![](https://i.imgur.com/2OUhYHX.png)

Click the check mark and link the method to the “scan” lambda from our day 2 tutorial.

![](https://i.imgur.com/0FEcGhz.png)
![](https://i.imgur.com/UTuy2LK.png)

Let's update the logic of our original scanUserData lambda function so that rather than having a console.log message we can receive a status code. 

        const AWS = require("aws-sdk");

        const docClient = new AWS.DynamoDB.DocumentClient({ region: "us-west-1" });

        exports.handler = async event => {
        const params = {
            TableName: "Students"
        };

        try {
            //await to satisfy the promise
            const data = await docClient.scan(params).promise();
            //the response is now a JSON object with a status code of 200 and a body of JSON data
            const response = {
                statusCode: 200,
                body: JSON.stringify(data.Items)
            };
            // Return the JSON response
            return response;
            //if there is an error we won't return any body, but just a status code of 500 to indicate a failed attempt
            } catch (e) {
            return {
                statusCode: 500
            };
        }
    };

### Test

Note the Lambda box on the right that is a quick link to the Lambda function that is connected to the endpoint.

Click the client **Test** icon to see what your Lambda function returns. It should be a JSON object with a status code and a body (the body should be an array of students).

![](https://i.imgur.com/VZIF5in.png)


![](https://i.imgur.com/cqrNDbH.png)

We have created a gateway with the endpoint that triggers the Lambda function against the DynamoDB.

Now let's create a new React app that needs to access these Lambda functions via this API.

### Enable CORS

As soon as you want to access an API from an external source you need to enable CORS (cross origin resource sharing).

To do this select the resource (/students) > and choose "enable CORS" from the Action dropdown menu. 
![](https://i.imgur.com/CrRfzII.png)


Do the same for the GET method (select Action and enable CORS).
![](https://i.imgur.com/v3i6Gvn.png)

For both the resource and the method keep the default settings and select "Enable CORS and replace existing CORS headers"

### Deploy API

Finally on the root node of the API (/) select Deploy API from the Action drop down.
![](https://i.imgur.com/3ofvYkR.png)

In the popup notice you're asked to define the stage of deployment. You can of course have many different stages of deployment, for our case let's select "new stage" call it "prod" (production). Select deploy.

![](https://i.imgur.com/XUX6Yzr.png)

Ignore the red pop up error banner and copy the URL (we need it to update the js that is in the next step).

![](https://i.imgur.com/tl6WuO0.png)

## Creating a React App

Use create-react-app to create an app with whatever name you want:

    npx create-react-app aws_lambda_api_consumer
    
    npm install react-router-dom
    
    npm start

    
### Update React App To Call API and Render Results

Open the app that you just created with a code editor.

In the src/App.js replace the code with the following:

        import React from "react";
        import {
          BrowserRouter as Router,
          Switch,
          Route,
          Link
        } from "react-router-dom";
        import Home from './components/Home';

        export default function App() {
          return (
            <Router>
              <div>
                <ul>
                  <li><Link to="/">Home</Link></li>
                </ul>
                <Switch>
                  <Route path="/"><Home /></Route>
                </Switch>
              </div>
            </Router>
          );
        }


Create a new component called Home.js to call the API and list all the students:

        import React, {useState, useEffect} from "react";
        import { Link } from "react-router-dom";

        export default function Home() {
            // base api url
            const API_INVOKE_URL = 'your_invoke_url'
    
    // state variable for form object
    const [students, setStudents] = useState([])

    // api calls
    
    
    // useEffect to get all students
    const searchApi = async () => {
        fetch(API_INVOKE_URL + '/students')
            .then(response => response.json())
            .then(data => {
                setStudents(JSON.parse(data.body));
            }
        )
    }

    useEffect(() => {
        searchApi();
    }, [])

    return (
    <div>
      <h2>Students</h2>
      <table>
          <thead>
              <tr>
                  <th>Id</th>
                  <th>First Name</th>
                  <th>Last Name</th>
                </tr>
            </thead>
            <tbody>
                {students.map(student =>
                <tr key={student.studentId}>
                    <td>{student.studentId}</td>
                    <td>{student.firstName}</td>
                    <td>{student.lastName}</td>
                </tr>
                )}
            </tbody>
        </table>
    </div>
    )
    }


### Test

The App should render the list of students. If CORS errors, ensure that you enable CORS on all Resources and Methods and re-deploy your API.

### Summary
We created a react app that connects via AWS API GAteway to execute lambda functions against a dynamodb database. 

## Create (PUT)

Let's now add a form to our home page to test our PUT Lambda.

### API Gateway

#### Add a second method under the resource:

This is what defines which Lambda function to trigger when a client GET “request” comes to https://myApi.com/students endpoint (we can set up different functions to fire for different request methods).

![](https://i.imgur.com/62JyrSy.png)

### Test
We need to modify the put lambda function in order to pass the student state variable from react:

  
    const AWS = require("aws-sdk");
    const docClient = new AWS.DynamoDB.DocumentClient({ region: "us-east-1" });

    exports.handler = async event => {
        const params = {
            TableName: "Students",
            Item: {
                studentId: event.student.studentId,
                firstName: event.student.firstName,
                lastName: event.student.lastName
            }
        };

        try {
            const data = await docClient.put(params).promise();
            const response = {
                statusCode: 200
            };
            return response;
        } catch (err) {
            return {
                statusCode: 500
            };
        }
    };

Add JSON to the Request Body to test a PUT method that is adding a new record to the database. In other words configure a new test event and include the following in the body request:
    
    {
        "student": {
        "studentId": "123456789",
        "firstName":"Gateway PUT",
        "lastName":"Test User"
        }
    }


Check the DynamoDb table items and you should see a new item added:

![](https://i.imgur.com/Dgh4wS0.png)

### Enable CORS
Enable CORS on the /students resource and all methods again.

### Deploy API
click the base route “/” and Deploy API to the prod stage again.

### React App:

Now we want to hit this api endpoint with a PUT call from our react app.

### Home.js

Reuse the Home.js by simply adding a form above the list of students:

    <form onSubmit={submit}>
        <label>Student Id: </label
        <input
            type="text"
            name="student[studentId]"
            value={student.studentId}
            onChange={e => setStudent({ ...student, studentId: e.target.value })} 
        />
        <br />
        <label>First Name: </label>
        <input
            type="text"
            name="student[firstName]"
            value={student.firstName}
            onChange={e => setStudent({ ...student, firstName: e.target.value })} 
        />
        <br />
        <label>Last Name: </label>
        <input
            type="text"
            name="student[lastName]"
            value={student.lastName}
            onChange={e => setStudent({ ...student, lastName: e.target.value })} 
        />
        <br />
        <input type="submit" name="Create Student" />
    </form>

### Add the student and setStudent to the state:

    const [student, setStudent] = useState({})



## Exercise: Add the handler function for the form submission that calls the API

See if you can figure out how to add the handler function for the form submission that calls the API Gateway.

Your result test should look like the following:

### Test

![](https://i.imgur.com/k526o5H.png)

![](https://i.imgur.com/bIvgjxH.png)

![](https://i.imgur.com/zJ1aHEO.png)

### Solution:

Home.js

    import React, { useState, useEffect } from "react";

    export default function Home() {
        //base API url 
        const API_INVOKE_URL = 'YOUR_INVOKE_URL';

        const [students, setStudents] = useState([])
        const [student, setStudent] = useState({})

        const searchApi = async () => {
            fetch(API_INVOKE_URL + '/students')
                .then(response => response.json())
                .then(data => {
                    setStudents(JSON.parse(data.body));
                }
                )
        }

        //on submit- pass form object - updated using state
        // /students resource, PUT method, student in the request body

        const submit = e => {
            e.preventDefault()
            fetch(API_INVOKE_URL + '/students', {
                method: 'PUT',
                body: JSON.stringify({ student }),
                headers: { 'Content-Type': 'application/json' }
            })
                .then(response => response.json())
                .then(() => { searchApi() })
        }


        useEffect(() => {
            searchApi();
        }, [])

        return (
            <div>
                <form onSubmit={submit}>
                    <label>Student Id: </label>
                    <input
                        type="text"
                        name="student[studentId]"
                        value={student.studentId}
                        onChange={e => setStudent({ ...student, studentId: e.target.value })}
                    />
                    <br />
                    <label>First Name: </label>
                    <input
                        type="text"
                        name="student[firstName]"
                        value={student.firstName}
                        onChange={e => setStudent({ ...student, firstName: e.target.value })}
                    />
                    <br />
                    <label>Last Name: </label>
                    <input
                        type="text"
                        name="student[lastName]"
                        value={student.lastName}
                        onChange={e => setStudent({ ...student, lastName: e.target.value })}
                    />
                    <br />
                    <input type="submit" name="Create Student" />
                </form>
                <h2>Students</h2>
                <table>
                    <thead>
                        <tr>
                            <th>Id</th>
                            <th>First Name</th>
                            <th>Last Name</th>
                        </tr>
                    </thead>
                    <tbody>
                        {students.map(student =>
                            <tr key={student.studentId}>
                                <td>{student.studentId}</td>
                                <td>{student.firstName}</td>
                                <td>{student.lastName}</td>
                            </tr>
                        )}
                    </tbody>
                </table>
            </div>
        )
    }

## Get One Item by Id

With our list view on the home page, lets test the lambda function that gets ONE student by id in a new StudentDetail component.

### API Gateway

#### Add a nested resource:

Under the /students resource add a nested resource as a “proxy resource”

![](https://i.imgur.com/h4xnTvT.png)

This will be an endpoint appended to the base url…

![](https://i.imgur.com/qwlEX5M.png)

### Create Lambda function GetById

    const AWS = require("aws-sdk");
    const docClient = new AWS.DynamoDB.DocumentClient({ region: "your-region" });

    exports.handler = async event => {
        const params = {
            TableName: "Students",
            Key: {
                studentId: event.pathParameters.studentId
            }
        };

        try {
            const data = await docClient.get(params).promise();
            const response = {
                    statusCode: 200,
                    body: JSON.stringify(data.Item),
                    headers:{ 'Access-Control-Allow-Origin' : '*' }
                };
            return response;
        } catch (err) {
            return {
                statusCode: 500
            };
        }
    };
#### Add a method under the proxy resource
You can delete the ANY method that is added under the new proxy resource and add a GET method (or leave it as ANY if you like)

![](https://i.imgur.com/HX5qD2A.png)

### Test

Click the Client “Test” icon to see what your Lambda function returns. Be sure to add a valid studentId as the Path parameter.

![](https://i.imgur.com/wRDCvL0.png)

### Enable Cors

Enable CORS on all resources and methods again.

### Deploy API
click the base route “/” and Deploy API to the prod stage again.

NOTE: Lambda function may need to be updated to include the following to resolve CORS error:

### React

Now we want to hit this api endpoint with a GET request that includes a studentId as a path parameter.

#### Home.js
Update the studentId to have the following navigation link (basically makes the id clickable to take us to the Detail page):

    <td><Link to={`studentDetail/${student.studentId}`}>{student.studentId}</Link></td>


## Exercise: 

Update the App.js to route to a new component that you will also create called StudentDetail.js

Create this new component to layout the details of one student. This may seem redundant given that we are showing all values on the home page, but this is valuable when you want to display some data in a list and more complete data in a detail view.

Your test should look something like the following:

#### Solution:

App.js

    import Home from './components/Home';
    import StudentDetail from './components/StudentDetail';

    export default function App() {
      return (
        <Router>
          <div>
            <ul>
              <li><Link to="/">Home</Link></li>
            </ul>
            <Switch>
              <Route path="/studentDetail/:studentId">
                <StudentDetail />
              </Route>
              <Route path="/">
                <Home />
              </Route>
            </Switch>

          </div>
        </Router>
      );
    }

StudentDetail.js:

    import React, { useState, useEffect } from "react";
    import { useParams } from "react-router-dom";

    export default function StudentDetail() {
        //base api url
        const API_INVOKE_URL = "yourinvokeurl";

        const [student, setStudent] = useState({})

        let { studentId } = useParams();

        const getApi = async () => {
            fetch(`${API_INVOKE_URL}/students/${studentId}`)
                .then(response => response.json())
                .then(data => {
                    setStudent(data)
                })
        }

        useEffect(() => {
            getApi();
        }, [])

        return <h3>Student Name: {student.firstName} {student.lastName}</h3>
    }

### Test

![](https://i.imgur.com/oA9V3cv.png)

## Exercise:
1.	 Usinig the put method, add a form to the student detail component and allow users to update a student's first name and last name but not id. 

2.	Adapt the GetById function to test delete by id either on the home page or the detail page or both.


## LAB #1

Do the above with an RDS mySQL Database. This does not have to be the profile database from last class although you may use that one if you wish.

Create an API Gateway with the appropriate methods and resources in order to connect from a React app to create, read, update and delete items via Lambda functions.

Your app should allow users to see details about a specific id in a new component when they click on a details button.

**Bonus marks for (1 mark each):**

Your app includes form elements that allow for the input of new items into the database.

Your app allows users to delete items when clicking on a delete button. 

Use Github to manage your tasks and include a readme file with any important information you'd like to include. 

Due Thursday Feb 25th at midnight

**Github Classroom link:** https://classroom.github.com/g/-eWkbU-B

DO NOT submit any sensitive data to Github including access keys or secret access keys.



