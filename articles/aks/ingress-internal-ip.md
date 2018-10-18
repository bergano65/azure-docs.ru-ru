---
title: Создание контроллера входящего трафика для внутренней сети в Службе Azure Kubernetes (AKS)
description: Сведения об установке и настройке контроллера входящего трафика NGINX для внутренней частной сети в кластере Службы Azure Kubernetes (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 08/30/2018
ms.author: iainfou
ms.openlocfilehash: 76ad9d21f7b328e7f201d227cdd9ace51c62a3fd
ms.sourcegitcommit: af9cb4c4d9aaa1fbe4901af4fc3e49ef2c4e8d5e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44356037"
---
# <a name="create-an-ingress-controller-to-an-internal-virtual-network-in-azure-kubernetes-service-aks"></a>Создание контроллера входящего трафика для внутренней виртуальной сети в Службе Azure Kubernetes (AKS)

Контроллер входящего трафика является программным компонентом, предоставляющим для служб Kubernetes обратный прокси-сервер, настраиваемую маршрутизацию трафика и обработку подключений TLS для последующей передачи. Ресурсы входящего трафика Kubernetes используются при настройке правил входящего трафика и маршрутов для отдельных служб Kubernetes. Применяя контроллер и правила входящего трафика, для маршрутизации трафика в несколько служб в кластере Kubernetes можно использовать один IP-адрес.

В этой статье описывается, как установить и настроить [контроллер входящего трафика NGINX][nginx-ingress] в кластере Службы Azure Kubernetes (AKS). Контроллер входящего трафика настроен для внутренней частной виртуальной сети и IP-адреса. Внешний доступ запрещен. Затем в кластере AKS запускаются два приложения, каждое из которых доступно по одному IP-адресу.

Также можно:

- [Создать основной контроллер входящего трафика с внешним сетевым подключением.][aks-ingress-basic]
- [Включить надстройку маршрутизации приложений HTTP.][aks-http-app-routing]
- [Создать контроллер входящего трафика с динамическим общедоступным IP-адресом и настроить шифрование для автоматического создания TLS-сертификатов.][aks-ingress-tls]
- [Создать контроллер входящего трафика со статическим общедоступным IP-адресом и настроить шифрование для автоматического создания TLS-сертификатов.][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Перед началом работы

В этой статье для установки контроллера входящего трафика NGINX cert-manager и примера веб-приложения используется Helm. Поэтому необходимо инициализировать Helm в кластере AKS и использовать учетную запись службы для Tiller. Дополнительную информацию о настройке и использовании Helm см. в статье [Использование Helm со службой Azure Kubernetes][use-helm].

Для этой статьи требуется Azure CLI версии 2.0.41 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Создание контроллера входящего трафика

По умолчанию контроллер входящего трафика NGINX создается с динамическим общедоступным назначением IP-адресов. Общим требованием к конфигурации является использование внутренней частной сети и IP-адреса. Такой подход позволяет ограничить доступ к службам внутренними пользователями без внешнего доступа.

Создайте файл с именем *internal-ingress.yaml*, используя приведенный ниже пример файла манифеста. Этот пример назначает IP-адрес *10.240.0.42* ресурсу *loadBalancerIP*. Укажите собственный внутренний IP-адрес для использования с контроллером входящего трафика. Убедитесь, что этот IP-адрес не используется в вашей виртуальной сети.

```yaml
controller:
  service:
    loadBalancerIP: 10.240.0.42
    annotations:
      service.beta.kubernetes.io/azure-load-balancer-internal: "true"
```

Разверните диаграмму *nginx ingress* с помощью Helm. Чтобы использовать файл манифеста, созданный на предыдущем шаге, добавьте параметр `-f internal-ingress.yaml`:

> [!TIP]
> В следующем примере контроллер входящего трафика устанавливается в пространстве имен `kube-system`. При необходимости вы можете указать другое пространство имен для своей среды. Если в кластере AKS не включено RBAC, добавьте `--set rbac.create=false` в команду.

```console
helm install stable/nginx-ingress --namespace kube-system -f internal-ingress.yaml
```

При создании службы распределения нагрузки Kubernetes для контроллера входящего трафика NGINX назначается внутренний IP-адрес, как показано в следующем примере выходных данных.

```
$ kubectl get service -l app=nginx-ingress --namespace kube-system

NAME                                              TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
alternating-coral-nginx-ingress-controller        LoadBalancer   10.0.97.109   10.240.0.42   80:31507/TCP,443:30707/TCP   1m
alternating-coral-nginx-ingress-default-backend   ClusterIP      10.0.134.66   <none>        80/TCP                       1m
```

Так как правила входящего трафика не созданы, при переходе к внутреннему IP-адресу по умолчанию будет отображаться страница контроллера входящего трафика NGINX с ошибкой "404 — страница не найдена". В следующих шагах настраиваются правила входящего трафика.

## <a name="run-demo-applications"></a>Запуск демонстрационных версий приложений

Чтобы увидеть контроллер входящего трафика в действии, давайте запустим два демонстрационных приложения в кластере AKS. В этом примере для развертывания двух экземпляров простого приложения Hello World применяется Helm.

Чтобы установить примеры диаграмм Helm, добавьте репозиторий примеров Azure в свою среду Helm таким образом:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Создайте первую демонстрационную версию приложения из диаграммы Helm с помощью такой команды:

```console
helm install azure-samples/aks-helloworld
```

Установите второй экземпляр демонстрационной версии приложения. Укажите новый заголовок для второго экземпляра, чтобы оба приложения визуально отличались. Также задайте уникальное имя службы:

```console
helm install azure-samples/aks-helloworld --set title="AKS Ingress Demo" --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Создание маршрута для входящего трафика

Оба приложения запущены в кластере Kubernetes. Для маршрутизации трафика для каждого приложения создайте ресурс входящего трафика Kubernetes. Ресурс входящего трафика настраивает правила, по которым осуществляется маршрутизация трафика к одному из двух приложений.

В следующем примере трафик по адресу `http://10.240.0.42/` направляется в службу `aks-helloworld`. Трафик по адресу `http://10.240.0.42/hello-world-two` направляется в службу `ingress-demo`.

Создайте файл `hello-world-ingress.yaml` и скопируйте в него следующий пример кода YAML:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /
        backend:
          serviceName: aks-helloworld
          servicePort: 80
      - path: /hello-world-two
        backend:
          serviceName: ingress-demo
          servicePort: 80
```

Создайте ресурс входящего трафика, используя команду `kubectl apply -f hello-world-ingress.yaml`.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-controller"></a>Тестирование контроллера входящего трафика

Чтобы проверить маршруты для контроллера входящего трафика, перейдите в два приложения с помощью веб-клиента. При необходимости вы можете быстро проверить эту внутреннюю функцию системного модуля pod в кластере AKS. Создайте тестовый системный модуль pod и свяжите с ним сеанс терминала.

```console
kubectl run -it --rm aks-ingress-test --image=debian
```

Установите `curl` в модуле pod с помощью `apt-get`:

```console
apt-get update && apt-get install -y curl
```

Теперь получите адрес контроллера входящего трафика Kubernetes с помощью `curl`, например *http://10.240.0.42*. Укажите свой внутренний IP-адрес, указанный при развертывании контроллера входящего трафика на первом шаге, описанном в этой статье.

```console
curl -L http://10.240.0.42
```

Так как с этим адресом не было указано дополнительного пути, для контроллера входящего трафика по умолчанию указан маршрут */*. Первое демонстрационное приложение возвращается, как показано в следующем сокращенном примере выходных данных:

```
$ curl -L 10.240.0.42

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Теперь добавьте к адресу путь */hello-world-two*, например *http://10.240.0.42/hello-world-two*. Второе демонстрационное приложение с настраиваемым заголовком возвращается, как показано в следующем сокращенном примере выходных данных:

```
$ curl -L -k http://10.240.0.42/hello-world-two

<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="next-steps"></a>Дополнительная информация

В данной статье упоминаются некоторые внешние компоненты для AKS. Чтобы узнать больше об этих компонентах, см. следующие страницы проекта:

- [Интерфейс командной строки Helm][helm-cli]
- [Контроллер входящего трафика NGINX][nginx-ingress]

Также можно:

- [Создать основной контроллер входящего трафика с внешним сетевым подключением.][aks-ingress-basic]
- [Включить надстройку маршрутизации приложений HTTP.][aks-http-app-routing]
- [Создать контроллер входящего трафика с динамическим общедоступным IP-адресом и настроить шифрование для автоматического создания TLS-сертификатов.][aks-ingress-tls]
- [Создать контроллер входящего трафика со статическим общедоступным IP-адресом и настроить шифрование для автоматического создания TLS-сертификатов.][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm#install-helm-cli
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-basic]: ingress-basic.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
