# maksim-se_platform
maksim-se Platform repository

## Домашнее задание №1 (занятие 3)

### Рабочее окружение
* oracle virtualbox v5
* kubectl + автодополнение
* minikube

###  что выполнено:
1. Установлен minikube:

![minikube](https://i.ibb.co/TB5Vd9C/hw1-screen01.png)

2. Запуск, проверка конфигурации и статуса:
```bash
minikube start
```
```bash
kubectl config view
```

![kubectl config view](https://i.ibb.co/vdGSxp9/Screenshot-from-2019-07-21-23-57-16.png)

```bash
kubectl cluster-info
```

![kubectl cluster-info](https://i.ibb.co/PNj92df/Screenshot-from-2019-07-21-23-58-14.png)

3. Включение dashboard `minikube addons enable dashboard`

4. Подключаемся к minikube и удаляем все поды:

```bash
minikube ssh
docker rm -f $(docker ps -a -q)
```

Поды восстанавливаются.

### kubectl
```bash
# Показать все поды в NS **kube-system**
kubectl get pods -n kube-system 
# Удалить системные контейнеры 
kubectl delete pod --all -n kube-system
# Проверка статуса кластера
kubectl get componentstatuses
#or
kubectl get cs
```

![kubectl config view](https://i.ibb.co/LJFCjyg/Screenshot-from-2019-07-22-00-16-20.png)

### Задание

> Разберитесь почему все pod в namespace kube-system восстановились после удаления.

	* kube-apiserver, kube-scheduler, kube-controller-manager, ectd - это статик-поды, управляются напрямую из kubelet  согласно манифестам
	* сoredns - управляется деплойментом
	* kube-proxy - демонсет, управляемый соответствующим контролером.

### Docker image
1. Сборка образа: docker build -t maks123/otus-nginx .
2. Загрузка образа в репозиторий: docker push maks123/otus-nginx
3. Запуск контейнера в minikube: kubectl apply -f web-pod.yaml
4. Проверка пода: kubectl get pod
5. Проброс порта пода наружу: kubectl port-forward --address 0.0.0.0 pod/web 8000:8000



![kubectl config view](https://i.ibb.co/jJdH0s5/Screenshot-from-2019-07-22-00-59-38.png)

## Домашнее задание №2 (занятие 5)

### Выполнено ДЗ №2

- [X] Основное ДЗ

###  В процессе выполнено:
 - task01
   - создали SA bob с ролью admin в рамках кластера
   - создали SA dave без доступа к кластеру
 - task02
   - создан namespace `prometheus`
   - создан ServiceAccount `carol` с доступом в `prometheus`
   - создан ClusterRole `prom-monitor` c доступом [ 'get', 'list', 'watch' ]
   - ClusterRole `prom-monitor` выдана для всех ServiceAccount в `prometheus`
 - task03
   - создан namespace `dev`
   - создан ServiceAccount `jane` с доступом в `dev`
   - ClusterRole `admin` для `dev` выдана `jane`
   - создан ServiceAccount `ken` с доступом в `dev`
   - CLusterRole `view-ken` выдана `ken`

### Как запустить проект:
 - В корне проекта выполнить `kubectl apply -f kubernetes-security --recursive`

### PR checklist:
 - [X] Выставлен label с номером домашнего задания
 - [X] Лектор добавлен в Assignees

## Домашнее задание №3

### Выполнено ДЗ №3
 - добавлены readinessProbe и livenessProbe в манифест kubernetes-intro/web-pod.yml 
 - создан манифест deployment web-deploy.yaml для пода kubernetes-intro/web-pod.yml 
 - добавил стратегию обновления
 - добавлен манифест Service с ClusterIP web-svc-cip.yaml
 - включен ipvs для kube-proxy
 - установлен MetalLB, выполнил настройку с помощью ConfigMap metallb-config.yaml
 - настроен маршрут в кластер kubernetes, проверил доступность веб-сервера в браузере;
 - прокинут DNS через MetalLB, задание со *
 - установил и настроил ingress-nginx;
 - создан манифест для Headless-сервиса web-svc-headless.yaml;
 - Создан манифест Ingress web-ingress.yaml для доступа к подам через Headless-сервис.
 - прокинут kubernetes-dashboard через MetalLB, задание со *

## Домашнее задание №4

### Выполнено ДЗ №4
 - развернут Minio (StatefulSet)
 - развернут service для доступа к Minio
 - проверена работа Minio
 
 ### задание со *
 - в файле minio-super-secrets.yaml описаны учетные данные.
 - данные записаны в minio-statefulset.yaml в секции env

## Домашнее задание №5

### Выполнено ДЗ №5
 - создан кластер с поддержкой snapshots
 ```bash
 kind create cluster --config cluster/cluster.yml --wait 300s
 ```
 - установлен CSI-драйвер
 ```bash
 git clone https://github.com/kubernetes-csi/csi-driver-host-path.git

./csi-driver-host-path/deploy/kubernetes-1.15/deploy-hostpath.sh
 ```
  - cоздан StorageClass, pvc и pod
```bash
kubectl apply -f 01-csi-strg-class.yml

kubectl apply -f 02-csi-pvc.yml

kubectl apply -f 03-csi-pod-with-pv.yml
```
 - проверка результата:
```bash
kubectl get pvc

kubectl get pv

kubectl get pods
```
## Домашнее задание №6

### kubectl debug
 - Установил kubectl-debug по инструкции
 - Попробовал выполнить трассировку
 ```bash 
bash-5.0# strace -c -p1
strace: test_ptrace_get_syscall_info: PTRACE_TRACEME: Operation not permitted
strace: attach: ptrace(PTRACE_ATTACH, 1): Operation not permitted
 ```
 - Для устранения ошибки изменил версию образа докер на latest - strace/agent_daemonset.yml
 - Применить изменения `kubectl apply -f strace/agent_daemonset.yml
 - Проверил трассировку
```bash
bash-5.0# strace -c -p1
strace: Process 1 attached
Handling connection for 10048
```
### iptables-tailer
 - Установил kube-iptables-tailer и необходимые для него сервис аккаунт, роль и биндинг
 - Установил тестовое приложение netperf-operator
 - Добавил сетевую потилику calico
 - Посмотрел логи calico на воркер-ноде
 - Создал DaemonSet iptables-tailer.yaml и создали SA iptables-tailer-sa.yaml.
 - Повторил тесты. Событий с ошибками нет.

## Домашнее задание №6

### Выполнено ДЗ №6
Создаем кластер кубернетес в minikube
 - [X] основное задание
 - [ ] задание со *

## В процессе сделано:
 - Создан оператор CRD с MySQL
 - Создан контейнер и запакован в образ

## Как запустить
# Создаем CRD + CR
```
kubectl apply -f kubernetes-operators/deploy/crd.yml 
kubectl apply -f kubernetes-operators/deploy/cr.yml 
```
# Проверяем результат
```
kubectl get crd

NAME                   CREATED AT
mysqls.otus.homework   2019-12-08T11:50:26Z

kubectl get mysqls.otus.homework
NAME             AGE
mysql-instance   21s

kubectl describe mysqls.otus.homework mysql-instance
Name:         mysql-instance
Namespace:    default
Labels:       <none>
Annotations:  kubectl.kubernetes.io/last-applied-configuration:
                {"apiVersion":"otus.homework/v1","kind":"MySQL","metadata":{"annotations":{},"name":"mysql-instance","namespace":"default"},"spec":{"datab...
API Version:  otus.homework/v1
Kind:         MySQL
Metadata:
  Creation Timestamp:  2019-12-08T11:50:34Z
  Generation:          1
  Resource Version:    806
  Self Link:           /apis/otus.homework/v1/namespaces/default/mysqls/mysql-instance
  UID:                 ed5b5797-8bcb-4772-a4ee-3ffe3de7ca3b
Spec:
  Database:      otus-database
  Image:         mysql:5.7
  Password:      otuspassword
  storage_size:  1Gi
Events:          <none>
```

# Собираем образ и пушим его в докерхаб
```
docker build . -t maks123/mysql-operator:v0.1
docker push maks123/mysql-operator:v0.1
```
# Применяем манифесты
```
kubectl apply -f kubernetes-operators/deploy/service-account.yml
kubectl apply -f kubernetes-operators/deploy/role.yml
kubectl apply -f kubernetes-operators/deploy/ClusterRoleBinding.yml
kubectl apply -f kubernetes-operators/deploy/deploy-operator.yml
```
## Как проверить
# Заполняем данные
```
export MYSQLPOD=$(kubectl get pods -l app=mysql-instance -o jsonpath="{.items[*].metadata.name}")
kubectl exec -it $MYSQLPOD -- mysql -u root -potuspassword -e "CREATE TABLE test ( id smallint unsigned not null auto_increment, name varchar(20) not null, constraint pk_example primary key (id) );" otus-database
kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "INSERT INTO test ( id, name ) VALUES ( null, 'some data' );" otus-database
kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "INSERT INTO test ( id, name ) VALUES ( null, 'some data-2' );" otus-database
```

# Смотрим содержание таблицы
```
kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "select * from test;" otus-database
mysql: [Warning] Using a password on the command line interface can be insecure.
+----+-------------+
| id | name        |
+----+-------------+
|  1 | some data   |
|  2 | some data-2 |
+----+-------------+
```

```
kubectl delete mysqls.otus.homework mysql-instance

kubectl get jobs
NAME                        COMPLETIONS   DURATION   AGE
backup-mysql-instance-job   1/1           2s         2m7s
```
```
kubectl apply -f ./deploy/cr.yml
export MYSQLPOD=$(kubectl get pods -l app=mysql-instance -o jsonpath="{.items[*].metadata.name}")

kubectl get jobs
NAME                         COMPLETIONS   DURATION   AGE
backup-mysql-instance-job    1/1           2s         3m27s
restore-mysql-instance-job   1/1           45s        56s

kubectl exec -it $MYSQLPOD -- mysql -potuspassword -e "select * from test;" otus-database
mysql: [Warning] Using a password on the command line interface can be insecure.
+----+-------------+
| id | name        |
+----+-------------+
|  1 | some data   |
|  2 | some data-2 |
+----+-------------+
```

## Завершаем работу

# Удаление:
```
kubectl delete mysqls.otus.homework mysql-instance
kubectl delete deployments.apps mysql-instance
kubectl delete pvc mysql-instance-pvc 
kubectl delete pv mysql-instance-pv
kubectl delete svc mysql-instance
```

## Домашнее задание № 10
### В процессе сделано:
 - Инсталляция с Helm2 c tiller
 - Инсталляция с Helm2 с helm-tiller
 - Helm3
 - jsonnet
 - kastomize

### Helm2 + tiller
```bash
kubectl apply -f kubernetes-templating/cert-manager/01-tiller-cert-manager-rb.yml
helm init --service-account=tiller
helm version
```
### Ingress
```bash
helm upgrade --install nginx-ingress stable/nginx-ingress --wait --namespace=nginx-ingress --version=1.11.1
```
### Certmanager
```bash
kubectl apply -f kubernetes-templating/cert-manager/01-tiller-cert-manager-rb.yml
helm init --tiller-namespace cert-manager --service-account tiller-cert-manager
```
### Chartmuseum
```bash
kubectl get service -n nginx-ingress
helm plugin install https://github.com/rimusz/helm-tiller
helm tiller run helm upgrade --install chartmuseum stable/chartmuseum --wait --namespace=chartmuseum --version=2.3.2 -f chartmuseum/values.yml
helm list
helm tiller run helm list
helm delete --purge chartmuseum
export HELM_TILLER_STORAGE=configmap
helm upgrade --install chartmuseum stable/chartmuseum --wait --namespace=chartmuseum --version=2.3.2 -f kubernetes-templating/chartmuseum/values.yaml
```
### Helm3
```bash
helm3 upgrade --install harbor harbor/harbor --wait \
--namespace=harbor \
--version=1.1.2 \
-f kubernetes-templating/harbor/values.yaml
```
### Socks-shop
```bash
helm upgrade --install socks-shop kubernetes-templating/socks-shop --wait --atomic
```

### kubecfg
```bash
kubecfg show services.jsonnet
kubecfg update services.jsonnet
```
### kustomize
```bash
kubectl apply -k kubernetes-templating/kustomize/overlays/socks-shop-prod
```

