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
Something like `<h1>Alex's Guestbook - v1</h1>`. Make sure to save the file when you're done.  
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
