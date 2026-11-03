# Lab 3 — hello-app on Kubernetes with Terraform

Name: Himavarshini V

Roll number: 261100680016

## Part 1: first `terraform plan`

Paste the `Plan:` line and the summary above it.

```

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the
following symbols:
  + create

Terraform will perform the following actions:

  # kubernetes_deployment_v1.hello will be created
  + resource "kubernetes_deployment_v1" "hello" {
      + id               = (known after apply)
      + wait_for_rollout = true

      + metadata {
          + generation       = (known after apply)
          + name             = "hello-app"
          + namespace        = "default"
          + resource_version = (known after apply)
          + uid              = (known after apply)
        }

      + spec {
          + min_ready_seconds         = 0
          + paused                    = false
          + progress_deadline_seconds = 600
          + replicas                  = "3"
          + revision_history_limit    = 10

          + selector {
              + match_labels = {
                  + "app" = "hello-app"
                }
            }

          + strategy (known after apply)

          + template {
              + metadata {
                  + generation       = (known after apply)
                  + labels           = {
                      + "app" = "hello-app"
                    }
                  + name             = (known after apply)
                  + resource_version = (known after apply)
                  + uid              = (known after apply)
                }
              + spec {
                  + automount_service_account_token  = true
                  + dns_policy                       = "ClusterFirst"
                  + enable_service_links             = true
                  + host_ipc                         = false
                  + host_network                     = false
                  + host_pid                         = false
                  + hostname                         = (known after apply)
                  + node_name                        = (known after apply)
                  + restart_policy                   = "Always"
                  + scheduler_name                   = (known after apply)
                  + service_account_name             = (known after apply)
                  + share_process_namespace          = false
                  + termination_grace_period_seconds = 30

                  + container {
                      + image                      = "shripada/hello-app:v1"
                      + image_pull_policy          = (known after apply)
                      + name                       = "hello-app"
                      + restart_policy             = (known after apply)
                      + stdin                      = false
                      + stdin_once                 = false
                      + termination_message_path   = "/dev/termination-log"
                      + termination_message_policy = (known after apply)
                      + tty                        = false

                      + port {
                          + container_port = 80
                          + protocol       = "TCP"
                        }

                      + resources (known after apply)
                    }

                  + image_pull_secrets (known after apply)

                  + readiness_gate (known after apply)
                }
            }
        }
    }

  # kubernetes_service_v1.hello will be created
  + resource "kubernetes_service_v1" "hello" {
      + id                     = (known after apply)
      + status                 = (known after apply)
      + wait_for_load_balancer = true

      + metadata {
          + generation       = (known after apply)
          + name             = "hello-app"
          + namespace        = "default"
          + resource_version = (known after apply)
          + uid              = (known after apply)
        }

      + spec {
          + allocate_load_balancer_node_ports = true
          + cluster_ip                        = (known after apply)
          + cluster_ips                       = (known after apply)
          + external_traffic_policy           = (known after apply)
          + health_check_node_port            = (known after apply)
          + internal_traffic_policy           = (known after apply)
          + ip_families                       = (known after apply)
          + ip_family_policy                  = (known after apply)
          + publish_not_ready_addresses       = false
          + selector                          = {
              + "app" = "hello-app"
            }
          + session_affinity                  = "None"
          + type                              = "LoadBalancer"

          + port {
              + node_port   = (known after apply)
              + port        = 8080
              + protocol    = "TCP"
              + target_port = "80"
            }

          + session_affinity_config (known after apply)
        }
    }

Plan: 2 to add, 0 to change, 0 to destroy.

Changes to Outputs:
  + url = "http://localhost:8080"

───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if
you run "terraform apply" now.

```

## Part 1: `terraform output -raw url`

```
url = "http://localhost:8080"
```

## Part 2: plan for the v1 → v2 image change

Paste the lines that show the image change, and the `Plan:` line.

