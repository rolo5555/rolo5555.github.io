---
date: 2024-09-13
categories:
- Kubernetes  
readtime: 10  
authors:
- rolo
---

# `Kube-bench`: Automating Kubernetes Security Checks

## What is it?
The [Center for Internet Security (CIS)](https://www.cisecurity.org/benchmark/kubernetes) has established benchmarks to ensure the secure deployment of Kubernetes clusters. These benchmarks provide security configuration guidelines for Kubernetes, aiming to help organizations protect their environments from potential vulnerabilities.

One tool that automates this important process is `kube-bench`. It runs checks against your Kubernetes cluster based on the CIS Kubernetes Benchmark, helping to verify whether your cluster is configured securely.


## Why Use Kube-bench?
`kube-bench` streamlines security auditing by automating the verification of your Kubernetes setup. It checks for best practices, identifies misconfigurations, and reports areas where your setup might fall short of CIS recommendations. This makes it easier to maintain compliance and reduce the risk of exposure to security threats.

Whether you're running Kubernetes in production, or setting up a development cluster, regular use of kube-bench helps ensure that your deployments meet security standards.

For more details and to start using kube-bench, visit the [official GitHub repository](https://github.com/aquasecurity/kube-bench).

## Running kube-bench on Kubernetes Clusters
The `kube-bench` tool can be executed in various ways depending on your Kubernetes cluster setup. It ensures that your Kubernetes deployment aligns with the CIS Kubernetes Benchmark, which provides security guidelines.

In this blog, I’ll share how I used kube-bench to audit both worker and master nodes of a Kubernetes cluster deployed with [kOps](https://kops.sigs.k8s.io/) on [AWS](https://kops.sigs.k8s.io/getting_started/aws/).

### Worker Node Auditing
To audit the worker nodes, I submitted a Kubernetes job that runs `kube-bench` specifically for worker node configuration. Below are the steps:

```bash
# Download the worker node job configuration
$ curl -O https://raw.githubusercontent.com/aquasecurity/kube-bench/main/job.yaml

$ kubectl apply -f job.yaml
job.batch/kube-bench created

$ kubectl get pods
NAME                      READY   STATUS              RESTARTS   AGE
kube-bench-j76s9   0/1     ContainerCreating   0          3s

# Wait for a few seconds for the job to complete
$ kubectl get pods
NAME                      READY   STATUS      RESTARTS   AGE
kube-bench-j76s9   0/1     Completed   0          11s

# The results are held in the pod's logs
kubectl logs kube-bench-j76s9
[INFO] 4 Worker Node Security Configuration
[INFO] 4.1 Worker Node Configuration Files
...
```

The logs will contain a detailed list of recommendations, outlining the identified security issues and how to address them. You can see an example of the full output in this [Gist](https://gist.github.com/rolo5555/6d96f59f2d7d390adfd99958e688ee04).

Within the output, each problematic area is explained, and kube-bench offers solutions for improving security on the worker nodes.

<iframe src="https://gist.github.com/rolo5555/6d96f59f2d7d390adfd99958e688ee04" width="100%" height="500px" style="border: 1px solid #ddd;"></iframe>

### Master Node Auditing
To audit the master nodes (control plane), I used a script specifically designed for the master node configuration. Follow these steps to run the audit:

```bash
# Download the master node job configuration
$ curl -O https://raw.githubusercontent.com/aquasecurity/kube-bench/main/job-master.yaml

$ kubectl apply -f job-master.yaml
job.batch/kube-bench created

$ kubectl get pods
NAME                      READY   STATUS              RESTARTS   AGE
kube-bench-xxxxx   0/1     ContainerCreating   0          3s

# Wait for a few seconds for the job to complete
$ kubectl get pods
NAME                      READY   STATUS      RESTARTS   AGE
kube-bench-xxxxx   0/1     Completed   0          11s

# The results are held in the pod's logs
kubectl logs kube-bench-j76s9
[INFO] 1 Control Plane Security Configuration
[INFO] 1.1 Control Plane Node Configuration Files
...
```
The logs will contain a detailed list of recommendations, outlining the identified security issues and how to address them. You can see an example of the full output in this [Gist](https://gist.github.com/rolo5555/0bc6ab77eaabce438d3a6f90f848c40e).