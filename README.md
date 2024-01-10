# argocd-hub-spoke-demo
Deploy resources to multiple kubernetes clusters using Argo CD.

1. Ensure you have enough VPCs to create.
2. eksctl takes approx. 30 mins so run the command initially.

eksctl create cluster --name hub-cluster --region us-west-1
eksctl create cluster --name spoke-cluster-1 --region us-west-1
eksctl create cluster --name spoke-cluster-2 --region us-west-1

Note: Teardown after the demo using eksctl delete cluster.

install kubectl
install eksctl
install aws cli
install argocd

install alb controller and its config.

Argo CD Ingress configuration
https://argo-cd.readthedocs.io/en/stable/operator-manual/ingress/#aws-application-load-balancers-albs-and-classic-elb-http-mode

Settings:

- start Argo CD in insecure mode

- alb.ingress.kubernetes.io/target-type: instance

Ingress file:

```
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  namespace: argocd
  name: argocd
  annotations:
    kubernetes.io/ingress.class: alb
    alb.ingress.kubernetes.io/scheme: internet-facing
    alb.ingress.kubernetes.io/target-type: instance
spec:
  rules:
    - http:
        paths:
          - path: /
            pathType: Prefix
            backend:
              service:
                name: argocd-server
                port:
                  number: 80
```