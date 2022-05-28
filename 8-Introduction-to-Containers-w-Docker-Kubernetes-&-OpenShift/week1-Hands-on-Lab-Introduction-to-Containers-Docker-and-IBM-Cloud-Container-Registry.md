**Hands-on Lab:**  
**Introduction to Containers, Docker and IBM Cloud Container Registry**   
This lab will demonstrate how to pull an image from Docker Hub and run an image as a container using docker, and build an image using a Dockerfile and push an image to IBM Cloud Container Registry.  
[the git repository that contains the artifacts needed for this lab](https://github.com/ibm-developer-skills-network/CC201)  
**Objectives**  
In this lab, you will:  

* Pull an image from Docker Hub
* Run an image as a container using docker
* Build an image using a Dockerfile
* Push an image to IBM Cloud Container Registry
>Note: Kindly complete the lab in a single session without any break because the lab may go on offline mode and may cause errors. If you face any issues/errors during the lab process, please logout from the lab environment. Then clear your system cache and cookies and try to complete the lab.

**Important**: You may already have an IBM Cloud account and may even have a namespace in the IBM Container Registry (ICR). However, in this lab you will not be using your own IBM Cloud account or your own ICR namespace. You will be using an IBM Cloud account that has been automatically generated for you for this excercise. The lab environment will not have access to any resources within your personal IBM Cloud account, including ICR namespaces and images.
# 
## Verify the environment and command line tools  
1-Open a terminal window by using the menu in the editor: `Terminal > New Terminal`.
>Note:If the terminal is already opened, please skip this step.  

![image](https://user-images.githubusercontent.com/100445644/170591534-1dadb9f4-e49c-492f-98c1-709426e915c0.png)

2-Verify that `docker` CLI is installed.
```
docker --version
```
You should see the following output, although the version may be different:  
![image](https://user-images.githubusercontent.com/100445644/170591690-f2de74e2-b166-401d-aec3-8730bd52202c.png)

3-Verify that `ibmcloud` CLI is installed.
```
ibmcloud version
```
You should see the following output, although the version may be different:
![image](https://user-images.githubusercontent.com/100445644/170592355-e35c9c8b-8aef-482f-af10-37ae5319965b.png)

3-Change to your project folder.
>Note: If you are already on the '/home/project' folder, please skip this s

```
cd /home/project
```
5-Clone the git repository that contains the artifacts needed for this lab, if it doesn't already exist.
```
[ ! -d 'CC201' ] && git clone https://github.com/ibm-developer-skills-network/CC201.git
```
![image](https://user-images.githubusercontent.com/100445644/170592455-8884f4fe-26f2-48a8-830e-04ef0d055f55.png)

6-Change to the directory for this lab.
```
cd CC201/labs/1_ContainersAndDocker/
```
7-List the contents of this directory to see the artifacts for this lab.
```
ls
```
![image](https://user-images.githubusercontent.com/100445644/170592547-aaad3390-f15c-47f2-992c-c5e78b171f84.png)

## Pull an image from Docker Hub and run it as a container
1-Use the docker CLI to list your images.
```
docker images
```
You should see an empty table (with only headings) since you don't have any images yet.  

![image](https://user-images.githubusercontent.com/100445644/170592850-64d61705-5cbd-4665-9f7f-e5fe2ad197ad.png)


2-Pull your first image from Docker Hub.
```
docker pull hello-world
```  
![image](https://user-images.githubusercontent.com/100445644/170592913-a3ad71da-4fa3-42c1-9b83-16f51042d49b.png)


3-List images again.
```
docker images
```

You should now see the hello-world image present in the table.  
![image](https://user-images.githubusercontent.com/100445644/170592948-9cd06767-7776-4ec0-ba31-24cbc67faf04.png)



4-Run the hello-world image as a container.
```
docker run hello-world
```
You should see a 'Hello from Docker!' message.
There will also be an explanation of what Docker did to generate this message.


![image](https://user-images.githubusercontent.com/100445644/170593709-8ca4bd69-4da7-4ed2-a4b0-9c61f95da1eb.png)

5-List the containers to see that your container ran and exited successfully.
```
docker ps -a
```
Among other things, for this container you should see a container ID, the image name (hello-world), and a status that indicates that the container exited successfully.
![image](https://user-images.githubusercontent.com/100445644/170593516-c906be9c-8d1f-4f99-888d-feb7b658ce2d.png)



6-Note the CONTAINER ID from the previous output and replace the \ in the command below with this value. This command removes your container.
```
docker container rm <container_id>
```

7-Verify that that the container has been removed. Run the following command.
```
docker ps -a
```
![image](https://user-images.githubusercontent.com/100445644/170593386-e6f30310-bd91-459f-bf48-efe98afb503b.png)

Congratulations on pulling an image from Docker Hub and running your first container! Now let's try and build our own image.


## Build an image using a Dockerfile
The current working directory contains a simple Node.js application that we will run in a container. The app will print a hello message along with the hostname. The following files are needed to run the app in a container:
app.js is the main application, which simply replies with a hello world message.
package.json defines the dependencies of the application.
Dockerfile defines the instructions Docker uses to build the image.

![image](https://user-images.githubusercontent.com/100445644/170593976-bb1201fc-4240-47f9-95b6-64a2a9e25226.png)

Use the Explorer to view the files needed for this app. Click the Explorer icon (it looks like a sheet of paper) on the left side of the window, and then navigate to the directory for this lab: CC201 > labs > 1_ContainersAndDocker. Click Dockerfile to view the commands required to build an image.
Dockerfile in Explorer

You can refresh your understanding of the commands mentioned in the Dockerfile below:  
The FROM instruction initializes a new build stage and specifies the base image that subsequent instructions will build upon.
The COPY command enables us to copy files to our image.
The RUN instruction executes commands.
The EXPOSE instruction exposes a particular port with a specified protocol inside a Docker Container.
The CMD instruction provides a default for executing a container, or in other words, an executable that should run in your container.

Run the following command to build the image:  
```
docker build . -t myimage:v1
```
As seen in the module videos, the output creates a new layer for each instruction in the Dockerfile.

![image](https://user-images.githubusercontent.com/100445644/170594066-70e732ad-d7b5-46de-8764-e85e35a194f5.png)


List images to see your image tagged myimage:v1 in the table.
```
docker images
```
Note that compared to the hello-world image, this image has a different image ID. This means that the two images consist of different layers -- in other words, they're not the same image.

You should also see a node image in the images output. This is because the docker build command pulled node:9.4.0-alpine to use it as the base image for the image you built.

![image](https://user-images.githubusercontent.com/100445644/170594126-18f09de8-6f1f-4d3e-ac37-e8f5347e8a41.png)


## Run the image as a container
Now that your image is built, run it as a container with the following command:
```
docker run -p 8080:8080 myimage:v1
```
The output should indicate that your application is listening on port 8080. This command will continue running until it exits, since the container runs a web app that continually listens for requests. To query the app, we need to open another terminal window.

![image](https://user-images.githubusercontent.com/100445644/170594247-e95fb2b3-7ae6-4066-9f2e-ad3c04b5ab1b.png)


To split the terminal, click Terminal > Split Terminal.  
![image](https://user-images.githubusercontent.com/100445644/170594260-e8cbb444-1b15-40c9-a128-d02c7d1e84cc.png)


In the second terminal window, use the curl command to ping the application.
```
curl localhost:8080
```
![image](https://user-images.githubusercontent.com/100445644/170594313-8526efee-b68c-4bb6-8112-7281c38c3437.png)

The output should indicate that 'Your app is up and running!'.



In the second terminal window, stop the container. The following command uses docker ps -q to pass in the list of all running containers:
```
docker stop $(docker ps -q)
```
![image](https://user-images.githubusercontent.com/100445644/170594368-d8037a19-ed9b-48ae-a74d-e2165ca48278.png)

In the second terminal window, check if the container has stopped by running the following command.
```
docker ps
```
![image](https://user-images.githubusercontent.com/100445644/170594443-9628d07a-d41a-44eb-8f68-9813e5acc361.png)


Close the second terminal window, as it is no longer needed.
```
exit
```
![image](https://user-images.githubusercontent.com/100445644/170594466-5ee625c5-c893-452d-b5db-0f1240fbdf9d.png)

In the original terminal window, the docker run command has exited and you are able to type commands in that terminal window again.

Note: If you face any issues in typing further commands in the terminal, press Enter.

![image](https://user-images.githubusercontent.com/100445644/170594492-18f19446-8d89-422d-8a84-863dd6fc55bf.png)


## Push the image to IBM Cloud Container Registry
The environment should have already logged you into the IBM Cloud account that has been automatically generated for you by the Skills Network Labs environment. The following command will give you information about the account you're targeting:
```
ibmcloud target
```
![image](https://user-images.githubusercontent.com/100445644/170594607-232115fc-fa28-419f-ab87-118f8c1971b9.png)


The environment also created an IBM Cloud Container Registry (ICR) namespace for you. Since Container Registry is multi-tenant, namespaces are used to divide the registry among several users. Use the following command to see the namespaces you have access to:  
```
ibmcloud cr namespaces
```
You should see two namespaces listed starting with sn-labs:

The first one with your username is a namespace just for you. You have full read and write access to this namespace.
The second namespace, which is a shared namespace, provides you with only Read Access
![image](https://user-images.githubusercontent.com/100445644/170594684-acffa22c-5142-4177-9958-f77dfb3ed06a.png)


Ensure that you are targeting the region appropriate to your cloud account, for instance us-south region where these namespaces reside as you saw in the output of the ibmcloud target command.

```
ibmcloud cr region-set us-south
```
![image](https://user-images.githubusercontent.com/100445644/170594733-b0e76b12-1b62-4c49-afd6-58d38c148598.png)


Log your local Docker daemon into IBM Cloud Container Registry so that you can push to and pull from the registry.  
```
ibmcloud cr login
```
![image](https://user-images.githubusercontent.com/100445644/170594763-c88cb055-361b-4004-b348-bd26d69fece7.png)


Export your namespace as an environment variable so that it can be used in subsequent commands.
```
export MY_NAMESPACE=sn-labs-$USERNAME
```
![image](https://user-images.githubusercontent.com/100445644/170594784-d7942084-9242-4467-b700-0940fc1be575.png)


Tag your image so that it can be pushed to IBM Cloud Container Registry.
```
docker tag myimage:v1 us.icr.io/$MY_NAMESPACE/hello-world:1
```
![image](https://user-images.githubusercontent.com/100445644/170594827-188db2be-da4f-4244-af75-79138da6c8c2.png)


Push the newly tagged image to IBM Cloud Container Registry.
```
docker push us.icr.io/$MY_NAMESPACE/hello-world:1
```
![image](https://user-images.githubusercontent.com/100445644/170594872-adef34ac-8eaa-4219-bf05-db304f593f61.png)


Note: If you have tried this lab earlier, there might be a possibility that the previous session is still persistent. In such a case, you will see a 'Layer already Exists' message instead of the 'Pushed' message in the above output. We recommend you to proceed with the next steps of the lab.

Verify that the image was successfully pushed by listing images in Container Registry.
```
ibmcloud cr images
```
![image](https://user-images.githubusercontent.com/100445644/170594910-7f14bda6-7dc0-4e91-8305-380d1751c711.png)


Optionally, to only view images within a specific namespace.
```
ibmcloud cr images --restrict $MY_NAMESPACE
```
You should see your image name in the output. Recall from the module videos that we discussed Vulnerability Advisor, which scans images in IBM Cloud Container Registry for common vulnerabilities and exposures. In the last column of the output, note that Vulnerability Advisor is either scanning your image or it has provided a security status, depending on how quickly you list the images and how long the scan takes.

![image](https://user-images.githubusercontent.com/100445644/170594954-054d741f-8a2d-42a3-9a62-8e6d98fc4322.png)


Congratulations! You have completed the second lab for the first module of this course.

Note: Please delete your project from SN labs environment before signing out to ensure that further labs run correctly. To do the same, click on this link

**Changelog**
|Date	|Version	|Changed by	|Change Description|
|:-----|:-----|:-----|:-----|
|2022-04-08	|1.1	|K Sundararajan	|Updated Lab instructions|
|2022-04-19	|1.2	|K Sundararajan	|Updated Lab instructions|
# 

© IBM Corporation 2022. All rights reserved.




