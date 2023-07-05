### From a config file

- We will write the greeting key-value pair in a file in Norvegian and create the ConfigMap from this file.

kubectl create configmap demo-config --from-literal=greeting=Hola

```bash
echo "greeting: Hei" > config
```

Note that, the comman notation used in key-value pairs is to use `key= value` notation, but this is not an obligatory. The notation actualy depends on the applicaton implementation that will parse and use these files.

- Look at the other example files that look like below

```bash
ls 
game.properties
ui.properties

cat game.properties
enemies=aliens
lives=3
enemies.cheat=true
enemies.cheat.level=noGoodRotten
secret.code.passphrase=UUDDLRLRBABAS
secret.code.allowed=true
secret.code.lives=30

cat ui.properties
color.good=purple
color.bad=yellow
allow.textmode=true
how.nice.to.look=fairlyNice
```

- Let's create our configmap from `config` file.

```bash
kubectl create configmap demo-config --from-file=./config
```

- Check the content of the `configmap/demo-config`.

```json
kubectl get  configmap/demo-config -o json
{
    "apiVersion": "v1",
    "data": {
        "config": "greeting: Hei\n"
    },
    "kind": "ConfigMap",
    "metadata": {
        "creationTimestamp": "2021-10-06T13:04:14Z",
        "name": "demo-config",
        "namespace": "default",
        "resourceVersion": "40173",
        "uid": "8bbeed1d-d516-4db8-b50e-c0fdb7f4f0c2"
    }
}
```

We have modifed our application to read parameters from the file. So the `deployment` file changed as follows:

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
          image: clarusway/demo:hello-config-file
          ports:
            - containerPort: 8888
          volumeMounts:
          - mountPath: /config/
            name: demo-config-volume
            readOnly: true
      volumes:
      - name: demo-config-volume
        configMap:
          name: demo-config
          items:
          - key: config
            path: demo.yaml
```

- Volume and volume mounting are common ways to place config files inside a container. We are selecting `config` key from `demo-config` ConfigMap and put it inside the container at path `/config/` with the name `demo.yaml`.

- Apply and run all the configurations as follow:

```bash
kubectl apply -f deployment.yaml

kubectl get po
NAME                   READY   STATUS    RESTARTS   AGE
demo-77496d887-rhkww   1/1     Running   0          4s

kubectl apply -f service.yaml 

kubectl get svc -o wide
NAME           TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE   SELECTOR
demo-service   NodePort    10.108.181.197   <none>        80:30001/TCP   11s   app=demo
kubernetes     ClusterIP   10.96.0.1        <none>        443/TCP        14h   <none>

curl < worker-ip >:30001
Hei, Clarusway!


## Create and use ConfigMaps with `kubectl create configmap` command

There are three ways to create ConfigMaps using the `kubectl create configmap` command. Here are the options.

1. Use the contents of an entire directory with `kubectl create configmap my-config --from-file=./my/dir/path/`
   
2. Use the contents of a file or specific set of files with `kubectl create configmap my-config --from-file=./my/file.txt`
   
3. Use literal key-value pairs defined on the command line with `kubectl create configmap my-config --from-literal=key1=value1 --from-literal=key2=value2`