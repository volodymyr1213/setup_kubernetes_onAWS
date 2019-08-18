# setup_kubernetes_onAWS

Setup Kubernetes (K8s) Cluster on AWS
Create Ubuntu EC2 instance

install AWSCLI

 curl https://s3.amazonaws.com/aws-cli/awscli-bundle.zip -o awscli-bundle.zip
 apt install unzip python
 unzip awscli-bundle.zip
 #sudo apt-get install unzip - if you dont have unzip in your system
 ./awscli-bundle/install -i /usr/local/aws -b /usr/local/bin/aws
Install kubectl

curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
 chmod +x ./kubectl
 sudo mv ./kubectl /usr/local/bin/kubectl
Create an IAM user/role with Route53, EC2, IAM and S3 full access

Attach IAM role to ubuntu server

Note: If you create IAM user with programmatic access then provide Access keys.
  aws configure
Install kops on ubuntu instance:

 curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
 chmod +x kops-linux-amd64
 sudo mv kops-linux-amd64 /usr/local/bin/kops
Create a Route53 private hosted zone (you can create Public hosted zone if you have a domain)

create an S3 bucket

 aws s3 mb s3://dev.k8s.valaxy.in
Expose environment variable:

 export KOPS_STATE_STORE=s3://dev.k8s.valaxy.in
Create sshkeys before creating cluster

 ssh-keygen
Create kubernetes cluster definitions on S3 bucket

 kops create cluster --cloud=aws --zones=ap-southeast-1b --name=dev.k8s.valaxy.in --dns-zone=valaxy.in --dns private
Create kubernetes cluser

  kops update cluster dev.k8s.valaxy.in --yes
Validate your cluster

 kops validate cluster
To list nodes

  kubectl get nodes 
Deploying Nginx container on Kubernetes
Deploying Nginx Container

  kubectl run sample-nginx --image=nginx --replicas=2 --port=80
  kubectl get pods
  kubectl get deployments
Expose the deployment as service. This will create an ELB in front of those 2 containers and allow us to publicly access them:

 kubectl expose deployment sample-nginx --port=80 --type=LoadBalancer
 kubectl get services -o wide
To delete cluster

 kops delete cluster dev.k8s.valaxy.in --yes
