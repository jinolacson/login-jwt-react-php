# A basic JWT+REACTJS+PHP+MYSQL login/logout application
A basic application login/logout authentication application using jwt, create react app cli, php, mysql.

## Folder structure example

```
htdocs/
  client-jwt/
  server-jwt/
```
## Basic setup

```
OS version         : Ubuntu 18.04.2 LTS
npm --version      : 6.9.0
npx --version      : 10.2.0(optional)
yarn --version 	   : 1.15.2(optional)
composer --version : 1.6.5

```

## If you want to test this by cloning, in terminal type the ff.

```
client-jwt
- sudo yarn install
- sudo yarn start

server-jwt
- composer install
- sudo /opt/lampp/lampp start
```

## Install simple JWT 

1. Create folder **server-jwt**.

2. Inside **server-jwt** create `login.php`.

3. `login.php` should have.

```

<?php

//Disable CORB = Cross-Origin Read Blocking (CORB) for testing
header('Access-Control-Allow-Origin: *');

require __DIR__ . '/vendor/autoload.php';

use ReallySimpleJWT\Token;

/**
 * Sample data for demonstration
 */
$userId = 1;
$secret = 'sec!ReT423*&';
$expiration = time() + 3600;
$issuer = 'localhost';

//decode post input variables
$data = json_decode(file_get_contents("php://input"));

//sample dummy credentials
$validCredentials = isset($data->username) && isset($data->password) && $data->username == 'uname' && $data->password == 'pass';

//set content type header to json
header('Content-type: application/json');

//check example if from db query
if($validCredentials){
	
	$token = Token::create($userId, $secret, $expiration, $issuer);

	/**
	 * Create response 200 ok
	 */
	echo json_encode([
		'token' => $token,
		'status' => 200,
		'message' => 'Login success'
	]);

}else{
 	/**
 	 * Create response 400 failed
 	 */
    echo json_encode([
		'status' => 400,
		'message' => 'Login failed!'
	]);
}
?>

```

2. Install chrome CORS(Allow-Control-Allow-Origin) plugin and `activate`

3. Setup ***composer.json*** composer via.

```
composer require rbdwllr/reallysimplejwt

```
4. After composer require `composer.json` should have the ff.

```
"require": {
    "rbdwllr/reallysimplejwt": "^2.0"
}

```

4. Start apache server.

```
sudo /opt/lampp/lampp start

```


## Sample login using `create-react-app` script

1. Requirements lets assume

```
node : v11.6.0
npx	 : 6.9.0
yarn : v1.16.0

```

2. Inside terminal type `sudo npx create-react-app client-jwt && cd create-react-app client-jwt`

3. Update `package.json` in terminal type `sudo npm install --save axios jwt-decode` package.json should have the ff.

```
"axios": "^0.18.0",
"jwt-decode": "^2.2.0"

```

4. In **src** folder create `login.css`, `login.js`,  `signup.js`.

5. `login.css` should have.

```
//src/login.css

@import url('https://fonts.googleapis.com/css?family=Raleway');

.main-wrapper {
    display: flex;
    background: rgb(91, 91, 209);
    height: 100vh;
    width: 100vw;
    justify-content: center;
    align-items: center;
}

.signiture {
    position: absolute;
    bottom: 0;
    left: 10px;
    color: rgb(177, 177, 241);
}

.box {
    display: flex;
    padding: 0px 40px;
    padding-top: 20px;
    border-radius: 20px;
    width: 40%;
    flex-direction: column;
    justify-content: space-around;
    align-items: center;
    background: linear-gradient(to bottom right, rgb(255, 255, 255), rgb(195, 195, 195));
    box-shadow: 0px 1px 10px 0px rgba(0, 0, 0, 0.63);
}

.box-header {
    display: flex;
    font-size: 30px;
    margin-bottom: 40px;
}

.box-header > h1 {
    margin: 0;
    font-weight: 500;
}

.box-form {
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
    width: 100%;
}

.box-form input {
    border-style: solid;
    border-width: 1px;
    border-radius: 5px;
    text-align: center;
    font-size: 20px;
    border-color: rgb(202, 202, 202);
    width: 100%;
    padding: 10px 0px;
    margin-bottom: 10px;
}


.box-form input:last-child {
    width: 100%;
    padding: 20px 0px;
}

.box-form button {
    padding: 20px 0px;
    width: 40%;
    margin: 0;
    margin-top: 20px;
    font-size: 20px;
    font-weight: 500;
    cursor: pointer;
}

.link {
    text-align: center;
    text-decoration: none;
    font-size: 20px;
    margin-top: 30px;
    margin-bottom: 5px;
    width:100%;
}

.link-signup {
    font-weight: 500;
    color: rgb(91, 91, 209);
}

```
6. `login.js` should have.

