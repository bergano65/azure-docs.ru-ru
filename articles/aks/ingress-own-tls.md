---
title: Использование сертификатов TLS для входящих данных
titleSuffix: Azure Kubernetes Service
description: Сведения об установке и настройке контроллера входящего трафика NGINX, который использует собственные сертификаты в кластере Службы Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 05/24/2019
ms.openlocfilehash: 7cc0cbd3809446d67875abfd2f5508889b381f61
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82145398"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Создание контроллера входящего трафика HTTPS и использование собственных сертификатов TLS в Службе Azure Kubernetes (AKS)

Контроллер входящего трафика является программным компонентом, предоставляющим для служб Kubernetes обратный прокси-сервер, настраиваемую маршрутизацию трафика и обработку подключений TLS для последующей передачи. Ресурсы входящего трафика Kubernetes используются при настройке правил входящего трафика и маршрутов для отдельных служб Kubernetes. Применяя контроллер и правила входящего трафика, для маршрутизации трафика в несколько служб в кластере Kubernetes можно использовать один IP-адрес.

В этой статье описывается, как установить и настроить [контроллер входящего трафика NGINX][nginx-ingress] в кластере Службы Azure Kubernetes (AKS). С помощью этих инструкций вы создадите собственные сертификаты и секрет службы Kubernetes, чтобы использовать их на маршруте входящего трафика. Наконец, в кластере AKS запущено два приложения, каждое из которых доступно по одному IP-адресу.

Кроме того, вы можете сделать следующее:

- [Создать базовый контроллер входящего трафика с внешним сетевым подключением.][aks-ingress-basic]
- [Включить надстройку маршрутизации приложений HTTP.][aks-http-app-routing]
- [Создать контроллер входящего трафика, который использует внутреннюю, частную сети и IP-адрес.][aks-ingress-internal]
- Создать контроллер входящего трафика, использующий службу Let's Encrypt для автоматического создания сертификатов TLS [с динамическим общедоступным IP-адресом][aks-ingress-tls] или [со статическим общедоступным IP-адресом][aks-ingress-static-tls].

## <a name="before-you-begin"></a>Подготовка к работе

В этой статье используется [Helm 3][helm] для установки контроллера входящих данных nginx и примера веб-приложения. Поэтому необходимо инициализировать Helm в кластере AKS и использовать учетную запись службы для Tiller. Убедитесь, что вы используете последний выпуск Helm. Инструкции по обновлению см. в документации по [установке Helm][helm-install]. Дополнительные сведения о настройке и использовании Helm см. [в статье Установка приложений с помощью Helm в службе Kubernetes Azure (AKS)][use-helm].

В этой статье также предполагается, что вы используете Azure CLI версии 2.0.64 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Создание контроллера входящего трафика

Чтобы создать контроллер входящего трафика, установите *nginx ingress* с помощью `Helm`. Для обеспечения дополнительной избыточности развертываются две реплики контроллеров входящего трафика NGINX с использованием параметра `--set controller.replicaCount`. Чтобы максимально эффективно использовать реплики контроллера входящего трафика, убедитесь, что в кластере AKS используется несколько узлов.

Контроллер Ingress также необходимо запланировать на узле Linux. Узлы Windows Server (в настоящее время в предварительной версии в AKS) не должны запускать входной контроллер. Селектор узла указывается с помощью параметра `--set nodeSelector`, чтобы сообщить планировщику Kubernetes о необходимости запуска контроллера NGINX Ingress на узле под управлением Linux.

> [!TIP]
> В следующем примере создается пространство имен Kubernetes для входящих ресурсов с именем входящие *-Basic*. При необходимости укажите пространство имен для своей среды. Если в кластере AKS не включен RBAC, добавьте `--set rbac.create=false` к командам Helm.

