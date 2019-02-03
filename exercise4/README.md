# Exercise 2 - Cloud Functions to interact with a Database

You know basic Cloud Functions now, so let's create some to do store and query from a database. We'll use a NoSQL DB called Cloudant.

Goals:
* Store and query data from a database from Cloud Functions 
* Learn how to:
    * Create packages to organize your Cloud Functions
    * Use other cloud services 
    * Connect to Cloudant from your Cloud Functions
    * Write Cloud Functions to make database calls


## Create a new package and create a Cloud Function inside of it

* Create a new package

``` 
ibmcloud fn package create serverlessworkshop
```

This is where we'll store all of our Cloud Functions from now on!

Why packages?
* Organize our codebase
* Packages give us the ability to set default parameters for all Cloud Functions in a package
* We can also set security credentials for other cloud services for a package - which means all Cloud Functions (also called Actions in OpenWhisk) will inherit these credentials as params
 
### Create a Cloud Function in our new package

* In this exercise4 folder:
    * ``` mvn package ```
    * ``` ibmcloud fn action create serverlessworkshop/getDBVersion target/create-engagement-fn.jar --main com.example.GetDBVersion ```
    
Now try to run it.. it wil fail because we haven't connected Cloudant to our Cloud Function package yet!

``` ibmcloud fn action invoke --result serverlessworkshop/getDBVersion ```    

We suggest you spend a few minutes looking at the code (GetDBVersion.java) and some of the other classes in this exercise4 project.

## Create a Cloudant database from the CLI

Let's create a new Cloudant database to link to this application.

* First, we'll need to get the list of regions:

``` 
ibmcloud regions        
Listing regions...

Name       Display name   
au-syd     Sydney   
jp-tok     Tokyo   
eu-de      Frankfurt   
eu-gb      London   
us-south   Dallas   
us-east    Washington DC 
```

Remember the region you originally setup your Cloud Functions to run in, and use that below as MY_REGION.

* Now create a free, lite version, of the Cloudant database.

``` 
ibmcloud resource service-instance-create cloudant-serverless cloudantnosqldb lite MY_REGION -p '{"legacyCredentials": true}'
```

Remember the name; we've called this Cloudant database: cloudant-serverless

* We need to create some credentials to access the database. This command will create a service key with admin (Manager) privileges for the cloudant-serverless database: 

``` ibmcloud resource service-key-create creds_cloudantserverless Manager --instance-name cloudant-serverless``` 

* You can get these credentials whenever you need them by running:

``` ibmcloud resource service-key creds_cloudantserverless```

* Make note of the URL, then use it to test the database connectivity from the command line:
``` curl https://59fedc56-ae8d-46aa-bc01-2b4fd1741271-bluemix:45821c744e54a4067a17b7c98cebc23bf19e25bbf6e4d1c0091196f4c4b6f7be@59fedc56-ae8d-46aa-bc01-2b4fd1741271-bluemix.cloudantnosqldb.appdomain.cloud```

* Set it in an ENV environment variable so we can run some more tests:

``` export CLOUDANT="https://59fedc56-ae8d-46aa-bc01-2b4fd1741271-bluemix:45821c744e54a4067a17b7c98cebc23bf19e25bbf6e4d1c0091196f4c4b6f7be@59fedc56-ae8d-46aa-bc01-2b4fd1741271-bluemix.cloudantnosqldb.appdomain.cloud"```

* Get list of cloudant db’s:
``` curl -s -X GET $CLOUDANT/_all_dbs```

* Create a new db called engagements:
```curl -s -X PUT $CLOUDANT/engagements```

* Add a new object into this db:
```curl -s -H "Content-type: application/json" -d "{\"name\":\"Jfokus\"}" -X POST $CLOUDANT/engagements```

* Get all data from this db:
```curl -s -X GET $CLOUDANT/engagements/_all_docs```

