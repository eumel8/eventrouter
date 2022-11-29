# eventrouter

## overview
This Helm Chart installs [Kubernetes Eventrouter](https://github.com/eumel8/eventrouter)
and watch for events in the installed namespace.

The project is forked from Banzaicloud (vmware,openshift) and has the target for a namespaced
installation.  Look at the [Readme](https://github.com/eumel8/blob/master/README.md) for more
information.

## installation

via Helm Chart Repo (required helm 3.10+):

```bash
helm -n <namespace> upgrade -i <app_name> --version 1.0.0 oci://mtr.devops.telekom.de/caas/charts/eventrouter
```

## usage

via Banzaicloud Logging Operator. e.g. Elastic Search Backend:

```yaml
apiVersion: logging.banzaicloud.io/v1beta1
kind: Output
metadata:
  name: customer
spec:
  elasticsearch:
    buffer:
      flush_at_shutdown: true
      flush_interval: 10s
      flush_mode: interval
    host: customer.es.otc-ece.telekom.de
    index_name: customer-events
    password:
      valueFrom:
        secretKeyRef:
          key: password
          name: customer-eaas-secret
    port: 443
    scheme: https
    ssl_verify: true
    ssl_version: TLSv1_2
    user: customer_write
---
apiVersion: logging.banzaicloud.io/v1beta1
kind: Flow
metadata:
  name: customer
spec:
  match:
  - select:
      labels:
        app.kubernetes.io/instance: eventrouter
  globalOutputRefs: []
  localOutputRefs:
  - customer
---
apiVersion: v1
data:
  password: xxxx==
kind: Secret
metadata:
  name: customer-eaas-secret
type: Opaque
```

