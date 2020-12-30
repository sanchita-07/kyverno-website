---
type: "docs"
title: Require Run As Non Root
linkTitle: Require Run As Non Root
weight: 48
description: >
    Containers must be required to run as non-root users.
---

## Category
Pod Security Standards (Restricted)

## Definition
[/pod-security/restricted/require-run-as-nonroot.yaml](https://github.com/kyverno/policies/raw/main//pod-security/restricted/require-run-as-nonroot.yaml)

```yaml
apiVersion: kyverno.io/v1
kind: ClusterPolicy
metadata:
  name: require-run-as-non-root
  annotations:
    policies.kyverno.io/category: Pod Security Standards (Restricted)
    policies.kyverno.io/description: Containers must be required to run as non-root users.
spec:
  background: true
  validationFailureAction: audit
  rules:
  - name: check-containers
    match:
      resources:
        kinds:
        - Pod
    validate:
      message: >-
        Running as root is not allowed. The fields spec.securityContext.runAsNonRoot,
        spec.containers[*].securityContext.runAsNonRoot, and
        spec.initContainers[*].securityContext.runAsNonRoot must be `true`.
      anyPattern:
      - spec:
          securityContext:
            runAsNonRoot: true
          containers:
          - =(securityContext):
              =(runAsNonRoot): true
          =(initContainers):
          - =(securityContext):
              =(runAsNonRoot): true              
      - spec:
          containers:
          - securityContext:
              runAsNonRoot: true
          =(initContainers):
          - securityContext:
              runAsNonRoot: true         

```