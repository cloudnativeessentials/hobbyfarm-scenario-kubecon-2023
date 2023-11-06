+++
title = "Policy: OPA and Gatekeeper"
weight = 18
+++

## Open Policy Agent and Gatekeeper

Open Policy Agent (OPA) is a policy engine with policy-as-code and a CNCF graduated project.
OPA is used to enforce policies in cloud native environments.

Gatekeeper is a validating and mutating webhook that enforces CRD-based policies executed by Open Policy Agent.


With Gatekeeper, you can enforce policies like container images must be pulled from a specific container registry or not allow privilege escalation in containers.

1. Add the Gatekeeper helm repo.

```ctr:kubernetes
helm repo add gatekeeper https://open-policy-agent.github.io/gatekeeper/charts
helm repo update
```

Expected output:

```shell
"gatekeeper" has been added to your repositories
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "gatekeeper" chart repository
...Successfully got an update from the "fluent" chart repository
...Successfully got an update from the "elastic" chart repository
...Successfully got an update from the "prometheus-community" chart repository
Update Complete. ⎈Happy Helming!⎈
```

2. Install Gatekeeper.

```ctr:kubernetes
helm install gatekeeper/gatekeeper --name-template=gatekeeper --namespace gatekeeper-system --create-namespace
```

Expected output:

```shell
NAME: gatekeeper
LAST DEPLOYED: Mon Nov  6 03:51:16 2023
NAMESPACE: gatekeeper-system
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

## Constraint Framework

A Constraint is the declaration of a policy. For example, if you require each resource in a specific namespace requires a `key` label.

Before you can use Constraints, you must have a Constraint Template. The Constraint Template defines the input parameters and the rego that defines and enforces a policy.

3. Create a Constraint Template that requires an `app` label.

```ctr:kubernetes
cat <<EOF | kubectl apply -f -
apiVersion: templates.gatekeeper.sh/v1
kind: ConstraintTemplate
metadata:
  name: k8srequiredlabels
spec:
  crd:
    spec:
      names:
        kind: K8sRequiredLabels
      validation:
        openAPIV3Schema:
          type: object
          properties:
            labels:
              type: array
              items:
                type: string
  targets:
    - target: admission.k8s.gatekeeper.sh
      rego: |
        package k8srequiredlabels

        violation[{"msg": msg, "details": {"missing_labels": missing}}] {
          provided := {label | input.review.object.metadata.labels[label]}
          required := {label | label := input.parameters.labels[_]}
          missing := required - provided
          count(missing) > 0
          msg := sprintf("you must provide labels: %v", [missing])
        }
EOF
```

Expected output:

```shell
constrainttemplate.templates.gatekeeper.sh/k8srequiredlabels created
```

4. Create a Constraint that enforces the ConstraintTemplate to require a label. Specify this policy for Namespaces with the label `owner`. Note this is a dry-run.

```ctr:kubernetes
cat <<EOF | kubectl apply -f -
apiVersion: constraints.gatekeeper.sh/v1beta1
kind: K8sRequiredLabels
metadata:
  name: ns-must-have-owner
spec:
  match:
    kinds:
      - apiGroups: [""]
        kinds: ["Namespace"]
  parameters:
    labels: ["owner"]
EOF
```

```shell
k8srequiredlabels.constraints.gatekeeper.sh/ns-must-have-owner created
```

5. Test the Constraint.

```ctr:kubernetes
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Namespace
metadata:
  name: new-namespace
  labels:
    owner: you
EOF
```

Expected output:

```shell
namespace/new-namespace created
```

6. Test the Constraint with a Namespace without an `owner` label.

```ctr:kubernetes
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Namespace
metadata:
  name: rejected-namespace
EOF
```

Expected output:

```shell
Error from server (Forbidden): error when creating "STDIN": admission webhook "validation.gatekeeper.sh" denied the request: [ns-must-have-owner] you must provide labels: {"owner"}
```