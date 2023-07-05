## Configure all key-value pairs in a ConfigMap as container environment variables

- We will update the `configmap.yaml` as follows:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: demo-config
data:
  greeting: Hola
```

- We will update the `deployment.yaml` as follows:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: demo
  template:
    metadata:
      labels:
        app: demo
    spec:
      containers:
        - name:  demo
          image: clarusway/demo:hello-config-env
          ports:
            - containerPort: 8888
          env:
            - name: GREETING
              valueFrom:
                configMapKeyRef:
                  name: demo-config
                  key: greeting
```

Note that this time, we are not placing the `GREETING` as run arguments. This time we will inject this variable as `environment variable`.