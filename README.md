
To Install AWS CLI
```
url "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"
```
for unzipping
```
unzip awscliv2.zip
```
to install in ubuntu
```
sudo ./aws/install
```
enter the details like access key ,secret key, region and format
```
aws configure
```
To install kubectl
```
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
chmod +x kubectl
sudo mv kubectl /usr/local/bin/kubectl
```
To Install eksctl
```
curl -s "https://api.github.com/repos/weaveworks/eksctl/releases/latest" | grep "browser_download_url.*linux-amd64.tar.gz" | cut -d : -f 2,3 | tr -d \" | wget -qi -
tar -xzf eksctl*.tar.gz
sudo mv eksctl /usr/local/bin/eksctl
```
Creating an EKS Cluster
```
eksctl create cluster --name fastapi-cluster --version 1.27 --region ap-south-1 --nodegroup-name linux-nodes --node-type t3.micro --nodes 2 --nodes-min 1 --nodes-max 3 --managed
```
Create the FastAPI ECR Repository
```
aws ecr create-repository --repository-name fastapi_service --region ap-south-1
```
Create the PostgreSQL ECR Repository
```
aws ecr create-repository --repository-name postgres_db --region ap-south-1
```
To authenticate Docker to ECR
```
aws ecr get-login-password --region ap-south-1 | docker login --username AWS --password-stdin <aws_account_id>.dkr.ecr.ap-south-1.amazonaws.com
```
Tagging docker image
```
docker tag fastapi_service:latest <aws_account_id>.dkr.ecr.ap-south-1.amazonaws.com/fastapi_service:latest
```
pushed image to ecr
```
docker push <aws_account_id>.dkr.ecr.ap-south-1.amazonaws.com/fastapi_service:latest
```
Tagging PostgreSQL Image
```
docker tag postgres_db:latest <aws_account_id>.dkr.ecr.ap-south-1.amazonaws.com/postgres_db:latest
```
pushing postgressql image to ecr
```
docker push <aws_account_id>.dkr.ecr.ap-south-1.amazonaws.com/postgres_db:lates
```
create fastapi-deployment.yaml, postgres-deployment.yaml,fastapi-service.yaml and postgres-service.yaml

To create secret details
```
kubectl create secret generic db-credentials \
--from-literal=POSTGRES_USER=postgres \
--from-literal=POSTGRES_PASSWORD=Teja@1234 \
--from-literal=POSTGRES_DB=User
```

Apply the Deployments and Services

for fastapi-deployment.yaml
```
kubectl apply -f fastapi-deployment.yaml
```
for postgres-deployment.yaml
```
kubectl apply -f postgres-deployment.yaml
```
for fastapi-service.yaml 
```
kubectl apply -f fastapi-service.yaml
```
for postgres-service.yaml
```
kubectl apply -f postgres-service.yaml
```

To verify pods
```
kubectl get pods
```
To verify services
```
kubectl get services
```
To get the logs of fastapi
```
kubectl logs fastapi-deployment-7445678d76-87j2x
```
To access the FastAPI Swagger UI using the LoadBalancer DNS
```
http://a74c3bb5d0234461dab0753204ccfb99-562290360.ap-south-1.elb.amazonaws.com/docs
```
