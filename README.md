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
... you'll likely see an error message saying something like 'eksctl' not found.

<img width="941" height="298" alt="image" src="https://github.com/user-attachments/assets/f27353f5-2867-4e07-95de-d2d1c38a749c" />

# What is eksctl?
eksctl is an official AWS tool for managing Amazon EKS clusters in your terminal. It's much, much easier to use compared to setting up a Kubernetes cluster using the AWS CLI.

2. Install eksctl

 run this command in your ternimal
   
```
   curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv -v /tmp/eksctl /usr/local/bin
```
<img width="1919" height="326" alt="image" src="https://github.com/user-attachments/assets/0e980583-0476-4ee8-8d34-922c02a7f549" />

# What do these commands do?
The commands download and install eksctl on your EC2 instance.

The first commands downloads the latest eksctl release from GitHub, then the second command moves it to a directory within your EC2 instance that lets you run eksctl from anywhere in your terminal.

Check that eksctl is installed correctly by running 'eksctl version'. You should see the version number printed in the terminal.

<img width="1919" height="980" alt="image" src="https://github.com/user-attachments/assets/e4275277-ce89-49cb-a6f9-7d0a3e6176f8" />

# Launch an EKS cluster
# Creating a Kubernetes cluster and using EKS is not AWS Free Tier eligible, so expect to spend $0.10 USD for every hour you leave your EKS cluster running once it's created. Make sure to follow all the deletion instructions at the end of this project to minimise costs for this project.

Now that eksctl is installed, let's try creating our cluster again.

```
eksctl create cluster \
--name nextwork-eks-cluster \
--nodegroup-name nextwork-nodegroup \
--node-type t3.micro \
--nodes 3 \
--nodes-min 1 \
--nodes-max 3 \
--version 1.33 \
--region your-region-code (ap-south-1)
```

<img width="1892" height="123" alt="image" src="https://github.com/user-attachments/assets/56442066-9822-466f-be8b-22c3fb34d837" />

the error this time is that we dont have permission to create EKS cluster, so we solve it by creating IAM role.

# Create an IAM role for your EC2 instance

1. In a new tab, head to your AWS IAM console.
2. Select roles on the left , click on create role.
3. Under Trusted entity type, select AWS service to tell AWS that we're setting up this role for a AWS serice (Amazon EC2).
4. under usecase select EC2.
5. select next.
6. Under Permissions policies, we'll grant our EC2 instance AdministratorAccess.


#  What does this permission policy mean?
AdministratorAccess gives your EC2 instance the permission to access any AWS resource and service in your AWS account.

A super powerful move, which your EC2 instance will need when it deploys and manages your Kubernetes cluster (your instance will be using a lot of different services).

<img width="1917" height="1130" alt="image" src="https://github.com/user-attachments/assets/3285863f-6266-4433-a44c-39cb536cc1a9" />

7. Make sure the AdministratorAccess option is checked, and select Next.
8. Let's give this role a straightforward name - nextwork-eks-instance-role
Enter a short description: Grants an EC2 instance AdministratorAccess to my AWS account. Created during NextWork's Kubernetes project.

9. select create role

   <img width="1910" height="1127" alt="image" src="https://github.com/user-attachments/assets/19a1718f-389f-4901-a3ea-fa94896cfa0e" />

# Attach IAM role to EC2 instance

1.  back to the EC2 console.
2. Select Instances from the left hand sidebar.
3. Select the checkbox next to your nextwork-eks-instance EC2 instance.
4. Select the Actions dropdown, and then Security -> Modify IAM role.

   <img width="1919" height="1104" alt="image" src="https://github.com/user-attachments/assets/58a88f17-2513-4003-8e15-052d2b7210ad" />


5. Under IAM role, select your new nextwork-eks-instance-role role.
6. select update IAM.

# Create your EKS cluster again. (after the updated steps)

1. run this command in the EC2 connect tab

```
eksctl create cluster \
--name nextwork-eks-cluster \
--nodegroup-name nextwork-nodegroup \
--node-type t3.micro \
--nodes 3 \
--nodes-min 1 \
--nodes-max 3 \
--version 1.33 \
--region ap-south-1

```

# Track how AWS creates your EKS cluster

!GitHub Logo