> [!TIP]
> Если вы хотите включить [Сохранение IP-адреса источника клиента][client-source-ip] для запросов к контейнерам в кластере, `--set controller.service.externalTrafficPolicy=Local` добавьте команду Helm install. Исходный IP-адрес клиента хранится в заголовке запроса в разделе *X-forwardd-for*. При использовании контроллера входящего трафика с включенным сохранением IP-адресов источника клиента передача TLS не будет работать.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Use Helm to deploy an NGINX ingress controller
helm install stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Во время установки для контроллера входящего трафика создается общедоступный IP-адрес Azure. Этот IP-адрес является статическим только на время жизненного цикла контроллера входящего трафика. При удалении контроллера входящего трафика этот общедоступный IP-адрес теряется. Если создать дополнительный контроллер входящего трафика, ему назначается новый общедоступный IP-адрес. Чтобы сохранить общедоступный IP-адрес, создайте вместо этого [контроллер со статическим общедоступным IP-адресом][aks-ingress-static-tls].

Чтобы получить общедоступный IP-адрес, используйте команду `kubectl get service`. Назначение службе IP-адреса занимает несколько минут.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                          TYPE           CLUSTER-IP    EXTERNAL-IP    PORT(S)                      AGE
virulent-seal-nginx-ingress-controller        LoadBalancer   10.0.48.240   40.87.46.190   80:31159/TCP,443:30657/TCP   7m
virulent-seal-nginx-ingress-default-backend   ClusterIP      10.0.50.5     <none>         80/TCP                       7m
```

Запомните или запишите общедоступный IP-адрес, так как он потребуется на последнем этапе для тестирования развертывания.

Правила доступа еще не созданы. Если вы перейдете на общедоступный IP-адрес, для контроллера входящего трафика NGINX по умолчанию отобразится страница 404.

## <a name="generate-tls-certificates"></a>Создание сертификатов TLS

В рамках этой статьи мы создадим самозаверяющий сертификат с помощью `openssl`. Для рабочей среды следует запросить доверенный подписанный сертификат через поставщика или другой центр сертификации. Затем мы создадим *секрет* Kubernetes с помощью сертификата TLS и закрытого ключа, созданного OpenSSL.

В приведенном ниже примере создается 2048-разрядный сертификат RSA X509, действующий в течение 365 дней, с именем *aks-ingress-tls.crt*. Файл закрытого ключа называется *aks-ingress-tls.key*. Секрету TLS Kubernetes требуются оба этих файла.

В этой статье используется общее имя субъекта *demo.azure.com*. Его не нужно менять. Для рабочей среды укажите значения вашей организации для параметра `-subj`:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out aks-ingress-tls.crt \
    -keyout aks-ingress-tls.key \
    -subj "/CN=demo.azure.com/O=aks-ingress-tls"
```

## <a name="create-kubernetes-secret-for-the-tls-certificate"></a>Создание секрета Kubernetes для сертификата TLS

Чтобы разрешить Kubernetes использовать сертификат TLS и закрытый ключ для контроллера входящего трафика, требуется создать и применить секрет. Секрет определяется один раз. Далее он использует сертификат и файл ключа, созданные на предыдущем шаге. Вы ссылаетесь на этот ключ при определении маршрутов входящего трафика.

В приведенном ниже примере создается секрет с именем *aks-ingress-tls*:

```console
kubectl create secret tls aks-ingress-tls \
    --namespace ingress-basic \
    --key aks-ingress-tls.key \
    --cert aks-ingress-tls.crt
```

## <a name="run-demo-applications"></a>Запуск демонстрационных версий приложений

Контроллер входящего трафика и секрет с вашим сертификатом настроены. Теперь запустите две демонстрационные версии приложения в своем кластере AKS. В этом примере для развертывания двух экземпляров простого приложения Hello World применяется Helm.

Чтобы установить примеры диаграмм Helm, добавьте репозиторий примеров Azure в свою среду Helm таким образом:

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Создайте первую демонстрационную версию приложения из диаграммы Helm с помощью такой команды:

```console
helm install azure-samples/aks-helloworld --namespace ingress-basic
```

Установите второй экземпляр демонстрационной версии приложения. Укажите новый заголовок для второго экземпляра, чтобы оба приложения визуально отличались. Также задайте уникальное имя службы:

