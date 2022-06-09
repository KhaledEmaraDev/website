---
title: "Prevent Naked Pods"
category: Other
version: 1.6.0
subject: Pod
policyType: "validate"
description: >
    Pods not created by workload controllers such as Deployments have no self-healing or scaling abilities and are unsuitable for production. This policy prevents such "naked" Pods from being created unless they originate from a higher-level workload controller of some sort.
---

## Policy Definition
<a href="https://github.com/kyverno/policies/raw/main//other/prevent-naked-pods/prevent-naked-pods.yaml" target="-blank">/other/prevent-naked-pods/prevent-naked-pods.yaml</a>

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: prevent-naked-pods
  annotations:
    policies.kyverno.io/title: Prevent Naked Pods
    pod-policies.kyverno.io/autogen-controllers: none
    policies.kyverno.io/category: Other
    policies.kyverno.io/severity: medium
    kyverno.io/kyverno-version: 1.7.0
    policies.kyverno.io/minversion: 1.6.0
    kyverno.io/kubernetes-version: "1.23"
    policies.kyverno.io/subject: Pod
    policies.kyverno.io/description: >-
      Pods not created by workload controllers such as Deployments
      have no self-healing or scaling abilities and are unsuitable for production.
      This policy prevents such "naked" Pods from being created unless they originate
      from a higher-level workload controller of some sort.
spec:
  validationFailureAction: audit
  background: true
  rules:
  - name: naked-pods
    match:
      any:
      - resources:
          kinds:
          - Pod
    preconditions:
      all:
      - key: "{{request.operation}}"
        operator: NotEquals
        value: DELETE
    validate:
      message: "Naked Pods are not allowed. They must be created by Pod controllers."
      deny:
        conditions:
          any:
          - key: ownerReferences
            operator: AnyNotIn
            value: "{{request.object.metadata.keys(@)}}"
```