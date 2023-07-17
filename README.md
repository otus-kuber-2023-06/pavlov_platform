# Выполнено ДЗ № 1

- [X] Разберитесь почему все pod в namespace kube-system восстановились
  после удаления.
  
Pod-ы в namespace kube-system, управляются напрямую kubelet.
  Описание данных подов находится в `/etc/kubernetes/manifests`
```  docker@minikube:~$ ls -l  /etc/kubernetes/manifests
  total 16
  -rw------- 1 root root 2470 Jul 11 13:24 etcd.yaml
  -rw------- 1 root root 4076 Jul 11 13:24 kube-apiserver.yaml
  -rw------- 1 root root 3395 Jul 11 13:24 kube-controller-manager.yaml
  -rw------- 1 root root 1441 Jul 11 13:24 kube-scheduler.yaml
```

Pod coredns описан в деплойменте неймспейса kube-system.

```
kubectl describe deployments coredns -n kube-system
Name:                   coredns
Namespace:              kube-system
CreationTimestamp:      Tue, 11 Jul 2023 16:24:14 +0300
Labels:                 k8s-app=kube-dns
Annotations:            deployment.kubernetes.io/revision: 1
Selector:               k8s-app=kube-dns
Replicas:               1 desired | 1 updated | 1 total | 1 available | 0 unavailable
```

- [X] Задание со *
в логах видим не хватает заданных переменных среды
```
kubectl logs frontend
{"message":"Tracing disabled.","severity":"info","timestamp":"2023-07-12T06:44:35.50891482Z"}
  {"message":"Profiling disabled.","severity":"info","timestamp":"2023-07-12T06:44:35.509176399Z"}
  panic: environment variable "PRODUCT_CATALOG_SERVICE_ADDR" not set

goroutine 1 [running]:
main.mustMapEnv(0xc000000000, {0xc07754, 0x1c})
/src/main.go:208 +0xb9
main.main()
/src/main.go:124 +0x5be
```

в src/frontend/main.go видим список обязательных переменных среды
```
	mustMapEnv(&svc.productCatalogSvcAddr, "PRODUCT_CATALOG_SERVICE_ADDR")
	mustMapEnv(&svc.currencySvcAddr, "CURRENCY_SERVICE_ADDR")
	mustMapEnv(&svc.cartSvcAddr, "CART_SERVICE_ADDR")
	mustMapEnv(&svc.recommendationSvcAddr, "RECOMMENDATION_SERVICE_ADDR")
	mustMapEnv(&svc.checkoutSvcAddr, "CHECKOUT_SERVICE_ADDR")
	mustMapEnv(&svc.shippingSvcAddr, "SHIPPING_SERVICE_ADDR")
	mustMapEnv(&svc.adSvcAddr, "AD_SERVICE_ADDR")
```
Добавил  недостающие переменные среды в манифест.

Значения взяты из файла [kubernetes-manifests/frontend.yaml](https://github.com/GoogleCloudPlatform/microservices-demo/blob/main/kubernetes-manifests/frontend.yaml)

## В процессе сделано:
- Основное ДЗ
1. создал и загрузил [чуть переделанный образ nginx](https://hub.docker.com/r/minized/kuber-hw-nginx)
   `docker build  ./ -t minized/kuber-hw-nginx:0.1`
2. написал манифест pod с label **web**  _web-pod.yaml_

- Задание со *
1. На основе [Doсkerfile](https://github.com/GoogleCloudPlatform/microservices-demo/blob/main/src/frontend/Dockerfile)
из репозитория [GoogleCloudPlatform/microservices-demo](https://github.com/GoogleCloudPlatform/microservices-demo) 
был создан образ [hipstor-shop](https://hub.docker.com/r/minized/hipster-frontend)
и размещен в публичном репозитории hub.docker.com
2. Находясь в директории **.\kubernetes-intro** командой
`kubectl run frontend --image minized/hipster-frontend:v0.0.1 --restart=Never --dry-run=client -o yaml > frontend-pod.yaml`
был создан манифест **frontend-pod.yaml** на основе образа описанного в пункте выше.
3. `kubectl get pods` убедился в наличии ошибки
4. Командой `kubectl logs frontend` посмотрел причины ошибки при запуске
5. Создал манифест **frontend-pod-healthy.yaml** в директории **./kubernetes-intro**
6. В манифест **frontend-pod-healthy.yaml** добавлены недостающие переменные

## Как запустить проект:
### Основное ДЗ 
- запустить команду `kubectl apply -f web-pod.yaml`
в директории **./kubernetes-intro**
### Задание со *
- запустить команду `kubectl apply -f frontend-pod-healthy.yaml`
в директории **./kubernetes-intro**

## Как проверить работоспособность:
### Основное ДЗ
- `kubectl port-forward --address 127.0.0.1 pod/web 8080:8000`
- Перейти по ссылке http://localhost:8080
### Задание со *
- `kubectl port-forward --address 127.0.0.1 pod/frontend 8082:8080`
- Перейти по ссылке http://localhost:8082, получим 500 ошибку
## PR checklist:
- [X] Выставлен label с темой домашнего задания