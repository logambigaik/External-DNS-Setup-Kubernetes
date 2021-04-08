# External-DNS-Setup-Kubernetes
# Pre-Requisites
    - EKS-Cluster Setup
    - Application Load Balancer
# EKS-Cluster Setup:
  [EKS Cluster Setup](https://github.com/Naresh240/eks-cluster-setup/blob/main/README.md)
# ALB-Ingress-Controller Setup:
  [ALB Ingress Controller](https://github.com/logambigaik/ALB-Ingress-Controller-Setup.git/README.md)
# External DNS Setup
  Create policy for External-DNS using below JSON content:
  
    {
      "Version": "2012-10-17",
      "Statement": [
        {
          "Effect": "Allow",
          "Action": [
            "route53:ChangeResourceRecordSets"
          ],
          "Resource": [
            "arn:aws:route53:::hostedzone/*"
          ]
        },
        {
          "Effect": "Allow",
          "Action": [
            "route53:ListHostedZones",
            "route53:ListResourceRecordSets"
          ],
          "Resource": [
            "*"
          ]
        }
      ]
    }
    
 ![image](https://user-images.githubusercontent.com/54719289/113169388-75af1180-923d-11eb-9698-3f3a50cee9a3.png)


 # Create Iam Role and attach Policy and service account:
     eksctl create iamserviceaccount \
        --name external-dns \
        --region us-east-1 \
        --namespace default \
        --cluster eksdemo \
        --attach-policy-arn arn:aws:iam::136962450893:policy/external-DNS \
        --approve \
        --override-existing-serviceaccounts
 # Verify the Service Account:
    kubectl get sa external-dns
 
 ![image](https://user-images.githubusercontent.com/54719289/113169835-e3f3d400-923d-11eb-87f3-3574b833667a.png)


    Search eks-addonrole in IAM and check if that role has external-DNS access policy. 
    if so choose and update the same in external-dns.yml file
    (arn:aws:iam::136962450893:role/eksctl-eksdemo-addon-iamserviceaccount-defau-Role1-XXXX)
 
 Open Deploy-ExternalDNS.yml file replce external-dns role arn:
  
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::136962450893:role/eksctl-eksdemo-addon-iamserviceaccount-defau-Role1-13NJMLQ2DIXII
 # Deploy External-dns yaml file
    kubectl get sa external-dns
    kubectl apply -f external-dns.yml

![image](https://user-images.githubusercontent.com/54719289/113171448-616c1400-923f-11eb-9333-86a4df7809ad.png)
![image](https://user-images.githubusercontent.com/54719289/113171562-7fd20f80-923f-11eb-937a-08a87fb6588e.png)

REmove record A in Route53 and refresh:

![image](https://user-images.githubusercontent.com/54719289/113171973-e820f100-923f-11eb-9c56-fd9c2ae7eb8a.png)

Now delete ingress1.yml and apply ,

![image](https://user-images.githubusercontent.com/54719289/113172434-56fe4a00-9240-11eb-834e-9bc3d29c0b34.png)

![image](https://user-images.githubusercontent.com/54719289/113172641-8319cb00-9240-11eb-8fea-4b6ffcdd25cc.png)

![image](https://user-images.githubusercontent.com/54719289/113172984-e0ae1780-9240-11eb-8a83-cc1d5c4bfcb5.png)
