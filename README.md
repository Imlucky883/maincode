**_Project Source_** : https://github.com/saha-rajdeep/kubernetescode

<h1>CI CD Pipeline using Jenkins and GitOps(ArgoCD) to dockerize Python application and deploy the container into a Kubernetes cluster.</h1>
This project demonstrates how to set up a CI/CD pipeline using Jenkins and GitOps (ArgoCD) to dockerize your code, and deploy the container into a Kubernetes cluster. The Jenkins server is used to automate the build and test phases of the CI/CD pipeline, while ArgoCD is used to deploy the containerized application to the Kubernetes cluster. This allows for efficient and automated delivery of code changes, ensuring that the application is always up-to-date and running smoothly.

<h2>WorkFlow(Architecture)</h2>

![Screenshot (57)](https://user-images.githubusercontent.com/88871793/210801745-312e6b79-691e-4065-86fd-281effd030ee.png)

<h2>Prerequisites</h2>

* AWS Account
* DockerHub Account
* Chocolatey( if you are on Windows )

<h2>Setup</h2>

1. Take a t2 medium instance with Amazon Linux 2 image to setup the Jenkins server
2. SSH into the Jenkins server
3. Install Jenkins 
```Bash
# Update the package manager and install Java 11
sudo yum update -y
sudo amazon-linux-extras install java-openjdk11 -y

# Add the Jenkins repository to the system
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key

# Install Jenkins
sudo yum install -y jenkins

# Start the Jenkins service and enable it to start on boot
sudo systemctl start jenkins
sudo systemctl enable jenkins
```
4. Access Jenkins on `https://<public_ip_of_instance>:8080`
5. Install Docker
```Bash
#Update the package manager
sudo yum update -y

# Install Docker
sudo yum install -y docker

# Start the Docker daemon and enable it to start on boot
sudo systemctl start docker
sudo systemctl enable docker

# Add the current user to the docker group
sudo usermod -aG docker $USER
```
6. Install the **below Jenkins Plugins**  from `Manage jenkins > Plugins > Available Plugins`
* Docker
* Docker Pipeline
* GitHub Integration
7. Add Github and DockerHub credentials which will be used for the authentication purpose.
![Screenshot (59)](https://user-images.githubusercontent.com/88871793/210787897-9c7233ec-a740-4138-aae5-ca982bb8f1f1.png)

8. Setup a Github Webhook which will trigger the Job whenever any updates are made to the repository.
  * Copy the Jenkins URL
  * In the current repo, Go to `Settings > Webhooks > Create a webhook` 
  * Paste the URL there suffixing _/github-webhook/_
  * For example : `https://56.34.123.51:8080/github-webhook/`

<h2> Setting Up Kubernetes CLuster on AWS </h2>
<p>
There are 3 ways to create a EKS cluster
</p>
1. Using AWS Management Console
2. Using eksctl
3. Using IaC tool like Terraform
<p> We will be using `eksctl` to provision our cluster. It is the easiest way to setup a cluster with just one command. </p>

>  **NOTE** : You need to have **kubectl** installed on your Local system. It may take around 20 minutes for the cluster to setup.

```Bash
#Installing eksctl
choco install -y eksctl
```

<h2> Installing ArgoCD </h2>

```YAML
kubectl create namespace argocd
kubectl apply -n argocd -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml
kubectl port-forward svc\/argocd-server -n argocd 8080:443
```

The API server can then be accessed using the `localhost:8080`

> **NOTE** : In the last stage of Jenkinsfile, the build job name should be same as that of the second job which will be triggered once the image is pushed to DockerHub. This repo is associated with https://github.com/Imlucky886/manifestcode which has the Jenkinsfile for Jenkins Job 2.
<h2> Output </h2>

![Screenshot (60)](https://user-images.githubusercontent.com/88871793/210800293-41e50057-cf78-4db4-8f36-409a42e4b9e0.png)

![Screenshot (62)](https://user-images.githubusercontent.com/88871793/210800349-6f324ac6-9c67-4c7b-a804-6400d44cc2d1.png)


![Screenshot (64)](https://user-images.githubusercontent.com/88871793/210799731-4cb01ae7-a226-4e90-94e4-17c8c05879a4.png)

<h1> Credits </h1>
https://youtu.be/o4QG_kqYvHk
