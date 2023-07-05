
Burada "deployment.yaml" dosyası içerisinde yer alan "clarusway/demo:hello-config-file" iamj dosyasından bir container oluşturulur. Dha sonra bu imaj dosyası ile oluşan container içerisinde "/" dizini altında volume adı "demo-config-volume" olacak şekilde bir "config" dosyası oluşturulur ve bizim volume "path" olur.   Bu path yani config dosyası içersine bağlamak istediğimiz volume dosyası "demo-config" isimli bir yaml dosyasıdır. Bu demo-config dosyası içerisinde "config" adındaki key değeri " greeting: Buongiorno" değerlerini "demo.yaml" adında bir klasör oluşturarak container içerisindeki "config" klasörü altına kayıt eder.

Checkout the result with: 
```bash
curl localhost:30001

-----------------------------------------------
deployment.yaml
-----------------------------------------------
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
-----------------------------------------------------
configmap.yaml
-----------------------------------------------------
apiVersion: v1
kind: ConfigMap
metadata:
  name: demo-config
data:
  config: |
    greeting: Buongiorno