```
kubernetes_service_v1.hello: Refreshing state... [id=default/hello-app]
kubernetes_deployment_v1.hello: Refreshing state... [id=default/hello-app]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the
following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # kubernetes_deployment_v1.hello will be updated in-place
  ~ resource "kubernetes_deployment_v1" "hello" {
        id               = "default/hello-app"
        # (1 unchanged attribute hidden)

      ~ spec {
            # (5 unchanged attributes hidden)

          ~ template {
              ~ spec {
                    # (18 unchanged attributes hidden)

                  ~ container {
                      ~ image                      = "shripada/hello-app:v1" -> "shripada/hello-app:v2"
                        name                       = "hello-app"
                        # (10 unchanged attributes hidden)

                        # (2 unchanged blocks hidden)
                    }
                }

                # (1 unchanged block hidden)
            }

            # (2 unchanged blocks hidden)
        }

        # (1 unchanged block hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.

───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if
you run "terraform apply" now.
```

## Part 3: plan that found the drift after `kubectl scale`

```
kubernetes_service_v1.hello: Refreshing state... [id=default/hello-app]
kubernetes_deployment_v1.hello: Refreshing state... [id=default/hello-app]

Terraform used the selected providers to generate the following execution plan. Resource actions are indicated with the
following symbols:
  ~ update in-place

Terraform will perform the following actions:

  # kubernetes_deployment_v1.hello will be updated in-place
  ~ resource "kubernetes_deployment_v1" "hello" {
        id               = "default/hello-app"
        # (1 unchanged attribute hidden)

      ~ spec {
          ~ replicas                  = "5" -> "3"
            # (4 unchanged attributes hidden)

            # (3 unchanged blocks hidden)
        }

        # (1 unchanged block hidden)
    }

Plan: 0 to add, 1 to change, 0 to destroy.

───────────────────────────────────────────────────────────────────────────────────────────────────────────────────────

Note: You didn't use the -out option to save this plan, so Terraform can't guarantee to take exactly these actions if
you run "terraform apply" now.


Deleted the Service by hand. terraform plan showed:
# kubernetes_service_v1.hello will be created
Plan: 1 to add, 0 to change, 0 to destroy.
After terraform apply, the page recovered.
```

## Part 4: `kubectl get svc -A` showing dev and prod

```
NAMESPACE     NAME         TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                  AGE
default       kubernetes   ClusterIP      10.96.0.1      <none>        443/TCP                  3d17h
dev           hello-app    LoadBalancer   10.96.71.123   172.18.0.8    8080:31440/TCP           10s
kube-system   kube-dns     ClusterIP      10.96.0.10     <none>        53/UDP,53/TCP,9153/TCP   3d17h
prod          hello-app    LoadBalancer   10.96.231.51   172.18.0.7    8090:30163/TCP           83s
```

## Break it: the error after deleting `terraform.tfstate`

```
Plan: 3 to add, 0 to change, 0 to destroy.

Do you want to perform these actions?
  Terraform will perform the actions described above.
  Only 'yes' will be accepted to approve.

  Enter a value: yes

module.hello.kubernetes_namespace_v1.env: Creating...
╷
│ Error: Error creating namespace
│
│   with module.hello.kubernetes_namespace_v1.env,
│   on ..\..\modules\hello-app\main.tf line 34, in resource "kubernetes_namespace_v1" "env":
│   34: resource "kubernetes_namespace_v1" "env" {
│
│ namespaces "dev" already exists
╵
```

## Break it: the wrong selector

What did you see, and how did you find the cause? One or two sentences.

`terraform apply` finished with no errors, but the dev page stopped loading while prod kept working. I ran `kubectl get endpointslices -n dev -l kubernetes.io/service-name=hello-app` and the EndpointSlice was empty, because the Service selector `helo-app` (typo) matched no Pod labels. Terraform only checks that the cluster matches the code, not that the code is right.

## Screenshot

Save a browser screenshot as `screenshot.png` in the repository root.