You can also browse the Cloudant database Web Console from the [IBM Cloud Resource](https://cloud.ibm.com/resources) page. Your Cloudant instance will be listed under 'Services'. Click it, then click 'LAUNCH CLOUDANT DASHBOARD' and you can see the data contained inside of your instance and create/delete/query.

## Bind our Cloudant database and credentials to our Cloud Functions package

We now need to bind the credentials to the package we created above, so the things contained within have access to the credentials as parameters.

* Create a service alias to bind the Cloudant instance
 
``` ibmcloud resource service-alias-create cloudant-serverless-alias --instance-name cloudant-serverless ```

* Create a service key and bind to the service alias

``` ibmcloud resource service-key-create cloudant-serverless-creds Manager --alias-name cloudant-serverless-alias```

* The credentials will show up  in the package list now:

```ibmcloud fn package refresh```

```/pratik.r.patel@NNNN_dev/Bluemix_cloudant-serverless-alias_cloudant-serverless-creds private ```

* You can now go and get the credentials; you will need the apikey and host to run the tests in this exercise:

```ibmcloud resource service-key cloudant-serverless-creds```

* Finally, bind the Cloudant service to OpenWhisk package 
```ibmcloud fn service bind cloudantNoSQLDB serverlessworkshop --instance cloudant-serverless-alias --keyname cloudant-serverless-creds ```

* You can now execute the getDBVersion Cloud Function you created above:

```ibmcloud fn action invoke --result serverlessworkshop/getDBVersion```

If you don't get something like this, find an instructor and get some help!

```{ "serverVersion": "2.1.1"}```

## Run Unit Tests locally

We've already created a full set of create/update/delete/find Cloud Functions for our mini-application. This app stores "Engagements" to Cloudant - engagements would be a customer meeting, conference, or meetup. The fields can be seen in the Engagements.java  file.

* Examine the source code for the Cloud Functions under: exercise4/src/main/java/com/example/
* Open up the unit test: exercise4/src/test/java/com/example/CreateEngagementTest.java
* The test has been marked as @Ignore because it's hitting the real Cloudant database
* In a previous section in this exercise, we asked you to note the apikey and host for your Cloudant instance. Go and set it in:

```exercise4/src/main/resources/local.properties``` 

like so:

``` cloudant_apikey=XXXXX```

```cloudant_host=XXXXXX```

* Once you've set this, you can run the unit tests locally! Examine each of the Cloud Functions, and run each unit test. Remember you can check the result in the Cloudant database console to see if your data is making into the DB!

## Deploy the Cloud Functions

* Deploy several of the Cloud Functions in this exercise and test them out!
    * CreateEngagement
    * FindEngagement
    * GetAllEngagements
    * UpdateEngagement
    
* We suggest you deploy all of them, then run them in the Web Console AND the CLI
* Use the Cloudant Web Console to check that your operations are making it to the database!    

(remember if you make changes to the Cloud Functions, you have to "mvn package" before you do an "ibmcloud fn update...")
## Exercise review

Our objectives in this exercise:

* Store and query data from a database from Cloud Functions 
* Learn how to:
    * Create packages to organize your Cloud Functions
    * Use other cloud services 
    * Connect to Cloudant from your Cloud Functions
    * Write Cloud Functions to make database calls

***Please ask your friendly workshop instructor if you have any questions!***

## EXTRA CREDIT
So you've finished before everyone else? No problem, here's some more stuff you can dive into!

* Fix CreateEngagement so that it actually takes in some inputs

## Extra Extra Credit

* Invoke a Cloud Function when a Cloudant database insertion occurs
(see exercise3 and https://cloud.ibm.com/docs/openwhisk/openwhisk_cloudant.html#openwhisk_cloudant)
* Call this Cloud Function "auditEngagement" and create a new Cloudant data record whenever a change occurs to an Engagement object
* Try to capture the change, and changed fields 

## Extra Extra Extra Credit 
* Create a new Object type to store to the database for a "person", follow the pattern in EngagementStore.java and the Engagement.java files. 
    * Try adding Person to Engagement as a "speaker" field. Does Cloudant automagically persist this to the database? What code will you need to write to make this happen?
    * Use the Cloudant API query guide to create a new Cloud Function to query by name and location for Engagement.
    
   
   
