# Devops-Projects-16--App-deployment-on-kubernetes-Cluster
## Prereq

* Setup domain for Kubernetes Cluster (C)
* Ubuntu vm and install
  * kops
  * kubectl
  * ssh keys conf
  * awscli conf
* Create s3 bucket
* Create IAM user (kopsadmin) and attach `AdministratorAccess` permissions
* Create Route53 hosted zone for subdomain


```
ssh-keygen -t rsa -f /home/ubuntu/.ssh/id_rsa -q -P ""
sudo apt update -y && sudo apt install awscli -y
aws configure
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x ./kubectl
mv kubectl /usr/local/bin/
curl -LO https://github.com/kubernetes/kops/releases/download/$(curl -s https://api.github.com/repos/kubernetes/kops/releases/latest | grep tag_name | cut -d '"' -f 4)/kops-linux-amd64
chmod +x kops-linux-amd64
sudo mv ./kops-linux-amd64 /usr/local/bin/kops
```

## Create update and validate cluster

```
kops create cluster --name=kube.jenko.ga \ 
--state=s3://kops-statete --zones=us-east-2a,us-east-2b \ 
--node-count=2 --node-size=t3.small --master-size=t3.medium --dns-zone=kube.jenko.ga \ 
--node-volume-size=8 --master-volume-size=8

###
kops update cluster --name kube.jenko.ga --state=s3://kops-statete --yes --admin

###
kops validate cluster \
--name kube.jenko.ga \
--state=s3://kops-statete

cat ~/.kube/config

kubectl get nodes
```

### Create EBS

`aws ec2 create-volume --region us-east-2 --availability-zone us-east-2a --size 3 --volume-type gp2`

