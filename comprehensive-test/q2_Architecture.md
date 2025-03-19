
- 前情提要
  很抱歉
因為我只知道另一個IaC-ansible,所以無法用terraform佈署,所以底下是嘗試問chatgpt協助完成的ansible playbook
這是我看得懂的yaml. 如果未來有機會可以接觸到雲端我很也很想要嘗試terraform deploy

- mysql-statefulset.yaml
`mysql-wrirer`只用來寫資料
`mysql-reader`只用來讀資料
`StatefulSet`控制Pod順序與穩定的網域名稱
```yaml
apiVersion: v1
kind: Service
metadata:
  name: mysql-writer
  namespace: asiayo
spec:
  selector:
    app: mysql
    role: writer
  ports:
    - name: mysql
      port: 3306
  clusterIP: None

---
apiVersion: v1
kind: Service
metadata:
  name: mysql-reader
  namespace: asiayo
spec:
  selector:
    app: mysql
    role: reader
  ports:
    - name: mysql
      port: 3306
  clusterIP: None

---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: mysql
  namespace: asiayo
spec:
  serviceName: "mysql"
  replicas: 2
  selector:
    matchLabels:
      app: mysql
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - name: mysql
        image: mysql:8.0
        ports:
        - containerPort: 3306
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: "password"
        - name: MYSQL_REPLICATION_USER
          value: "repl"
        - name: MYSQL_REPLICATION_PASSWORD
          value: "replpassword"
        volumeMounts:
        - name: mysql-storage
          mountPath: /var/lib/mysql
  volumeClaimTemplates:
  - metadata:
      name: mysql-storage
    spec:
      accessModes: [ "ReadWriteOnce" ]
      resources:
        requests:
          storage: 5Gi
```

- app-deployment.yaml
`MYSQL_WRITE_HOST`指向`mysql-writer`
`MYSQL_READ_HOST`指向`mysql-reader`

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp
  namespace: asiayo
spec:
  replicas: 3
  selector:
    matchLabels:
      app: myapp
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - name: myapp
        image: myapp-image:latest
        env:
        - name: MYSQL_WRITE_HOST
          value: "mysql-writer"
        - name: MYSQL_READ_HOST
          value: "mysql-reader"
        ports:
        - containerPort: 80
```
- svc.yaml
`myapp-service`讓`Ingress`可以存取app
```yaml
apiVersion: v1
kind: Service
metadata:
  name: myapp-service
  namespace: asiayo
spec:
  selector:
    app: myapp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
  type: ClusterIP
```

- ingress.yaml
使用`Ingress Controller`（Nginx, ALB）
指向`myapp-service`
提供asiay.com的對外訪問

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: myapp-ingress
  namespace: asiayo
  annotations:
    kubernetes.io/ingress.class: "nginx"
spec:
  rules:
  - host: asiayo.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: myapp-service
            port:
              number: 80
```

- pv-pvc.yaml

持久性存儲,pvc與pv `bonding`

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: mysql-pv
spec:
  capacity:
    storage: 10Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  storageClassName: gp3-storage
  hostPath:
    path: "/mnt/data"

---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-pvc
  namespace: asiayo
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 5Gi
  storageClassName: gp3-storage
```








