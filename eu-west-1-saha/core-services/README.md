# K8s cluster core services

These are the applications that required in any EKS clusters we have.

## Webhook admissions and Calico CNI

As we have replaced the aws-cni with Calico there's a problem with Webhooks. The k8s apiserver can't reach to the IP address of the pods because it can't recognize the IPs assigned to the pods by Calico.

The workaround is to enable `hostnetwork: true` in the pod and set a custom container port for that pod. These ports are accessible by the EKS control plane (Security group rules) 10200 to 10300, 4443, 15017

The table below shows the applications and the ports are used for their webhook admission. for additional applications using the hostnetwork you need to make sure there's no conflict between port numbers.

| App                | hostnetwork      | Ports                              |
|--------------------|------------------|------------------------------------|
| metric-server      | enabled          | 4443                               |
| cert-manager       | enabled          | 10260, 6080, 9402                  |
| external-secrets   | enabled          | 10210, 8085, 8086                  |
| keda-webhook       | enabled          | 10230                              |
| keda-metricsServer | enabled          | 10231, 18080                       |
|                    |                  |                                    |
