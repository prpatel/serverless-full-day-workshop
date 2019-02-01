# Exercise 3 - Sequences to weave together Cloud Functions

Sequences are how we invoke Cloud Functions back-to-back. You could write a Cloud Function A to invoke Cloud Functions B and C, but that is inefficient and costly - and most importantly, not idiomatic Serverless design. Your instructor will go into details on why this is the case. 

Goals:
* Construct a Cloud Function sequence 
* Learn how to:
    * Create a sequence 
    * Passing params between sequences

## Creating a sequence

Your instructor will discuss sequences, then you can run the following code to creat a sequence.


ibmcloud fn action invoke --result greetAndUpperFn --param name "Pratik"
* Create a sequence
```ibmcloud fn action create greetAndUpperFn --sequence greetingReceivedFn,upperCaseFn```
* Execute a sequence
```ibmcloud fn action invoke --result greetAndUpperFn --param name "Pratik"```

## Exercise review

Our objectives in this exercise:

* Construct a Cloud Function sequence 
* Learn how to:
    * Create a sequence 
    * Passing params between sequences

***Please ask your friendly workshop instructor if you have any questions!***

## EXTRA CREDIT
So you've finished before everyone else? No problem, here's some more stuff you can dive into!