```console
helm install azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="ingress-demo"
```

## <a name="create-an-ingress-route"></a>Создание маршрута для входящего трафика

Теперь оба приложения запущены на кластере Kubernetes, несмотря на то что они настроены с помощью службы типа `ClusterIP`. Таким образом приложения не доступны через Интернет. Чтобы сделать их доступными, создайте ресурс входящего трафика Kubernetes. Ресурс входящего трафика настраивает правила, по которым осуществляется маршрутизация трафика к одному из двух приложений.

В следующем примере трафик по адресу `https://demo.azure.com/` направляется в службу `aks-helloworld`. Трафик по адресу `https://demo.azure.com/hello-world-two` направляется в службу `ingress-demo`. В этой статье не нужно менять имена демонстрационных узлов. Для рабочей среды используйте имена, указанные при подаче запроса на сертификат и его создании.

> [!TIP]
> Если имя узла, указанное в процессе запроса сертификата, не соответствует узлу, указанному в маршруте входящего трафика, то в контроллере входящих данных отображается предупреждение о *поддельном сертификате Kubernetes входящего контроллера* . Убедитесь в том, что имена сертификата и узла в маршруте входящего трафика совпадают.

В разделе *tls* указано, что в маршруте входящего трафика используется секрет с именем *aks-ingress-tls* для узла *demo.azure.com*. Напоминаем, что для рабочей среды следует указать адрес вашего узла.

Создайте файл `hello-world-ingress.yaml` и скопируйте в него следующий пример кода YAML:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo.azure.com
    secretName: aks-ingress-tls
  rules:
  - host: demo.azure.com
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /(.*)
      - backend:
          serviceName: ingress-demo
          servicePort: 80
        path: /hello-world-two(/|$)(.*)
```

Создайте ресурс входящего трафика, используя команду `kubectl apply -f hello-world-ingress.yaml`.

```
$ kubectl apply -f hello-world-ingress.yaml

ingress.extensions/hello-world-ingress created
```

## <a name="test-the-ingress-configuration"></a>Проверка конфигурации входящего трафика

Чтобы проверить сертификаты нашего несуществующего узла *demo.azure.com*, используйте `curl` и определите параметр *--resolve*. Этот параметр позволяет сопоставить имя *demo.azure.com* с общедоступным IP-адресом контроллера входящего трафика. Укажите общедоступный IP-адрес вашего контроллера входящего трафика, как показано в приведенном ниже примере.

```
curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com
```

С адресом не был предоставлен дополнительный путь, поэтому для контроллера входящих данных по умолчанию используется */* маршрут. Первое демонстрационное приложение возвращается, как показано в следующем сокращенном примере выходных данных:

```
$ curl -v -k --resolve demo.azure.com:443:40.87.46.190 https://demo.azure.com

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>Welcome to Azure Kubernetes Service (AKS)</title>
[...]
```

Параметр *-v* в команде `curl` позволяет вывести подробные сведения, в том числе данные полученного сертификата TLS. В выходных данных curl можно проверить, использовался ли собственный сертификат TLS. При использовании параметра *-k* по-прежнему загружается страница, несмотря на то что используется самозаверяющий сертификат. В приведенном ниже примере показано, что использован сертификат *issuer: CN=demo.azure.com; O=aks-ingress-tls*.

```
[...]
* Server certificate:
*  subject: CN=demo.azure.com; O=aks-ingress-tls
*  start date: Oct 22 22:13:54 2018 GMT
*  expire date: Oct 22 22:13:54 2019 GMT
*  issuer: CN=demo.azure.com; O=aks-ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
[...]
```

Теперь добавьте к адресу путь */hello-world-two*, например `https://demo.azure.com/hello-world-two`. Второе демонстрационное приложение с настраиваемым заголовком возвращается, как показано в следующем сокращенном примере выходных данных:

```
$ curl -v -k --resolve demo.azure.com:443:137.117.36.18 https://demo.azure.com/hello-world-two

[...]
<!DOCTYPE html>
<html xmlns="http://www.w3.org/1999/xhtml">
<head>
    <link rel="stylesheet" type="text/css" href="/static/default.css">
    <title>AKS Ingress Demo</title>
[...]
```

