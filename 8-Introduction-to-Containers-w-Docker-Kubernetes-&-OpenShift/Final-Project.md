# Final Project: Scenario and Review Criteria
## Project Overview
In this final project, you will build and deploy a simple guestbook application.  
You will roll out updates using Openshift image streams.   
You will be rolling out a multi-tier version of the guestbook application.   
Finally, you will create and bind a tone analyzer service instance to your application and autoscale the guestbook.  

## Review Criteria
After completing the hands-on lab: Build and Deploy a Simple Guestbook App, you will complete the peer-graded assignment and be graded on the following nine tasks.

For each of the nine tasks, provide a screenshot and upload the JPEG (.jpg) file for your peers to review when you submit your work.

* Task 1: Deploy a simple v1 guestbook application. (2 points)

* Task 2: Use an in-memory data store for the simple guestbook application. (1 point)

* Task 3: Update the simple guestbook homepage to include your name. (1 point)

* Task 4: Automatically deploy the homepage update using a second image stream tag. (1 point)

* Task 5: Deploy the second version of the guestbook application using an OpenShift build. (5 points)

* Task 6: Deploy a Redis master, a Redis slave, and an analyzer microservice.(3 points)

* Task 7: Use Redis for the v2 guestbook application instead of an in-memory datastore. (1 point)

* Task 8: Submit entries to the guestbook and have their tone analyzed. Some simple sentences will not have a tone detected. Ensure that you submit something complex enough so that its tone is detected.(2 points)

* Task 9: Create a Horizontal Pod Autoscaler that shows guestbook as the scale target, the current and desired replicas as three, and the last scale time as the time the deployment was scaled up to three replicas (4 points)
#
## Final Project
Objectives
In this lab, you will:  

Build and deploy a simple guestbook application  
Use OpenShift image streams to roll out an update  
Deploy a multi-tier version of the guestbook application 
Create a Watson Natural Language Understanding service instance on IBM Cloud  
Bind the Natural Language Understanding service instance to your application  
Autoscale the guestbook app  


## Project Overview
Guestbook application
Guestbook is a simple, multi-tier web application that we will build and deploy with Docker and Kubernetes.  
The application consists of a web front end, a Redis master for storage, a replicated set of Redis slaves, and a Natural Language Understanding service that will analyze the tone of the comments left in the guestbook.    
For all of these we will create Kubernetes Deployments, Pods, and Services.  

There are two versions of this application. Version 1 (in the v1 directory) is the simple application itself, while version 2 (in the v2 directory) extends the application by adding additional features that leverage the Watson Natural Language Understanding service.  

We will deploy and manage this entire application on OpenShift.  

