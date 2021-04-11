# Postman Tutorial


Link: https://www.youtube.com/watch?v=VywxIQ2ZXw4
Api: https://github.com/vdespa/introduction-to-postman-course/blob/main/simple-books-api.md

Postman is for *communicating with APIs"

Postman is ok for "security testing" (but is not is main goal...)

Postman *isn't* for:
- User interaction
- Performance testing

## Workspace
A workspace is just a way to organize postman collections.

## HTTP /Hyper Text Transfer Protocol

Postman is a web client. It can be used to send request to Http Servers.
Postman allow to send request (The upper part of the display) and to botain a response from the server (below zone)

*Headers* : metainfo added to req / res.

## Collections & Variables

A collection is a list of requests related to the same API.

To save a server url as a variable
- Set as variables on a URL -> Set as a new variable
*ACHTUNG* : should not have the "/"
If the variable is saved with the _scope_ of Collection, will be used only inside the collection.
A varaible can also be *global* (visible inside all the workspace) or *environment* (for production/ staging / develop)

To see all the *collection's variables*, go to:
1. Hover on the collection names
2. Three dots
3. Tab Variables
The *initial value* of a variable is the value that will be presentend to others when the collection is shared.
The *current value* is what ultimately Postman use, and is perfect for storing secrets.

## Query Parameters

It is possible to add query parameters in postman for a specific request directly in the "Params" tab of the request.

## Path Variables

/books/:bookId

This creates automatically a Path Variable "bookId".
This variable should be assigned the value of the id of the element that sould be read.
Postman replaces the :bookId with the value assigned to the field.

## POST request

Used to send data to server.

### Authentication
Some API requires an authentication. Most of the time, the authentication is rappresented by an *access token*.

Once obtained the access token, go to the collections variables and set it.
(This is the case where the access token should not be shared across users, so the _initial value_ must be "---" and
the token value should go in the *current value*)

The value can be passed in the private api via "Authorization" -> *Bearer Token*
( This put the "Authorization" key insde the headers of the request)

## Random Test Data

Using the same data over api will not spot potential issues. It's a good practice while testing to randomize data.

For example, to send a random name: "{{$randomFullName}}".

To know what is sent, it is possible to check inside the console (situated bottom left).

Inside the *console* it is possible to view all the request done.

## PATCH Request
To updated some resource in the server.
It can return a 204.

## DELETE Request
To delete some resource in the server.

# AUTOMATIC API TESTING

## Hello Postman Api Test
For example, it is wanted a worflow like: get lsit of book, create a books, etc..

To start, select an API and go to the Tab *tests*.
Here is possible to write javascript test.

For example, to check if an API returns 200:

```js
pm.test("Status code is 200", function () {
    pm.response.to.have.status(200);
});
```

Once the test is defined, it is possible to view the results inside the response after submitting a request.

Another example: Check a value inside a JSON response.

1. Firstly, the JSON must be *parsed*

```js
// Takes the response parsed ina javascript object
const response = pm.response.json();
```

Tips: response['status'] can be used to extract a property with some kind of strange text inside (like special characters)

A *test* in Postman is written in this way:

```js
pm.test("Test Name", () => {
    // Callback
    // Assertion to check if a variable is deeply equals to others
    pm.expect(variable).to.eql();
});
```

## Testing with variables

Some APIs can share a single id (like reading, patching and deleting the same resource), for this it is possible
to store that value as a variable.
1. Copy the value inside the _response_
2. Click on the *eye* top-right ("Environment quick look")
3. Global Variables -> edit 
4. Declare the variables as same as the collection variable.

Now is simple to customized the variable via the global view.

## Extracting Data from the Response

It is possible to assign a variable after a response inside the Tests section:

pm.globals.set("bookId", nonFictionBooks[0].id);

```js
const response = pm.response.json();

// takes only the avaiables book
const nonFictionBooks = response.filter((book) => book.available === true);

const book = nonFictionBooks[0];

// This test allow to check the case where there aren't books.
pm.test("Book found", () => {

    pm.expect(book).to.be.an('object');
    pm.expect(book.available).to.be.true; // same as to.eql(true);
});

pm.globals.set("bookId", book.id);
```

## Collection Runner
Allows to execute the entire collection with just a click.

To do so, click on the bottom or on the 3 dots of a collection.
Drag n' droppin the request inside the runner will allow to decide the order to execute the request.
It's *important* to check "Save response" in order to watch what happened.

## Request Order (Jumping)

Inside the *test* script of a request, it is possible to specify which is the next request.
The parameter of this method must be the request name specified in Postman.
This allows to skip one or more request (like a jump).

```js
postman.setNextRequest('List of Books');
```

*ACHTUNG* : this can generate loops!

Another way is to specify the test to stop (putting the request that aren't to test after):
```js
postman.setNextRequest(null);
```

## Postman Monitors

From the _collection options_ or from Monitors -> create a monitor

Monitors are collection tester runned by Postman (so it should have secrets!)

Monitors can be scheduled!

## Newman
Newman is the command line collection runner for Postman. This allows to execute Collection Runners on
build servers.

It is installed via Node Package Manager.

To run via Newman a collection, firstly *export* the collection.
There are 3 ways to export a postman collection:
- Export via the collection options "Schare Collection" -> Get Public Link
    (! The link is not instanlty updated when the collection is updated. Must be done _manually_)

    With this methods > newman run public-link-to-the-postman-collection

- Export the collection as JSON.

    With this method, just run: > newman run collection_name.json

- By Postman API with api key.

It is possible to exports newman reports *visually* with newman HTML Reporter:

    _Install_: > npm install -g newman-reporter-htmlextra

    _Use_: > newman run link-to-postman-collection --reporters cli,htmlextra

This generates a folder with the html reporter.

## CI / CD Overview

It is possible to use Postman via Newman to test the "after deploy" of a pipeline to test API.


### Extra: Postman prefilled Headers

- User-Agent : who is making the request.

### Extra: Some http methods

- 200 : All ok
- 201 : Created (The request has been fullfilled and returns a new resource created)
- 204 : No Content (No body inside the response)

- 400 : something wrong in the request
- 401 : Unauthorized: user not allowed
- 409 : Conflict ()

