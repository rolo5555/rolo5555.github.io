---
date: 2024-09-13
categories:
- Kubernetes  
readtime: 10  
authors:
- rolo
---

# `kubesec` Static analysis security scanning tool. 

## The problem
Kubernetes resources can be vulnerable to misconfigurations, leading to security risks in your infrastructure. Detecting these issues early is critical to maintaining a secure environment.

## What is ?
Is an [open-source](https://github.com/controlplaneio/kubesec) tool that performs static analysis on Kubernetes resources, identifying security risks before deployment. It ensures that your Kubernetes configuration adheres to best security practices.

## How to usage `kubesec`
There are several ways to use `kubesec` to scan your Kubernetes resources:

- Docker container image: `docker.io/kubesec/kubesec:v2`
- Linux/MacOS/Win binary (get the latest release)
- Kubernetes Admission Controller
- Kubectl plugin


### Using the Docker Image
The simplest way to run kubesec is by using its Docker image and passing the file you want to scan. For example, to check the `app1-510d6362.yaml` file:

```bash 
docker run -i kubesec/kubesec:512c5e0 scan /dev/stdin < app1-510d6362.yaml
```

This command runs a scan on the specified file, producing results like this:

```bash
[
  {
    "object": "Pod/pod.default",
    "valid": true,
    "message": "Passed with a score of 0 points",
    "score": 0,
    "scoring": {
      "advise": [
        {
          "selector": "containers[] .securityContext .readOnlyRootFilesystem == true",
          "reason": "An immutable root filesystem can prevent malicious binaries being added to PATH and increase attack cost"
        },
        {
          "selector": "containers[] .securityContext .runAsNonRoot == true",
          "reason": "Force the running image to run as a non-root user to ensure least privilege"
        },
        {
          "selector": "containers[] .securityContext .runAsUser -gt 10000",
          "reason": "Run as a high-UID user to avoid conflicts with the host's user table"
        },
        {
          "selector": "containers[] .securityContext .capabilities .drop",
          "reason": "Reducing kernel capabilities available to a container limits its attack surface"
        },
        {
          "selector": "containers[] .securityContext .capabilities .drop | index(\"ALL\")",
          "reason": "Drop all capabilities and add only those required to reduce syscall attack surface"
        },
        {
          "selector": "containers[] .resources .requests .cpu",
          "reason": "Enforcing CPU requests aids a fair balancing of resources across the cluster"
        },
        {
          "selector": "containers[] .resources .limits .cpu",
          "reason": "Enforcing CPU limits prevents DOS via resource exhaustion"
        },
        {
          "selector": "containers[] .resources .requests .memory",
          "reason": "Enforcing memory requests aids a fair balancing of resources across the cluster"
        },
        {
          "selector": "containers[] .resources .limits .memory",
          "reason": "Enforcing memory limits prevents DOS via resource exhaustion"
        },
        {
          "selector": ".spec .serviceAccountName",
          "reason": "Service accounts restrict Kubernetes API access and should be configured with least privilege"
        },
        {
          "selector": ".metadata .annotations .\"container.seccomp.security.alpha.kubernetes.io/pod\"",
          "reason": "Seccomp profiles set minimum privilege and secure against unknown threats"
        },
        {
          "selector": ".metadata .annotations .\"container.apparmor.security.beta.kubernetes.io/nginx\"",
          "reason": "Well defined AppArmor policies may provide greater protection from unknown threats. WARNING: NOT PRODUCTION READY"
        }
      ]
    }
  }
]
```


