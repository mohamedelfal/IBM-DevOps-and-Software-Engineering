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

