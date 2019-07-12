---
title: Использование собственных сертификатов TLS для входящего трафика с кластером Службы Azure Kubernetes (AKS)
description: Сведения об установке и настройке контроллера входящего трафика NGINX, который использует собственные сертификаты в кластере Службы Azure Kubernetes (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 2b30ade9971ede6f9544b618504033553392e9bd
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615437"
---
# <a name="create-an-https-ingress-controller-and-use-your-own-tls-certificates-on-azure-kubernetes-service-aks"></a>Создание контроллера входящего трафика HTTPS и использование собственных сертификатов TLS в Службе Azure Kubernetes (AKS)

Контроллер входящего трафика является программным компонентом, предоставляющим для служб Kubernetes обратный прокси-сервер, настраиваемую маршрутизацию трафика и обработку подключений TLS для последующей передачи. Ресурсы входящего трафика Kubernetes используются при настройке правил входящего трафика и маршрутов для отдельных служб Kubernetes. Применяя контроллер и правила входящего трафика, для маршрутизации трафика в несколько служб в кластере Kubernetes можно использовать один IP-адрес.

В этой статье показано, как развернуть [входящего контроллера NGINX][nginx-ingress] в кластер Azure Kubernetes Service (AKS). С помощью этих инструкций вы создадите собственные сертификаты и секрет службы Kubernetes, чтобы использовать их на маршруте входящего трафика. Наконец, в кластере AKS запущено два приложения, каждое из которых доступно по одному IP-адресу.

Также можно:

- [Создание основных входящего контроллера с помощью подключения к внешней сети][aks-ingress-basic]
- [Включение маршрутизации надстройки приложения HTTP][aks-http-app-routing]
- [Создайте входящий контроллер, используется внутренней частной сети и IP-адрес][aks-ingress-internal]
- Создайте входящий контроллер, использующий Let's Encrypt для автоматического создания TLS-сертификатами [динамический общедоступный IP-адрес][aks-ingress-tls] or [with a static public IP address][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Перед началом работы

В этой статье для установки контроллера входящего трафика NGINX и примера веб-приложения используется Helm. Поэтому необходимо инициализировать Helm в кластере AKS и использовать учетную запись службы для Tiller. Убедитесь, что вы используете последний выпуск Helm. Инструкции по обновлению см. в разделе [Helm установите документация][helm-install]. For more information on configuring and using Helm, see [Install applications with Helm in Azure Kubernetes Service (AKS)][use-helm].

Здесь также предполагается, что вы используете Azure CLI версии 2.0.64 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

## <a name="create-an-ingress-controller"></a>Создание контроллера входящего трафика

Чтобы создать контроллер входящего трафика, установите *nginx ingress* с помощью `Helm`. Для обеспечения дополнительной избыточности развертываются две реплики контроллеров входящего трафика NGINX с использованием параметра `--set controller.replicaCount`. Чтобы максимально эффективно использовать реплики контроллера входящего трафика, убедитесь, что в кластере AKS используется несколько узлов.

Входящий контроллер также должен быть запланирован на узле Linux. Узлы Windows Server (в настоящее время в предварительной версии в AKS) не следует выполнять контроллеру входящего трафика. Имя узла задается с помощью `--set nodeSelector` параметр, чтобы сообщить планировщик Kubernetes для выполнения входящего контроллера NGINX на узле под управлением Linux.

> [!TIP]
> В следующем примере создается пространство имен Kubernetes для входящего трафика ресурсов с именем *входящих данных basic*. Укажите необходимые пространства имен для конкретной среды. Если кластер AKS не включена RBAC, добавьте `--set rbac.create=false` командам Helm.

> [!TIP]
> Если вы хотите включить [сохранение IP-адрес исходного клиента][client-source-ip] для запросов к контейнерам в кластере, добавьте `--set controller.service.externalTrafficPolicy=Local` для Helm команды установки. Источник клиента, IP-адрес хранится в заголовке запроса в разделе *X-Forwarded-For*. При использовании входящего контроллера с помощью клиента исходный IP-адрес включенным режимом сохранения, сквозной SSL не будет работать.

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

Во время установки для контроллера входящего трафика создается общедоступный IP-адрес Azure. Этот IP-адрес является статическим только на время жизненного цикла контроллера входящего трафика. При удалении контроллера входящего трафика этот общедоступный IP-адрес теряется. Если создать дополнительный контроллер входящего трафика, ему назначается новый общедоступный IP-адрес. Если вы хотите сохранить использование общедоступного IP-адреса, можно вместо этого [создайте входящий контроллер статический общедоступный IP-адрес][aks-ingress-static-tls].

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
> Если имя узла, указанное в процессе запроса на сертификат (CN-имя), не соответствует имени узла, указанному в маршруте входящего трафика, в контроллере входящего трафика отображается *поддельный сертификат контроллера входящего трафика Kubernetes*. Убедитесь в том, что имена сертификата и узла в маршруте входящего трафика совпадают.

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

Так как с этим адресом не было указано дополнительного пути, для контроллера входящего трафика по умолчанию указан маршрут */* . Первое демонстрационное приложение возвращается, как показано в следующем сокращенном примере выходных данных:

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

Параметр *-v* в команде `curl` позволяет вывести подробные сведения, в том числе данные полученного сертификата TLS. В выходных данных curl можно проверить, использовался ли собственный сертификат TLS. При использовании параметра *-k* по-прежнему загружается страница, несмотря на то что используется самозаверяющий сертификат. В следующем примере показано, что использовался сертификат *issuer: CN=demo.azure.com; O=aks-ingress-tls*:

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

В этой статье для установки компонентов обработки входящего трафика, сертификатов и примеров приложений используется Helm. При развертывании диаграммы Helm создается несколько ресурсов Kubernetes. К ним относятся элементы pod, развертывания и службы. Для очистки этих ресурсов, можно либо удалить пространство имен пример целиком или отдельные ресурсы.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Удалить пространство имен примера и все ресурсы

Чтобы удалить пространство имен весь пример, используйте `kubectl delete` команду и укажите имя пространства имен. Будут удалены все ресурсы в пространстве имен.

```console
kubectl delete namespace ingress-basic
```

Удалите из репозитория Helm для приложения hello world AKS:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Удаление ресурсов по отдельности

Кроме того более детализированный подход — удалить отдельные ресурсы, созданные. Список Helm освобождает с `helm list` команды. Найдите диаграммы *nginx-ingress* и *aks-helloworld*, как показано в следующем примере выходных данных.

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

Удалите сертификат секрет:

```console
kubectl delete secret aks-ingress-tls
```

Наконец вы можете удалить сам пространства имен. Используйте `kubectl delete` команду и укажите имя пространства имен:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Следующие шаги

В данной статье упоминаются некоторые внешние компоненты для AKS. Чтобы узнать больше об этих компонентах, см. следующие страницы проекта:

- [Helm CLI][helm-cli]
- [Контроллер входящего трафика NGINX][nginx-ingress]

Также можно:

- [Создание основных входящего контроллера с помощью подключения к внешней сети][aks-ingress-basic]
- [Включение маршрутизации надстройки приложения HTTP][aks-http-app-routing]
- [Создайте входящий контроллер, используется внутренней частной сети и IP-адрес][aks-ingress-internal]
- Создайте входящий контроллер, использующий Let's Encrypt для автоматического создания TLS-сертификатами [динамический общедоступный IP-адрес][aks-ingress-tls] or [with a static public IP address][aks-ingress-static-tls]

<!-- LINKS - external -->
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[nginx-ingress]: https://github.com/kubernetes/ingress-nginx
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