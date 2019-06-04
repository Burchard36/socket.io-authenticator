# socket.io-authenticator
A NodeJS Based backend server to handle your user information!

## What am i?

This is a NodeJS that is avaliabe for public use. It utilizes a MySQL DB For storing users data, so you dont risk corrupt JSON files. Upon registering, the users details (Username, Email, and password) are put into JSON Data and sent to this server (You need to do the front end part of the server IE: The JavaScript inside your website). Once data it received, the users details are scanned for any type of SQL/JavaScript injections while i do deem this system `secure` i do have to put this in : **DISCLAIMER**: **Dalton Burchard (Content creator) IS NOT RESPONSIBLE FOR ANY DATA BREACHES ON YOUR NETWORK USE THIS AT YOUR OWN RISK**. You can never be to sure with a login system, but if any reports of vulnerabilitys are found, i will fix it ASAP. After the users details are scanned, their details are stored into the database, then an email is sent to theyre email address that send them a code they need to use to "Finish the registration process". If the user does not type in the code provided by the email server (Its built in), theyre details are completly wiped from the server to eliminate overcrowding on your database. The login system is a login system so yeah :P.

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

Secondly you need to configure the Mailing server, this is built into the app.js so no need to gather anything additional. 
There is 2 sections to this mailinig system so listen carefully, on lin `7` there is a function, here is the function you should fix up:
```javascript
//Mailing system
var transporter = nodemailer.createTransport({
    service: 'gmail',
    auth: {
        user: 'your gmail username',
        pass: 'your gmail password'
    }
});
```
Thats pretty basic right? **Its not.** You also need to go to `https://support.google.com/accounts/answer/185833?hl=en` and then click on `"How to generate an app password"` follow directions there and boom.

Now that you are logged in. there is a function on line `98` fill it in with these:
```javascript
var mailOptions = {
    from: 'Your email here',
    to: email,
    subject: 'Your subject here',
    html: '<h2 style="text-align: center;">Put some stuff here</h2><p style="text-align: center;">Your registration code is ' + code + '</p><p style="align-right;">You have 10 minutes to activate your account or it gets deleted!</p>',
};
```
[**DEV NOTE**] 
**DO NOT REMOVE THAT "code" THINGYMAHOOZER, DOING SO WILL MESS UP YOUR ENTIRE SYSTEM. (You can move it around though)**

alrighty, thats it:)

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
