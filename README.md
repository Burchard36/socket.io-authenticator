# socket.io-authenticator
A NodeJS Based backend server to handle your user information!

## How does it work?

This project works by using a NodeJS Library called socket.io, it is able to emit data back in forth between a website,

To add this project to your website, you will first need a `MySQL Database` without this it will **NOT** work.

## Getting started
first you need to acquire a MySQL Database, once you do inside the `app.js` file on line `23` with this function:

```javascript


//Connect to mysql database
const con = mysql.createConnection({
    host: "Your database host",
    port: "Your database port",
    user: "your database username",
    password: "your database password",
    database: 'your database name'
});
```

I would hope that is clear enough instruction, fill in the strings with the needed information

Next you need to enable the emailing system (The email only sends a confirmation code that the user has to type in.

**Great so now what?**

Alrighty, this part will be tricky, so i will divide them up into a few sections

## Setting up registering

Glad you made it this far! okay so in order to get the register setup you need to make a form that has the following
data inputs:

`username`
`email`
`password`

[**DEV NOTE**]
Usernames and passwords are alphanumeric, and can be any length, i may change the length for usernames and passwords one day

[**DEV NOTE**]
Confirming emails/passwords are not done server side, i simply just did this on he front end with javascript. I will not provide an example as that is extremely basic. This *Might* be added to the server, if requested by enough people

now that you have the data collected it needs to be formatted into a JSON String, this is really simple:

```javascript
let username = ""; // Username you collected from the form
let email = ""; // Email you collected from the form
let password = ""; // email you collected from the form

let data = { Username: username, Email: email, Password: password };
```

**Alright, we now have the username email and password in a JSON Format, what to we flippin do now?**
`simple.`

```javascript
socket.emit("registerUser", data);
```

No seriously thats it. your done with setting up the register system. 

[**DEV NOTE**] 
email codes are only valid for ~10 minutes, you can change this if you look at the code/understand how NodeJS setInterval functions work.

[**README BEFORE OPENING A COMPLAINT**]

`After emitting this data, the server also emits data back to the webpage!!!`

You can catch these emitions by doing the following:
```javascript
socket.on("Insert Emition Here", function(data) {
    // data return the data you send, or undefined.
});
```
^^ You put this right after the socket.emit code you put above

`If the data entered is invalid, socket emits this code:`
```javascript
socket.emit("sqlInjection", data);

// Catch this emit like this:
socket.on("sqlInjection", function(data) {
    // data return the data you send, or undefined.
});
```

`If the username entered is already taken`
```javascript
socket.emit("usernameTaken", data);

// Catch this emit like this:
socket.on("usernameTaken", function(data) {
    // data return the data you send, or undefined.
});
```

`If the email entered is already taken`
```javascript
socket.emit("emailTaken", data);

// Catch this emit like this:
socket.on("emailTaken", function(data) {
    // data return the data you send, or undefined.
});
```

`If the email is invalid`
```javascript
socket.emit("invalidEmail", data);

// Catch this emit like this:
socket.on("invalidEmail", function(data) {
    // data return the data you send, or undefined.
});
```

`If the registering is succesfull`
```javascript
socket.emit("registerSuccess", data);

// Catch this emit like this:
socket.on("registerSuccess", function(data) {
    // data return the data you send, or undefined.
});
```
## Setting up confirmation email

This is super simple. You will need a HTML Page for the user to type in the confirmation email code they receive (Basically just a form).

now just collect that data like so in a JSON Format :
```javascript
let code = ""; // Code you collected from the form

let data = { Code: code };
```

**Alright, we have the code stored in a JSON Format, now what?**

`Simple` 
emit the data to the server
```javascript
socket.emit("confirmEmail", data);
```


## Setting up login