#
### Verify the environment and command line tools
1-If a terminal is not already open, open a terminal window by using the menu in the editor: `Terminal > New Terminal`
>Note: Please wait for some time for the terminal prompt to appear.
![image](https://user-images.githubusercontent.com/100445644/173707127-61d030f5-b50e-4c54-8364-cc2dd877a72e.png)

2-Change to your project folder.  
>Note: If you are already on the `/home/project` folder, please skip this step.
```shell
cd /home/project
```

3-Clone the git repository that contains the artifacts needed for this lab.
```shell
[ ! -d 'CC201' ] && git clone https://github.com/ibm-developer-skills-network/guestbook
```
![image](https://user-images.githubusercontent.com/100445644/173707499-07cdaea6-9bac-4d01-9c1a-b51241fe6687.png)

4-Change to the directory for this lab.
```shell
cd guestbook
```

5-List the contents of this directory to see the artifacts for this lab.
```shell
ls
```
![image](https://user-images.githubusercontent.com/100445644/173707623-51cda1fb-a2d6-4cd7-98ff-f04cf525f70c.png)


### Build the guestbook app
To begin, we will build and deploy the web front end for the guestbook app.

1- Change to the `v1/guestbook` directory.
```
cd v1/guestbook
```

2-Run the following command or open the Dockerfile in the Explorer to familiarize yourself with it. The path to this file is `guestbook/v1/guestbook/Dockerfile`.   This Dockerfile incorporates a more advanced strategy called multi-stage builds, so feel free to read more about that [here](https://docs.docker.com/develop/develop-images/multistage-build/?utm_medium=Exinfluencer&utm_source=Exinfluencer&utm_content=000026UJ&utm_term=10006555&utm_id=NA-SkillsNetwork-Channel-SkillsNetworkCoursescc20117568655-2021-01-01).
```
cat Dockerfile
```
![image](https://user-images.githubusercontent.com/100445644/173708026-e0c2bcca-8d7c-461d-b955-a9638d1cb715.png)

3-Export your namespace as an environment variable so that it can be used in subsequent commands.
```
export MY_NAMESPACE=sn-labs-$USERNAME
```

4-Build the guestbook app.
```
docker build . -t us.icr.io/$MY_NAMESPACE/guestbook:v1
```
![image](https://user-images.githubusercontent.com/100445644/173708317-ba46f6fb-e8d8-4e3e-ab0c-e0386af6e341.png)

5-Push the image to IBM Cloud Container Registry.
```
docker push us.icr.io/$MY_NAMESPACE/guestbook:v1
```
![image](https://user-images.githubusercontent.com/100445644/173708550-5e4a89fe-3561-4f59-8875-433e80908f41.png)

>Note: If you have tried this lab earlier, there might be a possibility that the previous session is still persistent. In such a case, you will see a 'Layer already Exists' message instead of the 'Pushed' message in the above output. We recommend you to proceed with the next steps of the lab.

6-Verify that the image was pushed successfully.
```
ibmcloud cr images
```
![image](https://user-images.githubusercontent.com/100445644/173708687-0f8fda53-db2a-4e3e-b447-106807a2583e.png)

>Note: If you see the status of the image as 'Scanning', please wait for 10 minutes & re-run the above command till the status gradually changes to 'No Issues'. Even if status shows as '1 issue', you can still proceed with lab.




## Deploy guestbook app from the OpenShift internal registry
As discussed in the course, IBM Cloud Container Registry scans images for common vulnerabilities and exposures to ensure that images are secure. But OpenShift also provides an internal registry -- recall the discussion of image streams and image stream tags. Using the internal registry has benefits too. For example, there is less latency when pulling images for deployments. What if we could use both—use IBM Cloud Container Registry to scan our images and then automatically import those images to the internal registry for lower latency?  

1- Create an image stream that points to your image in IBM Cloud Container Registry.
```
oc tag us.icr.io/$MY_NAMESPACE/guestbook:v1 guestbook:v1 --reference-policy=local --scheduled
```
With the `--reference-policy=local` option, a copy of the image from IBM Cloud Container Registry is imported into the local cache of the internal registry and made available to the cluster's projects as an image stream. The `--schedule` option sets up periodic importing of the image from IBM Cloud Container Registry into the internal registry. The default frequency is 15 minutes.
![image](https://user-images.githubusercontent.com/100445644/173708984-9eb380b9-d473-458a-b9ae-1b546157bae9.png)

Now let's head over to the OpenShift web console to deploy the guestbook app using this image stream.

2-Open the OpenShift web console using the link at the top of the lab environment.

![image](https://user-images.githubusercontent.com/100445644/173709455-a02e2bcb-b03a-4308-a980-2e3ce5b3a6a0.png)

>Note: Currently we are experiencing certain difficulties with the OpenShift console . If your screen takes time in loading, please close the OpenShift console browser tab & re-launch the same. It may take upto 10 mins to load the screen.

3-From the Developer perspective, click the +Add button to add a new application to this project.
![image](https://user-images.githubusercontent.com/100445644/173709593-eb3a1eb6-dc08-492a-83a1-699d4e13c7b8.png)

4-Click the Container Image option so that we can deploy the application using an image in the internal registry.
![image](https://user-images.githubusercontent.com/100445644/173709847-05db148b-82b6-4265-ab0a-056c8b9ddb7c.png)

5-Under Image, switch to "Image stream tag from internal registry".
![image](https://user-images.githubusercontent.com/100445644/173709926-97a99527-22a0-46b2-8a43-2c160f0388e4.png)

6-Select your project, and the image stream and tag you just created (guestbook and v1, respectively). You should have only have one option for each of these fields anyway since you only have access to a single project and you only created one image stream and one image stream tag.
![image](https://user-images.githubusercontent.com/100445644/173710091-eaafae32-67aa-4df4-a9b8-6f4141971971.png)

7-Keep all the default values and hit Create at the bottom. This will create the application and take you to the Topology view.
![image](https://user-images.githubusercontent.com/100445644/173710232-223521d3-e814-4c0b-bddd-cf9232dae965.png)

8-From the Topology view, click the guestbook Deployment. This should take you to the Resources tab for this Deployment, where you can see the Pod that is running the application as well as the Service and Route that expose it.
>Kindly wait as the deployments in the Topology view may take time to get running.
![image](https://user-images.githubusercontent.com/100445644/173710386-56b9cf39-3d7f-4f5b-a9a5-888ab37398f0.png)

9-Note: Kindly do not delete the opensh.console deployment in the Topography view as this is essential for the OpenShift console to function properly.

>Note: Please wait for status of the pod to change to 'Running' before launching the app.

📷Kindly take the screenshot of the guestbook for the final assignment.

![image](https://user-images.githubusercontent.com/100445644/173710961-8c9dbe59-b624-43d1-9bfb-054ef37b7185.png)

10-Try out the guestbook by putting in a few entries. You should see them appear above the input box after you hit Submit.
![image](https://user-images.githubusercontent.com/100445644/173711018-ce3ab2ca-228c-472f-b4b3-62511ec1e9a5.png)


## Update the guestbook
Let's update the guestbook and see how OpenShift's image streams can help us update our apps with ease.

1-Use the Explorer to edit `index.html` in the `public` directory. The path to this file is `guestbook/v1/guestbook/public/index.html`.
![image](https://user-images.githubusercontent.com/100445644/173711346-f0fe9121-4e7a-48a8-ae6e-161c54d81902.png)

2-Let's edit the title to be more specific.   
On line number 12, that says `<h1>Guestbook - v1</h1>`, change it to include your name.    
Something like `<h1>Mohamed's Guestbook - v1</h1>`. Make sure to save the file when you're done.  
![image](https://user-images.githubusercontent.com/100445644/173711607-d36cd38b-8226-4890-9ab0-b725ae2a1ed5.png)

```html
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta content="text/html; charset=utf-8" http-equiv="Content-Type">
    <meta charset="utf-8">
    <meta content="width=device-width" name="viewport">
    <link href="style.css" rel="stylesheet">
    <title>Lavanya's Guestbook - v1</title>
  </head>
  <body>
    <div id="header">
      <h1>Mohamed's Guestbook - v1</h1>
    </div>

    <div id="guestbook-entries">
      <link href="https://afeld.github.io/emoji-css/emoji.css" rel="stylesheet">
      <p>Waiting for database connection... <i class='em em-boat'></i></p>
      
    </div>

    <div>
      <form id="guestbook-form">
        <input autocomplete="off" id="guestbook-entry-content" type="text">
        <a href="#" id="guestbook-submit">Submit</a>
      </form>
    </div>

    <div>
      <p><h2 id="guestbook-host-address"></h2></p>
      <p><a href="env">/env</a>
      <a href="info">/info</a></p>
    </div>
    <script src="jquery.min.js"></script>
    <script src="script.js"></script>
  </body>
</html>

```

3-Build and push the app again using the same tag. This will overwrite the previous image.
```
docker build . -t us.icr.io/$MY_NAMESPACE/guestbook:v1 && docker push us.icr.io/$MY_NAMESPACE/guestbook:v1
```
  
![image](https://user-images.githubusercontent.com/100445644/173712919-dce0ac1c-790c-4cce-9e44-0010fb55331b.png)  

4-Recall the --schedule option we specified when we imported our image into the OpenShift internal registry. As a result, OpenShift will regularly import new images pushed to the specified tag. Since we pushed our newly built image to the same tag, OpenShift will import the updated image within about 15 minutes. If you don't want to wait for OpenShift to automatically import the image, run the following command.
```
oc import-image guestbook:v1 --from=us.icr.io/$MY_NAMESPACE/guestbook:v1 --confirm
```
![image](https://user-images.githubusercontent.com/100445644/173713022-dd5a1ccf-a965-446f-8979-ad597df50fb8.png)  

5-Switch to the Administrator perspective so that you can view image streams.  
![image](https://user-images.githubusercontent.com/100445644/173713061-ca214078-ccd3-478b-81e3-61d046311ef3.png)  

6-Click Builds > Image Streams in the navigation.  
![image](https://user-images.githubusercontent.com/100445644/173713166-371cbd1b-b0f8-4445-8b20-2084ac795c1f.png)  

7-Click the guestbook image stream.  
![image](https://user-images.githubusercontent.com/100445644/173713407-c122dc84-c350-484d-b7b6-181fb9047cf4.png)  

8-Click the History menu. If you only see one entry listed here, it means OpenShift hasn't imported your new image yet. Wait a few minutes and refresh the page. Eventually you should see a second entry, indicating that a new version of this image stream tag has been imported. This can take some time as the default frequency for importing is 15 minutes.
>📷Kindly take the screenshot of the image stream showing two distinct version for the final assignment.  

![image](https://user-images.githubusercontent.com/100445644/173713545-107e91d5-d48a-44fa-94bc-4c1faa8ac007.png)  

9-Return to the Developer perspective.  
![image](https://user-images.githubusercontent.com/100445644/173713959-b7c17f3f-e447-444e-b577-1a57bb8f5427.png)   

>Note: Please wait for some time for the OpenShift console & the Developer perspective to load.

10-View the guestbook in the browser again. If you still have the tab open, go there. If not, click the Route again from the guestbook Deployment. You should see your new title on this page! OpenShift imported the new version of our image, and since the Deployment points to the image stream, it began running this new version as well.
>📷Kindly take the screenshot of the updated guestbook for the final assignment.  

![image](https://user-images.githubusercontent.com/100445644/173714350-242b51aa-e1a6-4d06-967c-37b950286d15.png)  


## Guestbook storage
1-From the guestbook in the browser, click the `/info` link beneath the input box.   
This is an information endpoint for the guestbook.
![image](https://user-images.githubusercontent.com/100445644/173714759-76ea2f32-9a45-4944-9f36-4905d881245d.png)

Notice that it says "In-memory datastore (not redis)". Currently, we have only deployed the guestbook web front end, so it is using in-memory datastore to keep track of the entries. This is not very resilient, however, because any update or even a restart of the Pod will cause the entries to be lost. But let's confirm this.

>📷Kindly take the screenshot of the In-memory datastore for the final assignment.

![image](https://user-images.githubusercontent.com/100445644/173714888-9b71b257-5dd9-4e5b-b3cc-62532796def9.png)

2-Return to the guestbook application in the browser by clicking the Route location again.   
You should see that your previous entries appear no more.   
This is because the guestbook was restarted when your update was deployed in the last section.   
We need a way to persist the guestbook entries even after restarts.  
![image](https://user-images.githubusercontent.com/100445644/173715181-8f5e7f25-f1de-4236-a019-1bd7d4283ad9.png)
>Note: Currently we are experiencing certain difficulties with the OpenShift console. There is a possibility that you will see your old entries because the image stream takes time in updating. You may move ahead with the further steps of lab.

## Delete the guestbook
In order to deploy a more complex version of the guestbook, delete this simple version.

1-From the Topology view, click the `guestbook-app` application.   
This is the light gray circle that surrounds the `guestbook` Deployment.  
![image](https://user-images.githubusercontent.com/100445644/173715496-f1cb7556-0911-45ec-a4b8-5000dd2b5a1e.png)  
2-Click Actions > Delete Application.
![image](https://user-images.githubusercontent.com/100445644/173715545-7cdad1b5-c6fa-42ed-a9b5-50298b0b9060.png)


2-Type in the application name and click Delete.  
![image](https://user-images.githubusercontent.com/100445644/173715592-732de24d-4899-4267-8323-82e9e5b42934.png)
 


## Deploy Redis master and slave
We've demonstrated that we need persistent storage in order for the guestbook to be effective. Let's deploy Redis so that we get just that. Redis is an open source, in-memory data structure store, used as a database, cache and message broker.

This application uses the v2 version of the guestbook web front end and adds on 1) a Redis master for storage, 2) a replicated set of Redis slaves, and 3) a Python Flask application that calls a Watson Natural Language Understanding service deployed in IBM Cloud to analyze the tone. For all of these components, there are Kubernetes Deployments, Pods, and Services. One of the main concerns with building a multi-tier application on Kubernetes is resolving dependencies between all of these separately deployed components.

In a multi-tier application, there are two primary ways that service dependencies can be resolved. The v2/guestbook/main.go code provides examples of each. For Redis, the master endpoint is discovered through environment variables. These environment variables are set when the Redis services are started, so the service resources need to be created before the guestbook Pods start. For the analyzer service, an HTTP request is made to a hostname, which allows for resource discovery at the time when the request is made. Consequently, we'll follow a specific order when creating the application components. First, the Redis components will be created, then the guestbook application, and finally the analyzer microservice.

>Note: If you have tried this lab earlier, there might be a possibility that the previous session is still persistent. In such a case, you will see an 'Unchanged' message instead of the 'Created' message when you run the Apply command for creating deployments. We recommend you to proceed with the next steps of the lab.  

1-From the terminal in the lab environment, change to the v2 directory.
```
cd ../../v2
```
![image](https://user-images.githubusercontent.com/100445644/173716227-c4260fff-0b51-4e39-9964-f245503e56d7.png)

2-Run the following command or open the `redis-master-deployment.yaml` in the Explorer to familiarize yourself with the Deployment configuration for the Redis master.  
```
cat redis-master-deployment.yaml
```
![image](https://user-images.githubusercontent.com/100445644/173716375-7c7cb683-3885-415c-997e-1bc9cc1332f8.png)
```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-master
  labels:
    app: redis
    role: master
spec:
  replicas: 1
  selector:
    matchLabels:
      app: redis
      role: master
  template:
    metadata:
      labels:
        app: redis
        role: master
    spec:
      containers:
      - name: redis-master
        image: redis:5.0.5
        ports:
        - name: redis-server
          containerPort: 6379
        volumeMounts:
        - name: redis-storage
          mountPath: /data
      volumes:
      - name: redis-storage
        emptyDir: {}
```
3-Create the Redis master Deployment.
```
oc apply -f redis-master-deployment.yaml
```

![image](https://user-images.githubusercontent.com/100445644/173716606-4fdbd828-fd65-4cb7-871e-4258a936d09e.png)

4-Verify that the Deployment was created.
```
oc get deployments
```
![image](https://user-images.githubusercontent.com/100445644/173716793-25fc1099-850b-43d3-8a7b-4fa0ed5d79a4.png)

5-List Pods to see the Pod created by the Deployment.  
![image](https://user-images.githubusercontent.com/100445644/173716859-da1b9748-dfad-4ac2-86cf-7554d1ff5be2.png)  
You can also return to the Topology view in the OpenShift web console and see that the Deployment has appeared there.

6-Run the following command or open the `redis-master-service.yaml` in the Explorer to familiarize yourself with the Service configuration for the Redis master.
```
cat redis-master-service.yaml
```  
![image](https://user-images.githubusercontent.com/100445644/173717141-0ec6c36c-e971-43df-9193-fed42697087c.png)
  
```yml
apiVersion: v1
kind: Service
metadata:
  name: redis-master
  labels:
    app: redis
    role: master
spec:
  ports:
  - port: 6379
    targetPort: redis-server
  selector:
    app: redis
    role: master
```
Services find the Pods to load balance based on Pod labels. The Pod that you created in previous step has the labels app=redis and role=master. The selector field of the Service determines which Pods will receive the traffic sent to the Service.  

7-Create the Redis master Service.
```
oc apply -f redis-master-service.yaml
```
![image](https://user-images.githubusercontent.com/100445644/173717287-519d48ca-7aa7-480e-8d55-5efc2f8429f7.png)

If you click on the `redis-master` Deployment in the Topology view, you should now see the `redis-master` Service in the Resources tab.
![image](https://user-images.githubusercontent.com/100445644/173717372-1b2b1f7d-ab1c-4eeb-9f25-c53d8e21745d.png)

8-Run the following command or open the `redis-slave-deployment.yaml` in the Explorer to familiarize yourself with the Deployment configuration for the Redis slave.
```
cat redis-slave-deployment.yaml
```

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-slave
  labels:
    app: redis
    role: slave
spec:
  replicas: 2
  selector:
    matchLabels:
      app: redis
      role: slave
  template:
    metadata:
      labels:
        app: redis
        role: slave
    spec:
      containers:
      - name: redis-slave
        image: redis:5.0.5
        command: ["/bin/sh"]
        args: ["-c","redis-server --slaveof redis-master 6379"]
        ports:
        - name: redis-server
          containerPort: 6379
        volumeMounts:
        - name: redis-storage
          mountPath: /data
      volumes:
      - name: redis-storage
        emptyDir: {}
```
![image](https://user-images.githubusercontent.com/100445644/173719241-58942f26-0359-4153-9c30-26cc2be6c541.png)

9-Create the Redis slave Deployment.
```
oc apply -f redis-slave-deployment.yaml
```
![image](https://user-images.githubusercontent.com/100445644/173719321-d19ec757-a570-4283-a5c2-b703c49945f2.png)

10-Verify that the Deployment was created.
```
oc get deployments
```
![image](https://user-images.githubusercontent.com/100445644/173719442-bb68a379-fd05-4bb5-b133-0f726410767f.png)

11-List Pods to see the Pod created by the Deployment.
```
oc get pods
```
![image](https://user-images.githubusercontent.com/100445644/173719527-1ffe8cfc-f4fc-47a6-9b93-984699575917.png)

You can also return to the Topology view in the OpenShift web console and see that the Deployment has appeared there.

12-Run the following command or open the redis-slave-service.yaml in the Explorer to familiarize yourself with the Service configuration for the Redis slave.
```shell
cat redis-slave-service.yaml
```

```yaml
apiVersion: v1
kind: Service
metadata:
  name: redis-slave
  labels:
    app: redis
    role: slave
spec:
  ports:
  - port: 6379
    targetPort: redis-server
  selector:
    app: redis
    role: slave
```
![image](https://user-images.githubusercontent.com/100445644/173719736-9a80f8cc-43ec-49c6-8928-e1266b0454b3.png)

13-Create the Redis slave Service.
```
oc apply -f redis-slave-service.yaml
``
![image](https://user-images.githubusercontent.com/100445644/173719822-470a56f6-f9ea-4e35-8629-619396e9179b.png)

If you click on the `redis-slave` Deployment in the Topology view, you should now see the `redis-slave` Service in the Resources tab.

![image](https://user-images.githubusercontent.com/100445644/173719920-c0da84c8-009e-4f7f-93eb-30f9e209fb4f.png)





## Next Steps
Be sure to take screenshots as per review criteria as you follow the step-by-step instructions.

Author(s)
Lavanya

Changelog
|Date	|Version	|Changed by	|Change Description|
|:-----|:-----|:-----|:------|
|2021-05-01	|0.1	|Lavanya	|Initial version created|
#
© IBM Corporation 2020. All rights reserved.