## <a name="clean-up-resources"></a>Очистка ресурсов

В этой статье для установки компонентов обработки входящего трафика, сертификатов и примеров приложений используется Helm. При развертывании диаграммы Helm создается несколько ресурсов Kubernetes. К ним относятся элементы pod, развертывания и службы. Чтобы очистить эти ресурсы, можно либо удалить весь пример пространства имен, либо отдельные ресурсы.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Удаление образца пространства имен и всех ресурсов

Чтобы удалить весь пример пространства имен, используйте `kubectl delete` команду и укажите имя пространства имен. Все ресурсы в пространстве имен удаляются.

```console
kubectl delete namespace ingress-basic
```

Затем удалите репозиторий Helm для приложения AKS Hello World:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Удаление ресурсов по отдельности

Кроме того, более детализированный подход заключается в удалении отдельных созданных ресурсов. Выведите список выпусков `helm list` Helm с помощью команды. Найдите диаграммы *nginx-ingress* и *aks-helloworld*, как показано в следующем примере выходных данных.

```
$ helm list

NAME            REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
virulent-seal   1           Tue Oct 23 16:37:24 2018    DEPLOYED    nginx-ingress-0.22.1    0.15.0      kube-system
billowing-guppy 1           Tue Oct 23 16:41:38 2018    DEPLOYED    aks-helloworld-0.1.0                default
listless-quokka 1           Tue Oct 23 16:41:30 2018    DEPLOYED    aks-helloworld-0.1.0                default
```

Удалить выпуски командой `helm delete`. В приведенном ниже примере удаляются развертывание контроллера входящего трафика NGINX и два примера приложений hello world для AKS.

```
$ helm delete virulent-seal billowing-guppy listless-quokka

release "virulent-seal" deleted
release "billowing-guppy" deleted
release "listless-quokka" deleted
```

Затем удалите репозиторий Helm, используемый для приложения hello world для AKS.

```console
helm repo remove azure-samples
```

Удалите маршрут входящего трафика, направлявший трафик в пример приложения.

```console
kubectl delete -f hello-world-ingress.yaml
```

Удалите секрет сертификата:

```console
kubectl delete secret aks-ingress-tls
```

Наконец, можно удалить само пространство имен. Используйте `kubectl delete` команду и укажите имя пространства имен:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Дальнейшие шаги

В данной статье упоминаются некоторые внешние компоненты для AKS. Чтобы узнать больше об этих компонентах, см. следующие страницы проекта:

- [Интерфейс командной строки Helm][helm-cli]
- [Контроллер входящего трафика NGINX][nginx-ingress]

Кроме того, вы можете сделать следующее:

- [Создать базовый контроллер входящего трафика с внешним сетевым подключением.][aks-ingress-basic]
- [Включить надстройку маршрутизации приложений HTTP.][aks-http-app-routing]
- [Создать контроллер входящего трафика, который использует внутреннюю, частную сети и IP-адрес.][aks-ingress-internal]
- Создать контроллер входящего трафика, использующий службу Let's Encrypt для автоматического создания сертификатов TLS [с динамическим общедоступным IP-адресом][aks-ingress-tls] или [со статическим общедоступным IP-адресом][aks-ingress-static-tls].

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
[helm]: https://helm.sh/
[helm-install]: https://docs.helm.sh/using_helm/#installing-helm

<!-- LINKS - internal -->
[use-helm]: kubernetes-helm.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-network-public-ip-create]: /cli/azure/network/public-ip#az-network-public-ip-create
[aks-ingress-internal]: ingress-internal-ip.md
[aks-ingress-static-tls]: ingress-static-ip.md
[aks-ingress-basic]: ingress-basic.md
[aks-http-app-routing]: http-application-routing.md
[aks-ingress-tls]: ingress-tls.md
[client-source-ip]: concepts-network.md#ingress-controllers
