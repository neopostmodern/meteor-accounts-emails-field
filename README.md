[![Build Status](https://travis-ci.org/splendido/meteor-accounts-emails-field.svg?branch=master)](https://travis-ci.org/splendido/meteor-accounts-emails-field)
accounts-emails-field
=====================

This is a Meteor package which maintains the `emails` array field inside the [user object](http://docs.meteor.com/#meteor_users) up to date with any 3rd-party account service email used by the user to login into the application.

It exploits the [`onLogin`](https://github.com/meteor/meteor/blob/b37fd2af7e028a474ee5aad25b27994fb2814bf1/packages/accounts-base/accounts_server.js#L50) hook from the Accounts object to check the user object after every successful login and possibly updates the content of its `emails` field.

In particular:

* email addresses used with 3rd-party services are added to the `emails` field
* email added with `accounts-password` service which are still not validated but appear inside some 3rd-party service as validated are promotes to validated also inside the `emails` field.
* emails which do not appear anymore inside some 3rd-party service are deleted from the `emails` field. In case the `accounts-password` service is used, the first email appearing inside the array, which is the one supposed to belong to the `accounts-password` service is anyway preserved!

### Advantages:

Having an up-to-date `emails` field should permit to find a registered user by any of its email addresses with a simple function like this:

```Javascript
var findUserByEmail = function(emailAddress){
    return Meteor.users.findOne({"emails.address": emailAddress});
};
```

This should, among other things, permit to check whether a newly registered user has already another account for the application. This could happen when different account services are used at the same time (e.g. `accounts-password`, `accounts-google`, `accounts-github`, etc.) and the user don't remember with which service logged in originally. A simple check on the email address could reveal this uncomfortable scenario and permit the application to warn the user or even propose her to merge the two accounts...


### Installation

```Shell
mrt add accounts-emails-field
```

### Usage

Nothing to do... just install it and you're ready to go!

### WIP

**This project is still Work In Progress**: any comments, suggestions, testing efforts, and PRs are very very welcome. Please use the [repo](https://github.com/splendido/meteor-accounts-emails-field) page for issues, discussions, etc.

In particular it would be very useful to test the package with as many accounts services as possible to confirm it is correctly functioning.
_**The major question mark is about the name used by each service to provide the email address**_ (so far `email` and `emailAddressed` were observed) and _**the presence of a field telling whether the same address was verified or not**_ (so far only for accounts-google a field called `verified_email` was found).

#### Instructions for testers

Please try the following:

```Shell
meteor create test-accounts-emails-filed && cd test-accounts-emails-filed
mrt add bootstrap-3
mrt add accounts-ui-bootstrap-3
meteor add service-configuration
mrt add accounts-YOUR_PREFERRED_SERVICE
mrt add accounts-emails-filed
mrt install
mkdir server
touch server/accounts.js
```

Then add the configuration required for your test app registered for the chosen service and finally run the application with:

```Shell
meteor
```

Head your browser to `localhost:3000` and try to login with your service. After this open the mongo shell with

```Shell
meteor mongo
```

from the same app folder but from a different terminal (while the testing app is still running) and type

```Shell
db.users.find().pretty()
```

Please try to confirm that the email you used to register to the service was added to the `emails` field and try to see if it is marked as verified or not.
In case something is unexpected, please try to see under `user.services.YOUR_PREFERRED_SERVICE` which is the name for the email field (if any!) and whether there is another field stating the verified status of the email address (if any...).

In any case, please comment/add an issue having the same name of the service with the test outcome.

Big Tnx in advance to anyone willing to test `accounts-emails-filed`!!!