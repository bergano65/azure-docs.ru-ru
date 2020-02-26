---
title: Создание базового контроллера входящего трафика в Службе Azure Kubernetes (AKS)
description: Узнайте, как установить и настроить базовый контроллер входящего трафика NGINX в кластере Службы Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 12/20/2019
ms.openlocfilehash: e37f7aa677be129aa9fe568880c53cc860947e30
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/25/2020
ms.locfileid: "77595643"
---
# <a name="create-an-ingress-controller-in-azure-kubernetes-service-aks"></a>Создание контроллера входящего трафика в Службе Azure Kubernetes (AKS)

Контроллер входящего трафика является программным компонентом, предоставляющим для служб Kubernetes обратный прокси-сервер, настраиваемую маршрутизацию трафика и обработку подключений TLS для последующей передачи. Ресурсы входящего трафика Kubernetes используются при настройке правил входящего трафика и маршрутов для отдельных служб Kubernetes. Применяя контроллер и правила входящего трафика, для маршрутизации трафика в несколько служб в кластере Kubernetes можно использовать один IP-адрес.

В этой статье показано, как развернуть контроллер входящего трафика [nginx][nginx-ingress] в кластере службы Kubernetes Azure (AKS). Затем в кластере AKS запускаются два приложения, каждое из которых доступно по одному IP-адресу.

Кроме того, вы можете сделать следующее:

- [Включение надстройки маршрутизации приложений HTTP][aks-http-app-routing]
- [Создание контроллера входящего трафика, использующего внутреннюю, частную сеть и IP-адрес][aks-ingress-internal]
- [Создание контроллера входящего трафика, использующего собственные сертификаты TLS][aks-ingress-own-tls]
- Создайте входной контроллер, который использует шифрование для автоматического создания сертификатов TLS [с динамическим общедоступным IP-адресом][aks-ingress-tls] или [со статическим общедоступным IP-адресом][aks-ingress-static-tls] .

## <a name="before-you-begin"></a>Перед началом

В этой статье для установки контроллера входящего трафика NGINX и примера веб-приложения используется Helm.

В этой статье также предполагается, что вы используете Azure CLI версии 2.0.64 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Создание контроллера входящего трафика

Чтобы создать входной контроллер, используйте Helm для установки *nginx-* in. Для обеспечения дополнительной избыточности развертываются две реплики контроллеров входящего трафика NGINX с использованием параметра `--set controller.replicaCount`. Чтобы максимально эффективно использовать реплики контроллера входящего трафика, убедитесь, что в кластере AKS используется несколько узлов.

Контроллер входящего трафика также необходимо запланировать на узле Linux. Узлы Windows Server (в настоящее время в предварительной версии в AKS) не должны запускать входной контроллер. Селектор узла указывается с помощью параметра `--set nodeSelector`, чтобы сообщить планировщику Kubernetes о необходимости запуска контроллера входящих данных NGINX на узле под управлением Linux.

> [!TIP]
> В следующем примере создается пространство имен Kubernetes для входящих ресурсов с именем входящие *-Basic*. При необходимости укажите пространство имен для своей среды.

> [!TIP]
> Если вы хотите включить [Сохранение IP-адреса источника клиента][client-source-ip] для запросов к контейнерам в кластере, добавьте `--set controller.service.externalTrafficPolicy=Local` в команду Helm install. Исходный IP-адрес клиента хранится в заголовке запроса в разделе *X-forwardd-for*. При использовании контроллера входящего трафика с включенным сохранением IP-адресов источника клиента передача данных по протоколу SSL не будет работать.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repository
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx-ingress stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

При создании службы распределения нагрузки Kubernetes входящего контроллера NGINX назначается динамический общедоступный IP-адрес, как показано в следующем примере выходных данных:

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                             TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)                      AGE
nginx-ingress-controller         LoadBalancer   10.0.61.144    EXTERNAL_IP   80:30386/TCP,443:32276/TCP   6m2s
nginx-ingress-default-backend    ClusterIP      10.0.192.145   <none>        80/TCP                       6m2s
```

Так как правила входящего трафика не созданы, при переходе к внутреннему IP-адресу по умолчанию будет отображаться страница контроллера входящего трафика NGINX с ошибкой "404 — страница не найдена". В следующих шагах настраиваются правила входящего трафика.

## <a name="run-demo-applications"></a>Запуск демонстрационных версий приложений

Чтобы увидеть контроллер входящего трафика в действии, давайте запустим два демонстрационных приложения в кластере AKS. В этом примере Helm используется для развертывания двух экземпляров простого приложения *Hello World* .

Чтобы установить примеры диаграмм Helm, добавьте репозиторий примеров Azure в свою среду Helm таким образом:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Создайте первую демонстрационную версию приложения из диаграммы Helm с помощью такой команды:

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Установите второй экземпляр демонстрационной версии приложения. Укажите новый заголовок для второго экземпляра, чтобы оба приложения визуально отличались. Также задайте уникальное имя службы:

```console
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>Создание маршрута для входящего трафика

