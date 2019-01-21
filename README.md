## Helm dependency

/sonarqube$ helm dependency update
Hang tight while we grab the latest from your chart repositories...
...Unable to get an update from the "local" chart repository (http://127.0.0.1:8879/charts):
	Get http://127.0.0.1:8879/charts/index.yaml: dial tcp 127.0.0.1:8879: connect: connection refused
...Successfully got an update from the "incubator" chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈Happy Helming!⎈
Saving 1 charts
Downloading postgresql from repo https://kubernetes-charts.storage.googleapis.com/
Deleting outdated charts

### This command will download postgresql-0.8.3.tgz into /sonarqube/charts/

## Access the UI
$ kubectl get svc
NAME                           TYPE           CLUSTER-IP       EXTERNAL-IP    PORT(S)          AGE
sonarqube-postgresql           ClusterIP      10.105.33.70     <none>         5432/TCP         109s
sonarqube-sonarqube            LoadBalancer   10.96.147.179    10.10.40.101   9000:30230/TCP   109s

### Browser to 10.236.1.1:30230

## Further improvement: local-storage issue
Persistent volume may be created in a different node from PostgreSQL. This condition may incur the following error. The way I bypassed this error is creating directories in all nodes which is not a good solution. The better way is specifying the directory with node affinity.

$ kubectl describe pod sonarqube-postgresql-6765fd498b-b86rs
Events:
  Type     Reason          Age               From                   Message
  ----     ------          ----              ----                   -------
  Normal   Scheduled       82s               default-scheduler      Successfully assigned default/sonarqube-postgresql-6765fd498b-b86rs to k8s-worker-1
  Normal   Pulling         71s               kubelet, k8s-worker-1  pulling image "postgres:9.6.2"
  Normal   Pulled          51s               kubelet, k8s-worker-1  Successfully pulled image "postgres:9.6.2"
  Warning  Failed          6s (x5 over 51s)  kubelet, k8s-worker-1  Error: stat /home/ccma/data/postgresql-data: no such file or directory
  Normal   SandboxChanged  6s (x5 over 50s)  kubelet, k8s-worker-1  Pod sandbox changed, it will be killed and re-created.
  Normal   Pulled          6s (x4 over 39s)  kubelet, k8s-worker-1  Container image "postgres:9.6.2" already present on machine
