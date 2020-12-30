---
type: "docs"
title: Disallow Default Namespace
linkTitle: Disallow Default Namespace
weight: 6
description: >
    Kubernetes namespaces are an optional feature that provide a way to segment and isolate cluster resources across multiple applications and users. As a best practice, workloads should be isolated with namespaces. Namespaces should be required and the default (empty) namespace should not be used.
---

## Category
Workload Isolation

## Definition
[/best-practices/disallow_default_namespace.yaml](https://github.com/kyverno/policies/raw/main//best-practices/disallow_default_namespace.yaml)

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: disallow-default-namespace
  annotations:
    pod-policies.kyverno.io/autogen-controllers: none	
    policies.kyverno.io/category: Workload Isolation
    policies.kyverno.io/description: Kubernetes namespaces are an optional feature 
      that provide a way to segment and isolate cluster resources across multiple 
      applications and users. As a best practice, workloads should be isolated with 
      namespaces. Namespaces should be required and the default (empty) namespace 
      should not be used.
spec:
  validationFailureAction: audit
  rules:
  - name: validate-namespace
    match:
      resources:
        kinds:
        - Pod
    validate:
      message: "Using 'default' namespace is not allowed"
      pattern:
        metadata:
          namespace: "!default"
  - name: require-namespace
    match:
      resources:
        kinds:
        - Pod
    validate:
      message: "A namespace is required"
      pattern:
        metadata:
          namespace: "?*"
  - name: validate-podcontroller-namespace
    match:
      resources:
        kinds:
        - DaemonSet
        - Deployment
        - Job
        - StatefulSet
    validate:
      message: "Using 'default' namespace is not allowed for podcontrollers"
      pattern:
        metadata:
          namespace: "!default"
  - name: require-podcontroller-namespace
    match:
      resources:
        kinds:
        - DaemonSet
        - Deployment
        - Job
        - StatefulSet
    validate:
      message: "A namespace is required for podcontrollers"
      pattern:
        metadata:
          namespace: "?*"


```