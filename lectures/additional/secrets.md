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

# Secrets
<!-- _class: lead -->

Securing Sensitive Data in K8s

## Need

Various services in K8s will need sensitive information
&rarr; _eg_ credentials, authentication tokens

Storing them in configuration files in git often means too much access

K8s **Secrets** store sensitive information in the cluster
Values only accessible to resources in the same namespace

## Creating a Secret

Secret resources are not tracked with YAML manifests
<small>That would defeat the point</small>

They are usually created via the command line:
```bash
kubectl create secret -n namespace generic my-secret-name \
  --from-literal=key1=value1 --from-literal=key2=value2
```

Secrets can contain multiple key/value pairs

## Accessing Secrets

Secrets can be injected into containers as environmental variables:

```yaml
spec:
  containers:
    - # ...
      env:
        - name: SECRET_TOKEN
          valueFrom:
            secretKeyRef:
              name: my-secret-name  # Name of secret
              key: key1             # Key within secret
```

## Limitations

Secrets are stored unencrypted by default in _etcd_
$\Rightarrow$ Enable encryption at rest

All users and service accounts have access to Secrets' values
$\Rightarrow$ Use RBAC to limit access to Secrets

$\Rightarrow$ Consider using an external secrets vault along with the [External Secrets Operator](https://external-secrets.io/latest/)
