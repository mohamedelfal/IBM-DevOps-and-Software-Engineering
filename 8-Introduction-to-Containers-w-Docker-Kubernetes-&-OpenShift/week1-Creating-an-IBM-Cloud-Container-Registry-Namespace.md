# Creating an IBM Cloud Container Registry Namespace
**Objectives**  
After completing this lab, you will be able to:  

Describe the IBM Cloud Container Registry service  
Create a Container Registry namespace  
**Lab Overview**  
In this lab you will create an IBM Cloud Container Registry namespace, which you will use in a subsequent labs.

**Pre-requisites**  
You will need an IBM Cloud account to do this lab. If you have not created one already, click on this link and follow the instructions to create an IBM Cloud account.

**About IBM Cloud**  
The IBM Cloud platform is deployed across data centers around the world.  
It combines platform as a service (PaaS) with infrastructure as a service (IaaS) to provide an integrated experience.  
The platform scales and supports both large enterprise businesses and small development teams and organizations.

The platform is built to support your needs, whether it's working only in the public cloud or taking advantage of a multicloud deployment model.  
IBM Cloud offers a variety of services, including Compute, Network, Storage, Management, Security, Databases, Analytics, AI, and Cloud Paks.

**About IBM Cloud Container Registry namespaces**   
IBM Cloud® Container Registry provides a multi-tenant, encrypted private image registry that you can use to store and access your container images in a highly available and scalable architecture.   
The namespace is a slice of the registry to which you can push your images.   
The namespace will be a part of the image name when you tag and push an image. For example, `us.icr.io/<my_namespace>/<my_repo>:<my_tag>`.

**Create a Container Registry namespace**  
1-Go to the [IBM Cloud catalog](https://cloud.ibm.com/catalog) page.

2-In the Catalog search box, type `Container Registry`.
![image](https://user-images.githubusercontent.com/100445644/170587688-5bc12423-cdc6-4120-bf82-1ab0ed570359.png)


3-Click on Container Registry in the search results.   
![image](https://user-images.githubusercontent.com/100445644/170588585-08d0818e-6651-4a46-ae0b-da60d4e0fe0d.png)

4-You can now read about the Container Registry service and visit links for API documentation and docs about how to use the service.  
![Registry catalog](https://user-images.githubusercontent.com/100445644/170587859-0a2312a7-0c0b-4cea-b8b6-caa0c452ba0e.png)


5-At the top right, click Get started.  
![image](https://user-images.githubusercontent.com/100445644/170587960-43e4e040-7fc5-42c9-b0e7-323c2f8ceb76.png)

6-Ensure that the location is set to Dallas.  
![Container Registry location](https://user-images.githubusercontent.com/100445644/170587999-cf8a4374-05d5-448a-8fe4-387810c781ea.png)


7-On the left hand side panel, click the Namespaces tab.  
![Container Registry namespaces menu](https://user-images.githubusercontent.com/100445644/170588102-d57a6f4c-b213-4a71-8ba0-5128d40f4d76.png)


8-On the right side of the Namespaces panel, click Create.  
![image](https://user-images.githubusercontent.com/100445644/170588124-1d205370-31ca-4307-9216-d51f32b9887e.png)

9-A Create namespace panel opens.  
![image](https://user-images.githubusercontent.com/100445644/170588172-ff503f60-ba95-4dfd-a80c-86f9beeeb66a.png)

10-In the Resource group field, select the name of the resource group you would like this namespace to reside in. For this lab, you can simply leave the selection as Default.  
![image](https://user-images.githubusercontent.com/100445644/170588203-b8b526f8-b790-4085-89b1-9da7af27693b.png)

11-In the Name field, type a unique name for the namespace. The name must be unique across all users of the Container Registry service in this region.  
![image](https://user-images.githubusercontent.com/100445644/170588236-d1587afc-30b3-44d2-8d43-c9aaffffc422.png)

12-Click Create at the bottom of the panel to create the namespace.  
![image](https://user-images.githubusercontent.com/100445644/170588273-e024116a-3da9-41c0-be6d-569f6d52a9f1.png)

You now have a namespace (as below) to which you can push images.  
![image](https://user-images.githubusercontent.com/100445644/170588299-caeededa-9c53-4285-8593-df64552b849b.png)


Congratulations! You have completed the first lab for the first module of this course.  

**Changelog**  
|Date	|Version	|Changed by	|Change Description|
|:-----|:-----|:-----|:-----|
|2022-04-08	|1.1	|K Sundararajan	|Updated Lab instructions|  
# 
© IBM Corporation 2022. All rights reserved.
