# Lab 3 — hello-app on Kubernetes with Terraform

Name: Himavarshini V

Roll number: 261100680016

## Part 1: first `terraform plan`

```
Plan: 2 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + url = "http://localhost:8080"
```

The plan creates `kubernetes_deployment_v1.hello` and `kubernetes_service_v1.hello` in `namespace = "default"`.

## Part 1: `terraform output -raw url`

```
http://localhost:8080
```

## Part 2: plan for the v1 → v2 image change

```
  # kubernetes_deployment_v1.hello will be updated in-place
      ~ image = "shripada/hello-app:v1" -> "shripada/hello-app:v2"

Plan: 0 to add, 1 to change, 0 to destroy.
```

## Part 3: plan that found the drift after `kubectl scale`

```
  # kubernetes_deployment_v1.hello will be updated in-place
      ~ replicas = "5" -> "3"

Plan: 0 to add, 1 to change, 0 to destroy.
```

I also deleted the Service by hand. `terraform plan` showed
`kubernetes_service_v1.hello will be created` and
`Plan: 1 to add, 0 to change, 0 to destroy.`
After `terraform apply`, the page recovered.

## Part 4: `kubectl get svc -A` showing dev and prod

```
NAMESPACE     NAME         TYPE           EXTERNAL-IP   PORT(S)
dev           hello-app    LoadBalancer   172.18.0.8    8080:31440/TCP
prod          hello-app    LoadBalancer   172.18.0.7    8090:30163/TCP
```

## Break it: the error after deleting `terraform.tfstate`

```
Plan: 3 to add, 0 to change, 0 to destroy.

Error: Error creating namespace
namespaces "dev" already exists
```

## Break it: the wrong selector

`terraform apply` finished with no errors, but the dev page stopped loading while prod kept working. I ran `kubectl get endpointslices -n dev -l kubernetes.io/service-name=hello-app` and the EndpointSlice was empty, because the Service selector `helo-app` (typo) matched no Pod labels. Terraform only checks that the cluster matches the code, not that the code is right.

## Screenshot

Browser screenshot of the dev (8080) and prod (8090) pages: `screenshot.png`