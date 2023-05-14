# Kubernetes Manifests for Jenkins Deployment

## Additional information regarding post deployment of Jenkins in your Kubernetes cluster

1. Refer https://devopscube.com/setup-jenkins-on-kubernetes-cluster/ for step by step process to use these manifests.

1. To get the initial administrator password for your Jenkins user, use `kubectl log pod-name-here --namespace devops-tools` to see the init password.

1. Install the `kubernetes plugin` to enable the integration between kuberenetes and Jenkins even if Jenkins is running as a POD in K8S.
   - `Manage Jenkins` -> `Manage Plugins` -> `Available Plugins` -> Search for `Kubernetes`

1. Install your `kubernetes cloud` from the Jenkins dashboard
   - `Manage Jenkins` -> `Manage Nodes and  Clouds` -> `Configure Clouds` -> `Add a new cloud (Kuberenetes)` -> `Kubernetes Cloud details` -> `WebSocket`
   
   - Test your connection by clicking on `Test Connection`
      - Message: `Connection to Kuberntes v1.26.1` should appear

   - You may if you want, define the pod template to use during your Jenkins deployment

# Access your application using Ingress resource
1. To deploy the Ingress manifest file, make sure you enable/install the Ingress controller is your cluster
   - Make sure the minikube ip address is mapped in your machine `hosts` file with any name of your choice
   - If your are in Minikube: `minikube addons list` and `minikube addons enable ingress` to enable it if it shows *disabled*
   ```table
   |-----------------------------|----------|--------------|--------------------------------|
   |         ADDON NAME          | PROFILE  |    STATUS    |           MAINTAINER           |
   |-----------------------------|----------|--------------|--------------------------------|
   | ambassador                  | minikube | disabled     | 3rd party (Ambassador)         |
   | auto-pause                  | minikube | disabled     | Google                         |
   | cloud-spanner               | minikube | disabled     | Google                         |
   | csi-hostpath-driver         | minikube | disabled     | Kubernetes                     |
   | dashboard                   | minikube | disabled     | Kubernetes                     |
   | default-storageclass        | minikube | enabled ✅   | Kubernetes                     |
   | efk                         | minikube | disabled     | 3rd party (Elastic)            |
   | freshpod                    | minikube | disabled     | Google                         |
   | gcp-auth                    | minikube | disabled     | Google                         |
   | gvisor                      | minikube | disabled     | Google                         |
   | headlamp                    | minikube | disabled     | 3rd party (kinvolk.io)         |
   | helm-tiller                 | minikube | disabled     | 3rd party (Helm)               |
   | inaccel                     | minikube | disabled     | 3rd party (InAccel             |
   |                             |          |              | [info@inaccel.com])            |
   | ingress                     | minikube | enabled ✅   | Kubernetes                     |
   | ingress-dns                 | minikube | disabled     | Google                         |
   | istio                       | minikube | disabled     | 3rd party (Istio)              |
   | istio-provisioner           | minikube | disabled     | 3rd party (Istio)              |
   | kong                        | minikube | disabled     | 3rd party (Kong HQ)            |
   | kubevirt                    | minikube | disabled     | 3rd party (KubeVirt)           |
   | logviewer                   | minikube | disabled     | 3rd party (unknown)            |
   | metallb                     | minikube | disabled     | 3rd party (MetalLB)            |
   | metrics-server              | minikube | disabled     | Kubernetes                     |
   | nvidia-driver-installer     | minikube | disabled     | Google                         |
   | nvidia-gpu-device-plugin    | minikube | disabled     | 3rd party (Nvidia)             |
   | olm                         | minikube | disabled     | 3rd party (Operator Framework) |
   | pod-security-policy         | minikube | disabled     | 3rd party (unknown)            |
   | portainer                   | minikube | disabled     | 3rd party (Portainer.io)       |
   | registry                    | minikube | disabled     | Google                         |
   | registry-aliases            | minikube | disabled     | 3rd party (unknown)            |
   | registry-creds              | minikube | disabled     | 3rd party (UPMC Enterprises)   |
   | storage-provisioner         | minikube | enabled ✅   | Google                         |
   | storage-provisioner-gluster | minikube | disabled     | 3rd party (Gluster)            |
   | volumesnapshots             | minikube | disabled     | Kubernetes                     |
   |-----------------------------|----------|--------------|--------------------------------|
   ```
1. The ingress controller gets installed in the cluster as a pod in the `ingress-nginx` namespace
  - Use: `kubectl get pods --namespace ingress-nginx` to see it.

  ```sh
   NAME                                       READY   STATUS      RESTARTS        AGE
   ingress-nginx-admission-create-bnhv7       0/1     Completed   0               27h
   ingress-nginx-admission-patch-sgpzb        0/1     Completed   1               27h
   ingress-nginx-controller-77669ff58-hm7tl   1/1     Running     1 (7h16m ago)   27h
  ```