```
//src/login.js

import React, { Component } from "react";

/* We want to import our 'AuthHelperMethods' component in order to send a login request */
import AuthHelperMethods from './components/AuthHelperMethods';
import { Link } from 'react-router-dom';
import './login.css'



class Login extends Component {

    /* In order to utilize our authentication methods within the AuthService class, we want to instantiate a new object */
    Auth = new AuthHelperMethods();

    state = {
        username: "",
        password: ""
    }

    /* Fired off every time the use enters something into the input fields */
    _handleChange = (e) => {
        this.setState(
            {
                [e.target.name]: e.target.value
            }
        )
    }

    handleFormSubmit = (e) => {
        
        e.preventDefault();
        /* Here is where all the login logic will go. Upon clicking the login button, we would like to utilize a login method that will send our entered credentials over to the server for verification. Once verified, it should store your token and send you to the protected route. */
        this.Auth.login(this.state.username, this.state.password)
            .then(res => {
                if (res === false) {
                    return alert("Sorry those credentials don't exist!");
                }
                this.props.history.replace('/');
            })
            .catch(err => {
                alert(err);
            })
    }

    componentWillMount() {
        /* Here is a great place to redirect someone who is already logged in to the protected route */
        if (this.Auth.loggedIn())
            this.props.history.replace('/');
    }

    render() {
        return (
            <React.Fragment>
                <div className="main-wrapper">
                    <div className="box">
                        <div className="box-header">
                            <h1>Login</h1>
                        </div>
                        <form className="box-form">
                            <input
                                className="form-item"
                                placeholder="Username"
                                name="username"
                                type="text"
                                onChange={this._handleChange}
                            />
                            <input
                                className="form-item"
                                placeholder="Password"
                                name="password"
                                type="password"
                                onChange={this._handleChange}
                            />
                            <button className="form-submit" onClick={this.handleFormSubmit}>Login</button>
                        </form>
                        <Link className="link" to="/signup">Don't have an account? <span className="link-signup">Signup</span></Link>
                    </div>
                    {/* <div className="signiture">
                        <h1></h1>
                    </div> */}
                </div>
                
            </React.Fragment>
        );
    }

}

export default Login;

```

7. `signup.js` should have.

```
//src/signup.js

import React, {Component} from "react";
import AuthHelperMethods from './components/AuthHelperMethods';
import './login.css'
import axios from "axios";
import { Link } from 'react-router-dom';

export default class Signup extends Component {
    
    Auth = new AuthHelperMethods();
    state = {
        username: "",
        password: ""
    }

    _handleChange = (e) => {
        
        this.setState(
            {
                [e.target.name]: e.target.value
            }
        )

        console.log(this.state);
    }

    handleFormSubmit = (e) => {

        e.preventDefault();
        
        axios.post("/signup", {
                username: this.state.username,
                password: this.state.password
            }).then((data) => {
                console.log(data);
                this.props.history.replace('/login');
            })
    }

    componentDidMount() {
        console.log(this.Auth.loggedIn());
        if(this.Auth.loggedIn()){
            this.props.history.push('/dashboard')
        }
    }

    render() {


        return (
            <React.Fragment>
                <div className="main-wrapper">
                    <div className="box">
                        <div className="box-header">
                            <h1>Signup</h1>
                        </div>
                        <form className="box-form">
                            <input
                                className="form-item"
                                placeholder="Username"
                                name="username"
                                type="text"
                                onChange={this._handleChange}
                            />
                            <input
                                className="form-item"
                                placeholder="Password"
                                name="password"
                                type="password"
                                onChange={this._handleChange}
                            />
                            <button className="form-submit" onClick={this.handleFormSubmit}>Signup</button>
                        </form>
                        <Link className="link" to="/login">Already have an account? <span className="link-signup">Login</span></Link>
                    </div>
                    {/* <div className="signiture">
                        <h1></h1>
                    </div> */}
                </div>
                
            </React.Fragment>
        );
    }

}

```
8. Create **components** folder inside `src` folder.

9. Inside **components** create these two files **AuthHelperMethods.js** and **withAuth.js**

10. `AuthHelperMethods.js` should have

```
//src/components/AuthHelperMethods.js

import decode from 'jwt-decode';

export default class AuthHelperMethods {
    
    // Initializing important variables

    login = (username, password) => {
        
        // Get a token from api server using the fetch api
        return this.fetch(`http://localhost/simple-jwt/login.php`, {
            method: 'POST',
            body: JSON.stringify({
                username,
                password
            })
        }).then(res => {
            
            this.setToken(res.token) // Setting the token in localStorage
            return Promise.resolve(res);
        })
    }


    loggedIn = () => {
        // Checks if there is a saved token and it's still valid
        const token = this.getToken() // Getting token from localstorage
        return !!token && !this.isTokenExpired(token) // handwaiving here
    }

    isTokenExpired = (token) => {
        try {
            const decoded = decode(token);
            if (decoded.exp < Date.now() / 1000) { // Checking if token is expired.
                return true;
            }
            else
                return false;
        }
        catch (err) {
            console.log("expired check failed! in AuthService.js component");
            return false;
        }
    }

    setToken = (idToken) => {
        // Saves user token to localStorage
        localStorage.setItem('id_token', idToken)
    }

    getToken = () => {
        // Retrieves the user token from localStorage
        return localStorage.getItem('id_token')
    }

    logout = () => {
        // Clear user token and profile data from localStorage
        localStorage.removeItem('id_token');
    }

    getConfirm = () => {
        // Using jwt-decode npm package to decode the token
        let answer = decode(this.getToken());
        console.log("Recieved answer!");
        return answer;
    }

    fetch = (url, options) => {
        // performs api calls sending the required authentication headers
        const headers = {
            'Accept': 'application/json',
            'Content-Type': 'application/json'
        }
        // Setting Authorization header
        // Authorization: Bearer xxxxxxx.xxxxxxxx.xxxxxx
        if (this.loggedIn()) {
            headers['Authorization'] = 'Bearer ' + this.getToken()
        }
        
        return fetch(url, {
            headers,
            ...options
        })
            .then(this._checkStatus)
            .then(response => response.json())
    }

    _checkStatus = (response) => {
        // raises an error in case response status is not a success
        if (response.status >= 200 && response.status < 300) { // Success status lies between 200 to 300
            return response
        } else {
            var error = new Error(response.statusText)
            error.response = response
            throw error
        }
    }
}

