# Firebase

The Firebase Realtime Database is a Google cloud-hosted database. Data is stored as JSON and synchronized in real time to every connected client.  It's an [NoSQL](https://en.wikipedia.org/wiki/NoSQL) cloud database.

Google offers two types/data models and both Realtime Database and Cloud Firestore are NoSQL Databases.  

1. Realtime Database

	* Stores data as one large JSON tree.
	* Simple data is very easy to store.
	* Complex, hierarchical data is harder to organize at scale.

2. Cloud Firestore

	* Stores data as collections of documents.
	* Simple data is easy to store in documents, which are very similar to JSON.
	* Complex, hierarchical data is easier to organize at scale, using subcollections * within documents.
	* Requires less denormalization and data flattening.


## Realtime Database usage in JavaScript projects

### Creation of database

1. Create account / log in, then create a project in which we can build database. Give it a name, enable or disable Google Analytics
2. Now in the project dashboard, in the menu click "Build", then "Realtime Database", then choose a server location (closest to you)
3. Choose security mode - "Locked mode" will be private and will require configuring the authentication and security rules while "Test mode" will be public and can be used right away. Use test while developing then secure the db.

Once database is created, url is offered and further used in code for configuring access to the database.

Example:
https://database-name-servers-location.firebasedatabase.app/

### Security rules & authentication

**TODO: Authentication


### In-project usage

1. Installation of the library

```powershell
npm install firebase
```


2. Imports

Two statements are needed, on to initialize the library app, other to import specific methods which the app will use.

Example with basic crud

```javascript

import { initializeApp } from "firebase/app";

import { getDatabase, ref, push, get, remove } from "firebase/database";

```


3. Preparing the database reference

```javascript

  

// TODO: Replace the following with your app's Firebase project configuration

// See: https://firebase.google.com/docs/web/learn-more#config-object

const firebaseConfig = {

  // ...

  // The value of `databaseURL` depends on the location of the database

  databaseURL: "https://DATABASE_NAME/rest-of-the-url"

};

// Initialize Firebase
const app = initializeApp(firebaseConfig);

// Initialize Realtime Database and get a reference to the service
const database = getDatabase(app);

// Create a reference which we furhter use in methods to talkt to the database
const dataInDb = ref(database);

```

  

6. Using methods for basic functionality

```javascript

// Read data
get(dataInDb).then(snapshot) => {
  // Snapshot is the returned payload structured as a specific object
  // First basic validation
  if(snapshot.exists()) {

    // We can work with it, it will return many things
    // To isolate just the data
    const data = snapshot.val();

  } else {

    // Null object in case the realtime db is empty
    console.log("Firebase: empty data set.");
    
  }
  
}.catch((error) => {

  // In case there are errors
  console.error(error);

});

// Write data
const yourStructuredData = {};

push(dataInDb, yourStructuredData);

// Purge the database (delete all)
remove(dataInDb);

// Update data
const updates {};

update(dataInDb, updates);

```

## References

1. [Official documentation](https://firebase.google.com/docs/database)