Оба приложения запущены в кластере Kubernetes. Для маршрутизации трафика для каждого приложения создайте ресурс входящего трафика Kubernetes. Ресурс входящего трафика настраивает правила, по которым осуществляется маршрутизация трафика к одному из двух приложений.

В следующем примере трафик к *EXTERNAL_IP* направляется в службу с именем `aks-helloworld`. Трафик к *EXTERNAL_IP/Хелло-Ворлд-тво* направляется в службу `aks-helloworld-two`. Трафик к *EXTERNAL_IP/Статик* направляется в службу с именем `aks-helloworld` для статических ресурсов.

Создайте файл `hello-world-ingress.yaml` и скопируйте в него следующий пример кода YAML:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: aks-helloworld-two
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress-static
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/ssl-redirect: "false"
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
spec:
  rules:
  - http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /static(/|$)(.*)
```

Создайте ресурс входящего трафика, используя команду `kubectl apply -f hello-world-ingress.yaml`.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
ingress.extensions/hello-world-ingress-static created
```

## <a name="test-the-ingress-controller"></a>Тестирование контроллера входящего трафика

Чтобы проверить маршруты для контроллера входящего трафика, перейдите в два приложения. Откройте в веб-браузере IP-адрес контроллера входящего трафика NGINX, например *EXTERNAL_IP*. Первое демонстрационное приложение отображается в браузере, как показано на следующем изображении:

![Первое приложение, выполняющееся за контроллером входящего трафика](media/ingress-basic/app-one.png)

Теперь добавьте путь */Хелло-Ворлд-тво* к IP-адресу, например *EXTERNAL_IP/Хелло-Ворлд-тво*. Отобразится второе демонстрационное приложение с пользовательским заголовком:

![Второе приложение, выполняющееся за контроллером входящего трафика](media/ingress-basic/app-two.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

В этой статье для установки компонентов обработки входящего трафика, сертификатов и примеров приложений используется Helm. При развертывании диаграммы Helm создается несколько ресурсов Kubernetes. К ним относятся элементы pod, развертывания и службы. Чтобы очистить эти ресурсы, можно либо удалить весь пример пространства имен, либо отдельные ресурсы.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Удаление образца пространства имен и всех ресурсов

Чтобы удалить весь пример пространства имен, используйте команду `kubectl delete` и укажите имя пространства имен. Все ресурсы в пространстве имен удаляются.

```console
kubectl delete namespace ingress-basic
```

Затем удалите репозиторий Helm для приложения AKS Hello World:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Удаление ресурсов по отдельности

Кроме того, более детализированный подход заключается в удалении отдельных созданных ресурсов. Выведите список выпусков Helm с помощью команды `helm list`. Найдите диаграммы *nginx-ingress* и *aks-helloworld*, как показано в следующем примере выходных данных.

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-06 19:57:00.131576 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-06 19:57:10.971365 -0600 CST    deployed        aks-helloworld-0.1.0               
nginx-ingress           ingress-basic   1               2020-01-06 19:55:46.358275 -0600 CST    deployed        nginx-ingress-1.27.1    0.26.1  
```

Удалить выпуски командой `helm delete`. В следующем примере удаляются развертывание контроллера входящего трафика NGINX и два примера приложений hello world для AKS.

```
$ helm delete aks-helloworld aks-helloworld-two nginx-ingress --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "nginx-ingress" uninstalled
```

Затем удалите репозиторий Helm, используемый для приложения hello world для AKS.

```console
helm repo remove azure-samples
```

Удалите маршрут входящего трафика, направлявший трафик в пример приложения.

```console
kubectl delete -f hello-world-ingress.yaml
```

Наконец, можно удалить само пространство имен. Используйте команду `kubectl delete` и укажите имя пространства имен:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Дальнейшие действия

В данной статье упоминаются некоторые внешние компоненты для AKS. Чтобы узнать больше об этих компонентах, см. следующие страницы проекта:

- [Helm CLI][helm-cli]
- [Контроллер входящего трафика NGINX][nginx-ingress]

Кроме того, вы можете сделать следующее:

- [Включение надстройки маршрутизации приложений HTTP][aks-http-app-routing]
- [Создание контроллера входящего трафика, использующего внутреннюю, частную сеть и IP-адрес][aks-ingress-internal]
- [Создание контроллера входящего трафика, использующего собственные сертификаты TLS][aks-ingress-own-tls]
- Создайте входной контроллер, который использует шифрование для автоматического создания сертификатов TLS [с динамическим общедоступным IP-адресом][aks-ingress-tls] или [со статическим общедоступным IP-адресом][aks-ingress-static-tls] .

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-tls]: ingress-tls.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-own-tls]: ingress-own-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
