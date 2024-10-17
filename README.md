Description of the project:-

This repository hosts the Three-tier application for the TWS community. The challenge involves deploying a Three-Tier Web Application using ReactJS, NodeJS, and MongoDB, with deployment on AWS EKS.

My Github Project link :- https://github.com/cloud-1997/Three-tier-appdeploy-To-do-list
Reference Github link :-  https://github.com/LondheShubham153/TWSThreeTierAppChallenge
Video link :- https://www.youtube.com/watch?v=YlUa3t9Aaic

![image](https://github.com/user-attachments/assets/a334ba62-5f82-4ce1-807c-6a98b05c6d95)



Pre-requisites to start the project:-
	1) AWS-CLI Installed and configured
	2) Docker installed
	3) Eksctl installed
	4) Kubectl installed
	5) Setup EKS Cluster
	6) Helm installed 


## Steps to Execute the Project

### 1. Review the Application Code
The application utilizes React.js for the frontend, Node.js for the backend, and MongoDB as the database. Review the code on GitHub to understand the implementation details. Engage with the developer as needed to gain insights into the architecture and functionality.

### 2. Create Dockerfiles
Develop Dockerfiles for both the backend and frontend components of the application. Note that MongoDB will not require a Dockerfile, as it is publicly available on DockerHub.

### 3. Push Docker Images to AWS ECR
Create a private registry in AWS Elastic Container Registry (ECR) for both the frontend and backend. Follow these steps for each component:
- Log in to the AWS ECR.
- Build the Docker image.
- Tag the image appropriately.
- Push the image to your private ECR repository.

### 4. Set Up an EKS Cluster
Create an Amazon EKS cluster according to the application’s requirements. Ensure that the cluster is properly configured for the application’s deployment.

### 5. Prepare the Kubernetes Namespace and Resources
- Create a separate Kubernetes namespace to deploy the three-tier application.
- Set up Kubernetes secrets to securely store database credentials.
- Create PersistentVolume (PV) and PersistentVolumeClaim (PVC) resources as required for storage.

### 6. Deploy Application Components
Deploy the database, backend, and frontend components using the appropriate YAML configuration files available in the GitHub repository. Define the necessary services to ensure proper communication between components.

### 7. Set Up Ingress and Load Balancer
To expose the application to the internet, follow these steps to configure an Ingress and deploy an Ingress Controller:

1. **Create IAM Policy**:
   ```bash
   curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json
   aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy.json
   ```

2. **Associate IAM OIDC Provider**:
   ```bash
   eksctl utils associate-iam-oidc-provider --region=<cluster-region> --cluster=<cluster-name> --approve
   ```

3. **Create IAM Service Account**:
   ```bash
   eksctl create iamserviceaccount --cluster=<cluster-name> --namespace=kube-system --name=aws-load-balancer-controller --role-name AmazonEKSLoadBalancerControllerRole --attach-policy-arn=arn:aws:iam::<Account-ID>:policy/AWSLoadBalancerControllerIAMPolicy --approve --region=<cluster-region>
   ```

4. **Install Ingress Controller via Helm**:
   ```bash
   sudo snap install helm --classic
   helm repo add eks https://aws.github.io/eks-charts
   helm repo update eks
   helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=<cluster-name> --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller
   ```

5. **Verify Deployment**:
   ```bash
   kubectl get deployment -n kube-system aws-load-balancer-controller
   ```

### 8. Verify Ingress Controller Deployment
To ensure the Ingress Controller is deployed correctly, run:
```bash
kubectl get deployment -n kube-system aws-load-balancer-controller
```

### 9. Deploy Ingress Resource
Create and deploy an `ingress.yaml` file as per your requirements. After deployment, verify the Ingress resource with:
```bash
kubectl get ingress -n <namespace>
```
This command will provide the DNS name/ Address as shown below in the screenshot to register it in your domain needed to access your application on the internet.
![image](https://github.com/user-attachments/assets/23e0b736-071a-4b48-859c-793dada57fcb)



