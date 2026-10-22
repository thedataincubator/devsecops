---
marp: true
theme: gaia
paginate: true
headingDivider: 2
---

<style>
  :root {
    /* Slide background, code foreground
       Second color is used for class: invert */
    --color-background: light-dark(#f9f9f9, #0288d1);
  }
  .highlighted-line {
    display: block;
    background-color: #ffaa0040;
    margin: 0 -6px;
    padding: 0 6px;
  }

  section.col2 {
    columns: 2;
    display: block;

    h2 {
      column-span: all;
    }
    h3, h4 {
    break-before: column;
    }
  }

  section.pedagogy {
    --color-background: #f9b0b0;
    --color-foreground: #645a45;
  }
</style>

# NetworkPolicies
<!-- _class: lead -->

Restricting In-cluster Network Traffic

## Need

By default, all network traffic within a cluster is allowed

When a **NetworkPolicy** is a applied to a pod, change to _default-deny_
&rarr; Traffic is blocked unless specifically allowed

Helps block:
- Badly-behaved workloads within the cluster
- Attacker who gain access to a pod in the cluster

## Creating a Network Policy

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: policyname
  namespace: ns    # Applies to pods in this namespace
spec:
  podSelector: {}  # If empty, applies to all pods in namespace.
  policyTypes:     # Otherwise, select which pods get this policy.
  - Ingress   # Can restrict ingress (traffic into the pod),
  - Egress    # egress (traffic out of the pod), or both
  # ...
```

## Ingress Rules

```yaml
spec
  ingress:
  - from:    # A single rule to allow traffic
    - namespaceSelector:
        matchLabels:
          kubernetes.io/metadata.name: my-ns  # Allow traffic only from
    ports:                                    # from pods in my-ns
    - protocol: TCP  # Allow only TCP traffic (as opposed to UDP)
      port: 2000     # Allow traffic only on ports 2000-2004
      endPort: 2004  # endPort can be omitted for only a single port
```

All other traffic into these pods will be blocked

## Egress Rules

```yaml
  egress:
    - ports:            # First rule:
      - port: 53        # Allow outbound traffic to anywhere on port 53
        protocol: UDP   # (This generally needed for DNS)
      - port: 53
        protocol: TCP
    - to:                   # Second rule
      - podSelector:        # Allow outbound traffic to ...
          matchLabels:
            app: app-name   # ... pods with the label app=app-name ...
      ports:
        - port: 12345       # ... on port 12345.
          protocol: TCP
```
