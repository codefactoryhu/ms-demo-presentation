### AWS environment for members
```
terragrunt run-all init
terragrunt run-all plan --terragrunt-exclude-dir ./live/dev/eks-alb \
                                                --terragrunt-exclude-dir ./live/dev/eks-cloudwatch-logs \
                                                --terragrunt-exclude-dir ./live/dev/eks-autoscaler
terragrunt run-all apply

aws eks update-kubeconfig --name dev-ingram-demo --region eu-central-1
```

#### GitHub Container Registry Preparation
```
# Personal access tokens (classic)
# Settings -> Developer settings -> Personal access tokens -> Tokens (classic).
# write:packages permission


echo 'token' | helm registry login ghcr.io -u ptzool --password-stdin

# github-imagepullsecret
echo -n "ptzool:ghp_sxxxxxxxxxxxk" | base64

echo -n  '{"auths":{"ghcr.io":{"auth":"xxxxxxxxxxxx="}}}' | base64

kubectl apply -f - <<EOF
kind: Secret
type: kubernetes.io/dockerconfigjson
apiVersion: v1
metadata:
    name: github-imagepullsecret
data:
    .dockerconfigjson: xxxxxxxxxxxxxx==
EOF


helm install ms-demo oci://ghcr.io/codefactoryhu/ms-demo --version=1.3.0

```