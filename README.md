# External-DNS-Setup-Kubernetes
# Pre-Requisites
    - EKS-Cluster Setup
    - Application Load Balancer
# EKS-Cluster Setup:
  [EKS Cluster Setup](https://github.com/Naresh240/eks-cluster-setup/blob/main/README.md)
# ALB-Ingress-Controller Setup:
  [ALB Ingress Controller](https://github.com/Naresh240/ALB-Ingress-Controller-Setup/blob/main/README.md)
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

 Open Deploy-ExternalDNS.yml file replce external-dns role arn:
  annotations:
    eks.amazonaws.com/role-arn: arn:aws:iam::057996059708:role/eksctl-eksdemo-addon-iamserviceaccount-defau-Role1-1VWNIU0P54SWC
 # Deploy External-dns yaml file
    kubectl apply -f external-dns.yml
