# MERN WEB STACK IMPLEMENTATION ON AMAZON AWS
This README outlines my experience in setting up a MERN (MongoDB, Express, React, Node.js) stack application on an AWS EC2 instance to deploy a simple ToDo application.



The MERN stack is a popular full-stack development framework consisting of MongoDB, Express, React, and Node.js. Each component plays a key role in creating a seamless and robust application: MongoDB as the NoSQL database, Express as the backend web framework, React for building interactive frontend user interfaces, and Node.js for executing JavaScript on the server side.


### STEPS INVOLVED
- Step 0 -  [Preparing the Prerequisites](#preparing-the-prerequisites)
- Step 1 -  [Backend Configuration](#backend-configuration)
- Step 2 -  [Install ExpressJS](#install-expressjs)
- Step 3 -  [Setting up Models](#setting-up-models)
- Step 4 -  [Setting up MongoDB Database](#setting-up-mongodb-database)
- Step 5 -  [Testing Backend Code without Frontend using Postman](#testing-backend-code-without-frontend-using-postman)
- Step 6 -  [Creating Frontend](#creating-frontend)
- Step 7 -  [Creating React Components](#creating-react-components)

### Preparing the Prerequisites
- **Knowledge Requirements:** Familiarity with Javascript, ReactJs, MongoDB, and Vim editor.
- **AWS Account:** Required to set up the Ubuntu server.
- **Terminal**

**Ubuntu Instance Setup**
1. Follow step 0 in the previous project [LEMP STACK IMPLEMENTATION](https://github.com/AyopoB/StegHub-PBL/tree/main/LEMP-stack-implementation)

---

### Backend Configuration
In the terminal, on the Ubuntu server, Update Ubuntu:
```bash
sudo apt update
```

Upgrade Ubuntu:
```bash
sudo apt upgrade
```

Get the location of Node.js software from [Ubuntu repositories](https://github.com/nodesource/distributions#deb).
```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
```
- ![](img/curl%20nodesource1.png)
- ![](img/curl%20nodesource2.png)
Install Node.js:
```bash
sudo apt-get install -y nodejs
```
![](img/install%20nodejs.png)


The above installs both Node.js and NPM, a package manager for Node.

Verify the node and NPM installation with the following:
```bash
node -v
npm -v
```


![](img/verify%20installation.png)


**Set up Todo application code**


Create a new directory for the ToDo application:
```bash
mkdir Todo
```


Change the Directory to the newly created one:

```bash
cd Todo
```


![](img/mkdir%20todo.png)

Next, use :
```bash
npm init
```
The above initializes the project, which creates a package.json file, which will contain information  about the application and the dependencies it needs to run. 


![](img/npm%20init.png)

---
### Install ExpressJS

Next, we will install ExpressJs and create the Routes directory.
Install Express using npm:
```bash
npm install express
```

![](img/install%20express.png)

Create a  file named index.js with:
```bash
touch index.js
```

Install the dotenv module with:
```bash
npm install dotenv
```

![](img/install%20dotenv.png)


Open index.js with vim :
```bash
vim index.js
```
Type in the code below:

```js
const express = require('express');
require('dotenv').config();

const app = express();

const port = process.env.PORT || 5000;

app.use((req, res, next) => {
res.header("Access-Control-Allow-Origin", "\*");
res.header("Access-Control-Allow-Headers", "Origin, X-Requested-With, Content-Type, Accept");
next();
});

app.use((req, res, next) => {
res.send('Welcome to Express');
});

app.listen(port, () => {
console.log(`Server running on port ${port}`)
});
```
Save and close the file with *:wq*.

Next, go to your EC2 instance and modify the security group  inbound rule to open TCP port 5000.

Now run:
```bash
node index.js
```

![](img/running%20node%20js%20port.png)


Open your browser and access the Server's Public IP or DNS name followed by port ':5000'

The browser should output "**Welcome to Express**".

**Setting up Routes**

The ToDo application will have 3 functionalities:
1. Create Tasks ( using HTTP Request methods: POST)
2. Display list of Tasks ( using HTTP Request methods: GET)
3. Delete Tasks ( using HTTP Request methods: DELETE)

For each task, let's create  *routes*  that will define various endpoints on which the ToDo app will depend.


Let's create a folder *routes*  inside the Todo directory and change the directory to routes:
```bash
mkdir routes
cd routes
```
Now, create a file api.js and open the file:
```bash
touch api.js
vim api.js
```
Type in the code below
```vim

const express = require ('express');
const router = express.Router();

router.get('/todos', (req, res, next) => {

});

router.post('/todos', (req, res, next) => {

});

router.delete('/todos/:id', (req, res, next) => {

})

module.exports = router;
```

---

### Setting up Models

In a MERN stack application, models represent the structure of data within your MongoDB database and are typically defined using Mongoose, a popular MongoDB ODM (Object Data Modeling) library for Node.js. To create a Schema and a model, let's install *mongoose*.
 

 Change the  directory to the Todo folder, and install Mongoose:
 ```bash
 npm install mongoose
 ```

 Create a new folder  with *mkdir models*, cd into models, and create a file with the name *todo.js*:

 ```bash
 mkdir models && cd models && touch todo.js
 ```
Type in the code below :


![](img/vim%20todojs.png)

Save the file.

Change  the directory to the  routes directory, open api.js, and delete the previous code in it, Update it with the new code below, save, and exit:
![](img/updated%20apijs.png)

---

### Setting up MongoDB Database
For this ToDo application, Let's make use of a cloud-hosted MongoDB with  [mLab](https://www.mongodb.com/products/platform/atlas-database) (AKA Atlas). Follow this [guide](https://www.freecodecamp.org/news/get-started-with-mongodb-atlas/) for setting up Atlas. Copy the connection string, it will be needed.


Earlier, in the index.js file, we specified process.env to access environment variables which is yet to be created. 

In the Todo directory create a file called *.env*
```bash
touch .env && vi .env
```
Add the connection script to the file, it enables access to the database in it replace <password> with the password gotten from the Atlas  Database setup:
```vi
DB = 'mongodb+srv://<username>:<password>@cluster0.mongodb.net/<dbname>?retryWrites=true&w=majority'
```

Next, we update our index.js code, first, we delete  the previous code and update with the new code below:


![](img/update%20indexjs.png)


Start the server using :
```bash
node index.js
```
![](img/node%20inexjs%20database%20connected.png)

It should show "Database connected successfully" as seen in the image above.  This shows our backend is configured.

---

### Testing Backend Code without Frontend using Postman
Open Postman, and create a POST request to the API http://<publicIP>:5000/api/todos . 

Set the header key as shown in the image below:


![](img/header%20key.png)

Add sample task to the Body>raw as  shown below:

![](img/add%20sample%20task%20pm.png)


Create a GET request to the API http://<publicIP>:5000/api/todos, which retrieves what was stored in the database from the POST request.

![](img/run%20get%20again.png)

We can also delete the  data stored in the database using DELETE request by  adding the ID as shown in the image above as part of the  request, see below:
![](img/send%20delete.png)

---

### Creating Frontend

Now that the backend is configured and tested it is working fine. The next step involves creating the user interface for the client(Browser) to interact with our backend.

In the Todo  directory, run:
```bash
npx create-react-app client
```

Running the above led to a failure due to low RAM on my EC2 instance as React is more resource-intensive

![](img/error%20installing%20react.png)

One solution to the issue above is to upgrade the instance to a t2.small or t2.medium basically any instance with Memory higher than 1 gb.

Another is to create a swap file to give more virtual memory to the system, Let's make use of this option.


First, we delete the client folder created from the failed react installation to avoid conflicts with the new installation after the virtual memory is added, To do this we use the following:
```bash
rm -rf client
```

Then we increase Swap Memory using:

```bash
sudo fallocate -l 2G /swapfile   # Allocate 2GB of swap space
sudo chmod 600 /swapfile         # Set the correct permissions
sudo mkswap /swapfile            # Create the swap space
sudo swapon /swapfile            # Enable the swap space
```
![](img/allocating%202gbswapspace.png)


Now run create-react  again:

```bash
npx create-react-app client
```

![](img/react%20install%20successful.png)

Before testing the react app, we need to install some dependencies, Concurrently (It is used to run more than one command simultaneously from the same Terminal) and Nodemon (It is used to run and monitor the server, it also applies code changes to the server automatically), let's install them using :
```bash
npm install concurrently --save-dev
npm install nodemon --save-dev
```

In the Todo folder open and modify the package.json file, and replace the "scripts" with the new "scripts" below:

```vim
"scripts": {
"start": "node index.js",
"start-watch": "nodemon index.js",
"dev": "concurrently \"npm run start-watch\" \"cd client && npm start\""
},
```
![](img/edit%20package%20json%20scripts.png)



Next Change Directory to  Client, open and modify the package.json  file  with : 
```vim
"proxy": "http://localhost:5000"
```
![](img/modify%20client%20package%20json%20with%20proxy.png)


Next, go to your EC2 instance and modify the security group inbound rule to open TCP port 3000.

Then change the directory to the Todo directory and run:
```bash
npm run dev
```
![](img/Npm%20run%20dev%20bash.png)
![](img/npm%20run%20dev%20init%20react.png)


---

### Creating React Components
For our Todo application, we would be creating two stateful components and one stateless component.


Run:

```bash
cd client && cd src && mkdir components && cd components
```
Inside the components directory, we create three  files:
```bash
touch Input.js ListTodo.js Todo.js
```

Open Input.js and paste the code below:

```vim
import React, { Component } from 'react';
import axios from 'axios';

class Input extends Component {

state = {
action: ""
}

addTodo = () => {
const task = {action: this.state.action}

    if(task.action && task.action.length > 0){
      axios.post('/api/todos', task)
        .then(res => {
          if(res.data){
            this.props.getTodos();
            this.setState({action: ""})
          }
        })
        .catch(err => console.log(err))
    }else {
      console.log('input field required')
    }

}

handleChange = (e) => {
this.setState({
action: e.target.value
})
}

render() {
let { action } = this.state;
return (
<div>
<input type="text" onChange={this.handleChange} value={action} />
<button onClick={this.addTodo}>add todo</button>
</div>
)
}
}

export default Input
```

To make use of Axios in the code above, change the directory to the client folder, and run :
```bash
npm install axios
```
Return to the components directory:
```bash
cd src/components
```
Next Open ListTodo.js and add the code below:
```vim

import React from 'react';

const ListTodo = ({ todos, deleteTodo }) => {

return (
<ul>
{
todos &&
todos.length > 0 ?
(
todos.map(todo => {
return (
<li key={todo._id} onClick={() => deleteTodo(todo._id)}>{todo.action}</li>
)
})
)
:
(
<li>No todo(s) left</li>
)
}
</ul>
)
}

export default ListTodo
```


Then in Todo.js, add the code below:

```vim
import React, {Component} from 'react';
import axios from 'axios';

import Input from './Input';
import ListTodo from './ListTodo';

class Todo extends Component {

state = {
todos: []
}

componentDidMount(){
this.getTodos();
}

getTodos = () => {
axios.get('/api/todos')
.then(res => {
if(res.data){
this.setState({
todos: res.data
})
}
})
.catch(err => console.log(err))
}

deleteTodo = (id) => {

    axios.delete(`/api/todos/${id}`)
      .then(res => {
        if(res.data){
          this.getTodos()
        }
      })
      .catch(err => console.log(err))

}

render() {
let { todos } = this.state;

    return(
      <div>
        <h1>My Todo(s)</h1>
        <Input getTodos={this.getTodos}/>
        <ListTodo todos={todos} deleteTodo={this.deleteTodo}/>
      </div>
    )

}
}

export default Todo;
```

Next, we need to adjust the default react code and delete the logo.

Move to the src folder and open the App.js file, modify it with the code below:

```vim
import React from 'react';

import Todo from './components/Todo';
import './App.css';

const App = () => {
return (
<div className="App">
<Todo />
</div>
);
}

export default App;
```

Open  App.css and modify with the code below:

```vim
.App {
text-align: center;
font-size: calc(10px + 2vmin);
width: 60%;
margin-left: auto;
margin-right: auto;
}

input {
height: 40px;
width: 50%;
border: none;
border-bottom: 2px #101113 solid;
background: none;
font-size: 1.5rem;
color: #787a80;
}

input:focus {
outline: none;
}

button {
width: 25%;
height: 45px;
border: none;
margin-left: 10px;
font-size: 25px;
background: #101113;
border-radius: 5px;
color: #787a80;
cursor: pointer;
}

button:focus {
outline: none;
}

ul {
list-style: none;
text-align: left;
padding: 15px;
background: #171a1f;
border-radius: 5px;
}

li {
padding: 15px;
font-size: 1.5rem;
margin-bottom: 15px;
background: #282c34;
border-radius: 5px;
overflow-wrap: break-word;
cursor: pointer;
}

@media only screen and (min-width: 300px) {
.App {
width: 80%;
}

input {
width: 100%
}

button {
width: 100%;
margin-top: 15px;
margin-left: 0;
}
}

@media only screen and (min-width: 640px) {
.App {
width: 60%;
}

input {
width: 50%;
}

button {
width: 30%;
margin-left: 10px;
margin-top: 0;
}
}
```

Next, we open index.css still in the src folder, and modify it with the code below:

```vim
body {
margin: 0;
padding: 0;
font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", "Roboto", "Oxygen",
"Ubuntu", "Cantarell", "Fira Sans", "Droid Sans", "Helvetica Neue",
sans-serif;
-webkit-font-smoothing: antialiased;
-moz-osx-font-smoothing: grayscale;
box-sizing: border-box;
background-color: #282c34;
color: #787a80;
}

code {
font-family: source-code-pro, Menlo, Monaco, Consolas, "Courier New",
monospace;
}
```

Change back to the Todo directory, Run:
``` bash
npm run dev
```
Tada!!!, The client(Browser) should display  and be functional as shown in the screenshots below:

- ![](img/final1.png)
- ![](img/final%202.png)

---

### Challenges Faced and Lessons Learned

#### Challenges:
1. **Low Memory on EC2 Instance:**
   - **Obstacle:** During the installation of React, the process failed due to insufficient memory on the t2.micro instance.
   - **Solution:** I overcame this by creating a swap file to increase virtual memory. This prevented the instance from crashing and allowed the React installation to proceed successfully. Alternatively, upgrading to a larger instance could have been a solution.
   - **Lesson Learned:** Always consider the resource requirements of each component when selecting an EC2 instance type. For heavier applications like React, higher memory instances may be necessary.

2. **Testing Backend without Frontend:**
   - **Obstacle:** Before building the frontend, I had to ensure the backend was working correctly. This meant testing the API endpoints manually through Postman, which required precision to validate all CRUD operations.
   - **Solution:** I used Postman to extensively test the backend functionality, verifying each API call (GET, POST, PUT, DELETE) to ensure everything worked before moving on to the frontend.
   - **Lesson Learned:** Backend testing can be efficiently done with tools like Postman before the frontend is integrated. This approach helped me catch and resolve issues early on, leading to smoother development once the frontend was connected.

#### Key Takeaways:
- Planning your server and resource allocation based on the needs of each component can save time and prevent performance issues during the project. Particularly for memory-heavy processes like installing and running React.
- Backend testing with Postman proved invaluable, allowing me to identify and address issues at an early stage. This saved time and reduced complexity during frontend integration.
- Following best practices such as secure database connection management and the use of environment variables is essential for production deployments on platforms like AWS.

By overcoming these challenges and taking proactive steps to ensure efficiency and security, I have gained deeper insight into building and deploying MERN stack applications on cloud infrastructure.

---



