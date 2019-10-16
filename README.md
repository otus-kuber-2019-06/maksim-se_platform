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
 - доступы кодированы в base64
 - кодированные данные записаны в minio-statefulset.yaml в секции env