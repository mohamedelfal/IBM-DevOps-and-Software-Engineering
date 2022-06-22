# Final Project: Serverless Web Application and API
In the final project, you will have the opportunity to take your serverless and microservices skills and put them into action in a project that combines these technologies into a single web application.  
You will create a serverless web application -- a simple guestbook website where users can post messages -- and you will host the website in two different ways.

**architecture**  
![image](https://user-images.githubusercontent.com/100445644/175143309-823cc35f-326c-4d62-90fd-07ed8c265ec5.png)  
**Learning Objectives**  
Deploy a serverless backend and a database  
Expose a rest API  
Host a static website using object storage  
Deploy the static website as a microservice on Red Hat OpenShift   
   
**Troubleshooting**   
If at any point the IBM Cloud Functions UI doesn't seem up to date, perform a hard refresh of the webpage.    
In many browsers this is accomplished by clickng on the refresh button while holding the shift key.

## Step 1: Deploy a Cloudant database
The guestbook entries will be stored in a Cloudant database for persistence.   
IBM Cloudant is a fully managed JSON document database built upon and compatible with Apache CouchDB.  

Go to the [IBM Cloud catalog](https://cloud.ibm.com/catalog) and create an instance of Cloudant.  
Keep in mind the following things when creating your instance:  

The environment should be multitenant  
The authentication method should be IAM and legacy credentials  
Once the Cloudant instance is provisioned (the status is Active), click on it to go to the service instance page.  

Click on Launch Dashboard to open the dashboard in a new browser tab.  

In the upper right, click on Create Database.   
Enter guestbook as the database name and select Non-Partitioned under Partitioning.   
Click Create to create the database.  

Switch back to the browser tab with the service dashboard page.   
Go to Service credentials.  

Click New Credential.  

Set the name for-guestbook, and leave the role as Manager.   
Click Add to add the new credential.  

Expand the newly created credentials and review them.   
These credentials will allow Cloud Functions actions to read/write to your Cloudant service.  

## Step 2: Create actions to save guestbook entries
In order for the guestbook to write entries to the Cloudant database and subsequently read entries from the database, you will use Cloud Functions. In this section, you need to create actions with IBM Cloud Functions to write the guestbook entries to Cloudant. The code will be given to you, but you need to create the appropriate actions and sequences to accomplish this task.

A sequence of two actions will be used to create the entries in Cloudant. Given a name, email address, and comment, the sequence will create a document to be persisted and store that document in your Cloudant database.

1- Go to IBM Cloud Functions and create a namespace for this project.

2- Create new Node.js action called `prepare-entry-for-save` in a new package called `guestbook`.

3- The code for this action should be the following:

```.js
/**
 * Prepare the guestbook entry to be persisted
 */
function main(params) {
  if (!params.name || !params.comment) {
    return Promise.reject({ error: 'no name or comment'});
  }

  return {
    doc: {
      createdAt: new Date(),
       name: params.name,
       email: params.email,
       comment: params.comment
    }
  };
}
```

Take note of what this action does. It first verifies the parameters passed to this function -- it ensures that there is both a name and a comment. Then it returns a JSON string with a document that contains the date as well as the three parameters passed into the function.

4- Add this action to a new sequence called `save-guestbook-entry-sequence`.   
This `sequence` should also be in the `guestbook` package.  

5- Add a second action to this sequence by clicking the Add + button on the sequence page.   
Use the public `create-document` action from the Cloudant package.    
You'll need a new package binding, which you should name `binding-for-guestbook`, to bind your Cloudant instance to this action.     
When creating the binding, make sure to choose your correct Cloudant instance, credentials, and database for this project.     
Also make sure to save the sequence after you add this action.  

6- Test your work so far by going to the `save-guestbook-entry-sequence` and clicking Invoke with parameters.    
Use the following JSON parameters:
```.js
{
  "name": "John Smith",
  "email": "john@smith.com",
  "comment": "this is my comment"
}
```
If this worked, you should not get an error, but you should see the ID and the revision of the document just added.  

![image](https://user-images.githubusercontent.com/100445644/175167424-c372e978-dbe9-4ebb-8103-23040d81543b.png)

You should also be able to go to your Cloudant instance dashboard, open the guestbook database that you created, and see a newly created document there containing your parameters and the date.  
![image](https://user-images.githubusercontent.com/100445644/175167478-1ccafe3f-8214-4e2a-9cf7-3fb3069bb663.png)

