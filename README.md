# Full Day Serverless workshop!

This is an instructor led workshop to teach you the basic principles of Serverless computing. If you'd like to run this workshop at your meetup, conference, or company, please contact @prpatel!(note: it's free!)


## Prerequisites: 
1. Do the setup here: [Setup for workshop](https://github.com/prpatel/Serverless-Workshop-Setup-All-Platforms)
2. Make sure to have Java 8 installed
3. You'll need maven to package the jar file

### Some basic programming knowledge and command line skills are needed. You can copy and paste most of the steps, but what fun is that?


### Build and deploy your first Serverless Java App!

From where you cloned this git repo:
1. mvn package  
2. ibmcloud fn action create helloJava target/hello-world-java.jar --main com.example.FunctionApp
3. ibmcloud fn action invoke --result helloJava --param name World

You should see:
{
    "greetings": "Hello! Welcome to OpenWhisk"
}

"--result" means just show the results. Omit that, and see what you get back :)
This also adds the "--blocking" flag, discussed below.

### Get to know some OpenWhisk commands

* ibmcloud wsk list
    * note that "wsk" is used here instead of fn! The ibmcloud" command wraps the OpenWhisk "wsk" command with either "fn" or "wsk"!
* ibmcloud wsk action invoke --blocking helloJava
    * this invokes the action in an sync fashion, you'll get a alot of text back, but you're interested in this:
    ***
        "activationId": "13a3f62589214b7da3f62589214b7d1e",
    ***
    * this is what you'll use to get the result back later!
* ibmcloud wsk action invoke helloJava
    * this invokes the action in an sync fashion, with something called an activationId
        ***
            ok: invoked /_/helloJava with id 5923d0321aa04fffa3d0321aa0cfffc2
        ***
        * this is what you'll use to get the result back later!
        * ibmcloud wsk activation result 5923d0321aa04fffa3d0321aa0cfffc2
        ***
                        
            {
                "greetings": "Hello! Welcome to OpenWhisk"
            }
        ***        
    * To  get the last invocation result: ibmcloud wsk activation result --last
    * To get everything (not just result): ibmcloud wsk activation get 5923d0321aa04fffa3d0321aa0cfffc2

    
* ibmcloud fn action update helloJava target/hello-world-java.jar --main com.example.FunctionApp
    * This will update the Cloud Function!              

### What about logs?

* Each activation has logs!
* ibmcloud wsk activation get --last
    * look for  "logs"
* I miss my "tail -f" ... how do I do that?
    * ibmcloud wsk activation poll
    