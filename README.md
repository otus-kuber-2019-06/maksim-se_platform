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

###  что выполнено:
1. task01
* создали SA bob с ролью admin в рамках кластера
* создали SA dave без доступа к кластеру