```

11. `withAuth.js` should have

```
//src/components/withAuth.js

import React, { Component } from 'react';
import AuthHelperMethods from './AuthHelperMethods';

/* A higher order component is frequently written as a function that returns a class. */
export default function withAuth(AuthComponent) {
    
    const Auth = new AuthHelperMethods();

    return class AuthWrapped extends Component {
       
        state = {
            confirm: null,
            loaded: false
        }

        /* In the componentDid<ount, we would want to do a couple of important tasks in order to verify the current users authentication status
        prior to granting them enterance into the app. */
        componentWillMount() {
            if (!Auth.loggedIn()) {
                this.props.history.replace('/login')
            }
            else {
                /* Try to get confirmation message from the Auth helper. */
                try {
                    
                    const confirm = Auth.getConfirm()
                    console.log("confirmation is:", confirm);
                    this.setState({
                        confirm: confirm,
                        loaded: true
                    })
                }
                /* Oh snap! Looks like there's an error so we'll print it out and log the user out for security reasons. */
                catch (err) {
                    console.log(err);
                    Auth.logout()
                    this.props.history.replace('/login');
                }
            }
        }

        render() {
            if (this.state.loaded === true) {
                if (this.state.confirm) {
                    console.log("confirmed!")
                    return (
                        /* component that is currently being wrapper(App.js) */
                        <AuthComponent history={this.props.history} confirm={this.state.confirm} />
                    )
                }
                else {
                    console.log("not confirmed!")
                    return null
                }
            }
            else {
                return null
            }

        }
    }
}

```

12. Update `App.js` with these.

```

//src/App.js

import React, { Component } from 'react';
//import { Link, Redirect } from 'react-router-dom';
import './App.css';

/* Once the 'Authservice' and 'withAuth' componenets are created, import them into App.js */
import AuthHelperMethods from './components/AuthHelperMethods';

//Our higher order component
import withAuth from './components/withAuth';

class App extends Component {

  state = {
    username: ''
  }
  /* Create a new instance of the 'AuthHelperMethods' compoenent*/
  Auth = new AuthHelperMethods();

  _handleLogout = () => {
    this.Auth.logout()
    this.props.history.replace('/login');
  }

  //Render the protected component
  render() {
    let name = null;
    if (this.props.confirm) {
      name = this.props.confirm.username;
    }
    //let name = this.props.confirm.username;
    console.log("Rendering Appjs!")
    return (

      <div className="App">
        <div className="App">
          <div className="main-page">
            <div className="top-section">
              <h1>Welcome, {name}</h1>
            </div>
            <div className="bottom-section">
              <button onClick={this._handleLogout}>LOGOUT</button>
            </div>
          </div>
        </div>
      </div>
    );
  }
}

//In order for this component to be protected, we must wrap it with what we call a 'Higher Order Component' or HOC.

export default withAuth(App);

```

13. Update `index.js` with these.

```
//src/index.js

import React from 'react';
import ReactDOM from 'react-dom';
import { BrowserRouter as Router, Route } from 'react-router-dom';
import './index.css';
import App from './App';
import Login from './login';
import Signup from './signup';
import registerServiceWorker from './registerServiceWorker';

/* Here we will create our routes right off the bat in order to 
prevent the user from getting very far in our app without authentication. */
ReactDOM.render(
    <Router>
        <div>
            <Route exact path="/" component={App} />
            <Route exact path="/login" component={Login} />
            <Route exact path="/signup" component={Signup} />
        </div>
    </Router>, document.getElementById('root'));
registerServiceWorker();

```

14. Start server using `yarn`.

```
sudo yarn start
```

### Create react app reference

```
https://facebook.github.io/create-react-app/
```

### Reference tutorial

```
https://github.com/rchvalbo/jwt_react_node_starting_template_complete
https://github.com/RobDWaller/ReallySimpleJWT
https://github.com/firebase/php-jwt
```
