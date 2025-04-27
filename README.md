# containerization-web-application
Step 1: EC2 Setup
Launch an Ubuntu instance 
![Screenshot 2025-04-25 132823](https://github.com/user-attachments/assets/bd918dd2-59a2-4662-8aed-fd15a24a0c32)

Step2: DockerFile for frontend
![Screenshot 2025-04-25 134459](https://github.com/user-attachments/assets/71da86a0-75c2-4b93-bdfa-ce97cb7fe924)

Step 3:Install Docker
sudo apt-get update
sudo apt install docker.io
docker ps
sudo chown $USER /var/run/docker.sock
![Screenshot 2025-04-25 134659](https://github.com/user-attachments/assets/5c4d2614-80ae-4ac2-b85c-70b2e07ebbf2)
![Screenshot 2025-04-25 135044](https://github.com/user-attachments/assets/ab02ea32-4fee-4110-9bf9-7cd076546c09)
![Screenshot 2025-04-25 135251](https://github.com/user-attachments/assets/06ae2b5f-9287-4862-ba17-4b223378ef81)

Step 4: Edit inbound rule for frontend 
![Screenshot 2025-04-25 135847](https://github.com/user-attachments/assets/28e8878a-9ca4-451f-a7e5-537b07b920d1)

![Screenshot 2025-04-25 135544](https://github.com/user-attachments/assets/82661f2d-f318-4da5-8ab1-f662b1ee65d8)

![Screenshot 2025-04-25 135631](https://github.com/user-attachments/assets/eeaf3c18-2a04-4af7-b9d0-06ba7bc4acf3)

Step 5: Configure IAM
Create a user eks-admin with AdministratorAccess.
Generate Security Credentials: Access Key and Secret Access Key.

![Screenshot 2025-04-25 140254](https://github.com/user-attachments/assets/93c4c9ff-c4aa-4402-adcd-32db43126ba6)
![Screenshot 2025-04-25 140606](https://github.com/user-attachments/assets/e4a36a5a-6a9b-4e72-93e0-62cdde0a30f7)

Step 6: Create ECR 

![Screenshot 2025-04-25 140654](https://github.com/user-attachments/assets/a89ff2b8-0d3f-4d97-9e47-53a70864f676)
Follow these to push image

![Screenshot 2025-04-25 140726](https://github.com/user-attachments/assets/995fc18b-529b-430a-9a6a-44ddf4facef4)

![Screenshot 2025-04-25 141146](https://github.com/user-attachments/assets/4c1d5a82-83e1-4e4a-b430-f21928a355fc)

![Screenshot 2025-04-25 141519](https://github.com/user-attachments/assets/40fab7aa-b0af-451d-85f8-5b47cd0da90a)

Create DockerFile for Backend

![Screenshot 2025-04-25 141654](https://github.com/user-attachments/assets/86985112-5e51-425d-9607-c58a0821e553)

Step 7: Push this image on ECR

![Screenshot 2025-04-25 141933](https://github.com/user-attachments/assets/3e2cf2a3-877f-40a2-b968-f6ec4260d297)

![Screenshot 2025-04-25 142252](https://github.com/user-attachments/assets/ae958091-74ab-4973-9f4c-23ee9c47e7dd)

Step 8: Install Kubectl
curl -o kubectl https://amazon-eks.s3.ap-south-1.amazonaws.com/1.19.6/2021-01-05/bin/linux/amd64/kubectl
chmod +x ./kubectl
sudo mv ./kubectl /usr/local/bin
kubectl version --short --client


Step 9: Install ekstl
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp
sudo mv /tmp/eksctl /usr/local/bin
eksctl version

![Screenshot 2025-04-25 143055](https://github.com/user-attachments/assets/0c88ff9e-4f15-4952-a9ab-4f73444239b8)

Step 10: Setup EKS Cluster

eksctl create cluster --name three-tier-cluster --region ap-south-1 --node-type t2.medium --nodes-min 2 --nodes-max 2
aws eks update-kubeconfig --region ap-south-1 --name three-tier-cluster
kubectl get nodes

![Screenshot 2025-04-25 145009](https://github.com/user-attachments/assets/9e5f8c94-8276-4af7-a847-154ed3dd7802)

Step 11: Install AWS CLI v2
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
sudo apt install unzip
unzip awscliv2.zip
sudo ./aws/install -i /usr/local/aws-cli -b /usr/local/bin --update
aws configure

![Screenshot 2025-04-25 155933](https://github.com/user-attachments/assets/55b8f5fc-0d66-4629-8e8d-bfbf275bfe8b)

Step 11: Setup Database MongoDB
![Screenshot 2025-04-25 155933](https://github.com/user-attachments/assets/7673e6fc-3636-414f-a8a5-0ee275e6d49f)

![Screenshot 2025-04-25 160514](https:![Screenshot 2025-04-25 162335](https://github.com/user-attachments/assets/12a62840-fb0d-4521-ac5e-c136b26a7e46)
//github.com/user-attachments/assets/c760b690-20ba-4d3f-ac78-eae0628036e7)

![Screenshot 2025-04-25 170835](https://github.com/user-attachments/assets/9abc14cf-a768-4412-9705-8e909f56d9a1)


 Step 12: Install AWS Load Balancer

curl -O https://raw.githubusercontent.com/kubernetes-sigs/aws-load-balancer-controller/v2.5.4/docs/install/iam_policy.json
aws iam create-policy --policy-name AWSLoadBalancerControllerIAMPolicy --policy-document file://iam_policy.json
eksctl utils associate-iam-oidc-provider --region=ap-south-1 --cluster=three-tier-cluster --approve
eksctl create iamserviceaccount --cluster=three-tier-cluster --namespace=kube-system --name=aws-load-balancer-controller --role-name AmazonEKSLoadBalancerControllerRole --attach-policy-arn=arn:aws:iam::339712834098:policy/AWSLoadBalancerControllerIAMPolicy --approve --region=ap-south-1

![Screenshot 2025-04-25 234021](https://github.com/user-attachments/assets/12c3c83e-311e-482b-b02b-f77bbef58c23)

Step 13: Deploy AWS Load Balancer Controller
sudo snap install helm --classic
helm repo add eks https://aws.github.io/eks-charts
helm repo update eks
helm install aws-load-balancer-controller eks/aws-load-balancer-controller -n kube-system --set clusterName=my-cluster --set serviceAccount.create=false --set serviceAccount.name=aws-load-balancer-controller
kubectl get deployment -n kube-system aws-load-balancer-controller
kubectl apply -f ingress.yaml
![Screenshot 2025-04-25 235618](https://github.com/user-attachments/assets/635749ea-43a8-4fd2-9e69-0437d23dddb5)

![Screenshot 2025-04-26 000830](https://github.com/user-attachments/assets/edfa87f1-5184-4cbf-8dfd-a6ea7fb2e0ef)

Step 14: Setup Prometheus 

![Screenshot 2025-04-26 003313](https://github.com/user-attachments/assets/764f9410-85c7-4fa5-8ead-50078b5c37d6)

![Screenshot 2025-04-26 003549](https://github.com/user-attachments/assets/5a7f80d6-d801-4d13-825f-a836885d10a9)

