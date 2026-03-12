# Launch-a-Kubernetes-Cluster


<img width="1729" height="933" alt="image" src="https://github.com/user-attachments/assets/6dd1dc41-4263-44e1-a442-e6b678ac152d" />


# Overview 

<img width="1570" height="845" alt="image" src="https://github.com/user-attachments/assets/95917fda-bcc4-4201-8772-3e6ba16034b6" />


1. create a EC2 instance. Once its running and set up connect to it using EC2 Instance Connect.

 # Why are we using EC2 Instance Connect?
EC2 Instance Connect is a shortcut way to get direct SSH access to your EC2 instance. Instead of having to manage a key pair manually, Instance Connect connects you to your instance within your AWS Management Console. 
To connect to an EC2 instance via SSH (which you can learn how to do in Set Up a Web App in the Cloud), you usually need to:

1. Generate a key pair.
2. Associate the public key with your EC2 instance.
3. Securely store your private key on your local machine.
4. Set up an SSH client (a software that can handle the SSH protocol, like Terminal on Max/Linux or VS Code).
5. Provide your private key and run ssh commands to set up an SSH connection to your EC2 instance
EC2 Instance Connect lets us skip all those steps and connect to your EC2 instances directly using the AWS Management Console




 # To connect to an EC2 instance via SSHz



2. Select the instance you just launced, click connect.



4. It redirects you to the instance connection page! This is where AWS gives you different options to connect with your EC2 instance.
   this wil open a terminal directly in your browser in a new tab.

 <img width="1919" height="1136" alt="image" src="https://github.com/user-attachments/assets/cf2165d0-8680-4997-ab28-acb442437894" />


# Launch an EKS cluster (and get an error)

Now for the main event,creating our Kubernetes cluster with Amazon EKS!

We'll use a handy command-line tool called eksctl to create a cluster within your EC2 instance's termninal.

# basic bg 
# What is Kubernetes?
Kubernetes is a tool that helps you manage your running containers.

Once you've created containers (e.g. using Docker), Kubernetes helps keep them running smoothly by automatically handling tasks like load balancing, scaling, and restarting containers if they fail. It doesn’t build the containers (that’s Docker’s job) but it’s great for keeping them running without you having to manage it manually.

#  What is Amazon EKS?
While Kubernetes makes it easier to work with containers, Amazon EKS makes it easier to work with Kubernetes itself!

Setting up Kubernetes from scratch can be quite a time consuming and complex thing to do, because you'd need to configure Kubernetes' networking, scaling, and security settings on your own. Amazon EKS handles all of these set up tasks for you and helps you integrate Kubernetes with other AWS services.

eksctl is a tool specifically for working with EKS in the command line

<img width="1691" height="958" alt="image" src="https://github.com/user-attachments/assets/47f64c34-d8f9-45ad-83fb-812c783de9ff" />


1. to create a cluster.
In your EC2 instance connect window, run the below command.
```
eksctl create cluster \
--name nextwork-eks-cluster \
--nodegroup-name nextwork-nodegroup \
--node-type t3.micro \
--nodes 3 \
--nodes-min 1 \
--nodes-max 3 \
--version 1.33
```
