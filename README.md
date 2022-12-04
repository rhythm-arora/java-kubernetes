# java-kubernetes
This project walks you through the deployment of a Java application to K8s.

# Application

I have used a simple [Java application](https://tomcat.apache.org/tomcat-8.0-doc/appdev/sample/) running on Tomcat 8.

# Installation

### Docker Desktop

Install [Docker Desktop](https://www.docker.com/products/docker-desktop) on your Windows or MAC machine.

### Enable Kubernetes

To enable Kubernetes support on Docker Desktop and install a standalone instance of Kubernetes running as a Docker container, go to Preferences > Kubernetes and then click Enable Kubernetes. This instantiates images required to run the Kubernetes server as containers, and installs the /usr/local/bin/kubectl command on your machine.

You can test this by listing the available nodes:

```
$ kubectl get nodes

NAME             STATUS   ROLES    AGE   VERSION
docker-desktop   Ready    master   1d    v1.21.5
```

### Create Docker Hub Account

You can create an account in DockerHub where you can publish your docker image. Go to following [link](https://hub.docker.com/) to create an account.

# Dockerizing Java Application

You have to dockerize the application in order to run on K8s. You can achieve this with the below steps:

Open cli of your choice and execute the following commands.

- Login to Docker Hub:
    ```
    $ docker login
    ```
    You need to Authenticate first time with your credentials. For next time, it will authenticate using your existing credentials.

- Build and Tag an Image
    ```
    $ docker build -t {tag_name} .
    $ docker tag {tag_name}:{version} <username/repo>:{image_name}
    ```

- Push Image to Docker Hub
    ```
    $ docker image push <username/repo>:{image_name}
    ```
    
# Deploying Application to Kubernetes Cluster

- **Create Namespace (optional)**

    You can create a namespace where all your resources will be created. If not, all the resources will be created in default namespace. You can give any name to your namespace.

- **Application Deployment**
    ```
    $ kubectl apply -f deployment.yaml
    ```
    This will create the pods where the Java application will be running.

- **Create Service**
    ```
    $ kubectl apply -f service.yaml
    ```
    This will enable network access to our pods.
   
You can check the resources created using the following commands:
```
# Deployment
$ kubectl get pods
NAME                                     READY   STATUS    RESTARTS   AGE
sample-app-deployment-1xxxxxxx-xxxxxx   1/1     Running   0          6h41m
sample-app-deployment-2xxxxxxx-xxxxx   1/1     Running   0          5h53m

***************************************************************************

# Service
$ kubectl get svc
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)    AGE
sample-app-svc   ClusterIP   10.96.153.61   <none>        3000/TCP   8h
```

Now, if you want to access and interact with our application from localhost, you can use port-forward:
```
$ kubectl port-forward service/sample-app-svc 8080:3000
Forwarding from 127.0.0.1:8080 -> 8080
Forwarding from [::1]:8080 -> 8080
```
Run the following curl to get the response,
```
$ curl -L http://localhost:8080/sample/
```

However, this is not a standard approach to allow access to your application to other users. So, I have implemented an Ingress to expose our service more securely.
