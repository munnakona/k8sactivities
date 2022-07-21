EKS Installation:
-----------------------

Step1: Create EC2 instance inoreder to Login the EKS cluster
--------------------------------------------------------------

STEP2: install aws cli
-------------------------
sudo apt update


sudo apt install unzip -y

curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
unzip awscliv2.zip
sudo ./aws/install


STEP:3 Create IAM user
-----------------------------

create IAM user and give programetic access

save access key and secret keys


aws configure

Step:4 copy this and enter
---------------------------

cat >eks-cluster-role-trust-policy.json <<EOF
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "eks.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
EOF

Step:5 copy this and enter
-------------------------

ls


aws iam create-role --role-name AmazonEKSClusterRole --assume-role-policy-document file://"eks-cluster-role-trust-policy.json"



aws iam attach-role-policy --policy-arn arn:aws:iam::aws:policy/AmazonEKSClusterPolicy --role-name AmazonEKSClusterRole




STEP:6 INSTALLING EKSCTL
--------------------------------
curl --silent --location "https://github.com/weaveworks/eksctl/releases/latest/download/eksctl_$(uname -s)_amd64.tar.gz" | tar xz -C /tmp


sudo mv /tmp/eksctl /usr/local/bin

eksctl version


STEP7: INSTALLING KUBECTL
-------------------------------

curl -o kubectl https://s3.us-west-2.amazonaws.com/amazon-eks/1.22.6/2022-03-09/bin/linux/amd64/kubectl

chmod +x ./kubectl


mkdir -p $HOME/bin && cp ./kubectl $HOME/bin/kubectl && export PATH=$PATH:$HOME/bin

echo 'export PATH=$PATH:$HOME/bin' >> ~/.bashrc


source <(kubectl completion bash) # setup autocomplete in bash into the current shell, bash-completion package should be installed first.
echo "source <(kubectl completion bash)" >> ~/.bashrc # add autocomplete permanently to your bash shell.



exit

relogin 

kubectl version

eksctl version


STEP:8 cretae eks cluster
-------------------------

create VPC using Cloud formation template

create stack in the cloud formaton 

select Template is ready

Amazon s3 url 

https://s3.us-west-2.amazonaws.com/amazon-eks/cloudformation/2020-10-29/amazon-eks-vpc-private-subnets.yaml


create stack

make note of private IP

subnet-0cc3781ec8e8ab2fd
subnet-09c0e6ffe16f0b88b

eksctl create cluster --help


eksctl create cluster --name demo-cluster --region us-east-1 --version 1.22 --vpc-private-subnets subnet-09c0e6ffe16f0b88b,subnet-0cc3781ec8e8ab2fd --node-volume-size 20 --ssh-pubic-key 'mylaptop' --without-nodegroup --auto-kubeconfig


eksctl utils write-kubeconfig -c demo-cluster -r 'us-east-1'

Step : 9  Cretae a node group
---------------------------------
Create node IAM role

vi node-role-trust-relationship.json
--------------------------------------

{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "Service": "ec2.amazonaws.com"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}

------------------------------------------------

aws iam create-role \
  --role-name AmazonEKSNodeRole \
  --assume-role-policy-document file://"node-role-trust-relationship.json"


aws iam attach-role-policy \
  --policy-arn arn:aws:iam::aws:policy/AmazonEKSWorkerNodePolicy \
  --role-name AmazonEKSNodeRole
aws iam attach-role-policy \
  --policy-arn arn:aws:iam::aws:policy/AmazonEC2ContainerRegistryReadOnly \
  --role-name AmazonEKSNodeRole


  aws iam attach-role-policy \
  --policy-arn arn:aws:iam::aws:policy/AmazonEKS_CNI_Policy \
  --role-name AmazonEKSNodeRole


eksctl create nodegroup \
  --cluster demo-cluster \
  --region us-east-1 \
  --name al-nodes \
  --node-type t3.medium \
  --nodes 2 \
  --nodes-min 2 \
  --nodes-max 4 \
  --ssh-access \
  --ssh-public-key mylaptop \
  --node-private-networking



kubectl get nodes















































