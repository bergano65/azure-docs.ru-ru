---
title: Создание контроллера входящего трафика HTTPS с помощью кластера Службы Azure Kubernetes (AKS)
description: Узнайте, как установить и настроить контроллер входа NGINX, который использует Let's Encrypt для автоматического генерации сертификатов TLS в кластере Azure Kubernetes Service (AKS).
services: container-service
ms.topic: article
ms.date: 01/29/2020
ms.openlocfilehash: 6f497ee3edd5ee831c091a5a50629df81673acea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78191322"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Создание контроллера входящего трафика HTTPS в Службе Azure Kubernetes (AKS)

Контроллер входящего трафика является программным компонентом, предоставляющим для служб Kubernetes обратный прокси-сервер, настраиваемую маршрутизацию трафика и обработку подключений TLS для последующей передачи. Ресурсы входящего трафика Kubernetes используются при настройке правил входящего трафика и маршрутов для отдельных служб Kubernetes. Применяя контроллер и правила входящего трафика, для маршрутизации трафика в несколько служб в кластере Kubernetes можно использовать один IP-адрес.

В этой статье описывается, как установить и настроить [контроллер входящего трафика NGINX][nginx-ingress] в кластере Службы Azure Kubernetes (AKS). Для автоматического создания и настройки сертификатов [Let's Encrypt][lets-encrypt] используется проект [cert-manager][cert-manager]. Наконец, в кластере AKS запущено два приложения, каждое из которых доступно по одному IP-адресу.

Кроме того, вы можете сделать следующее:

- [Создать базовый контроллер входящего трафика с внешним сетевым подключением.][aks-ingress-basic]
- [Включить надстройку маршрутизации приложений HTTP.][aks-http-app-routing]
- [Создать контроллер входящего трафика, который использует внутреннюю, частную сети и IP-адрес.][aks-ingress-internal]
- [Создать контроллер входящего трафика, который использует ваши собственные сертификаты TLS][aks-ingress-own-tls]
- [Создать контроллер входящего трафика, который использует службу Let's Encrypt для автоматического создания сертификатов TLS со статическим общедоступным IP-адресом][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Перед началом

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, обратитесь к этому краткому руководству по работе с AKS [с помощью Azure CLI][aks-quickstart-cli] или [портала Azure][aks-quickstart-portal].

В этой статье также предполагается, что у вас есть [пользовательский домен][custom-domain] с [зоной DNS][dns-zone] в той же группе ресурсов, что и кластер AKS.

В этой статье для установки контроллера входящего трафика NGINX cert-manager и примера веб-приложения используется Helm. Убедитесь, что вы используете последний выпуск Helm. Для инструкций по [обновлению][helm-install]см. Для получения дополнительной информации о настройке и использовании Helm [см.][use-helm]

В этой статье также требуется, чтобы вы запускали версию Azure CLI 2.0.64 или позже. Чтобы узнать версию, выполните команду `az --version`. Если вам нужно установить или обновить, [см.][azure-cli-install]

## <a name="create-an-ingress-controller"></a>Создание контроллера входящего трафика

Для создания контроллера входа `helm` используйте команду для установки *nginx-ingress.* Для обеспечения дополнительной избыточности развертываются две реплики контроллеров входящего трафика NGINX с использованием параметра `--set controller.replicaCount`. Чтобы максимально эффективно использовать реплики контроллера входящего трафика, убедитесь, что в кластере AKS используется несколько узлов.

Контроллер Ingress также необходимо запланировать на узле Linux. Узлы Windows Server (в настоящее время в предварительном просмотре в AKS) не должны запускать контроллер входа. Селектор узла указывается с помощью параметра `--set nodeSelector`, чтобы сообщить планировщику Kubernetes о необходимости запуска контроллера NGINX Ingress на узле под управлением Linux.

> [!TIP]
> Следующий пример создает пространство имен Kubernetes для ресурсов входа, названных *ingress-basic.* Укажите пространство имен для собственной среды по мере необходимости.

> [!TIP]
> Если вы хотите включить [сохранение исходного кода клиента][client-source-ip] для запросов в контейнеры в кластере, добавьте `--set controller.service.externalTrafficPolicy=Local` команду установки Helm. IP-адрес клиента хранится в заголовке запроса под *X-Forwarded-For.* При использовании контроллера входа с включенным сохранением ip-кода клиента проход SSL не будет работать.

```console
# Create a namespace for your ingress resources
kubectl create namespace ingress-basic

# Add the official stable repo
helm repo add stable https://kubernetes-charts.storage.googleapis.com/

# Use Helm to deploy an NGINX ingress controller
helm install nginx stable/nginx-ingress \
    --namespace ingress-basic \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

Во время установки для контроллера входящего трафика создается общедоступный IP-адрес Azure. Этот IP-адрес является статическим только на время жизненного цикла контроллера входящего трафика. При удалении контроллера входящего трафика этот общедоступный IP-адрес теряется. Если создать дополнительный контроллер входящего трафика, ему назначается новый общедоступный IP-адрес. Чтобы сохранить общедоступный IP-адрес, создайте вместо этого [контроллер со статическим общедоступным IP-адресом][aks-ingress-static-tls].

Чтобы получить общедоступный IP-адрес, используйте команду `kubectl get service`. Назначение службе IP-адреса занимает несколько минут.

```
$ kubectl get service -l app=nginx-ingress --namespace ingress-basic

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
nginx-ingress-controller                         LoadBalancer   10.0.182.160   MY_EXTERNAL_IP  80:30920/TCP,443:30426/TCP   20m
nginx-ingress-default-backend                    ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Правила доступа еще не созданы. Если вы перейдете на общедоступный IP-адрес, для контроллера входящего трафика NGINX по умолчанию отобразится страница 404.

## <a name="add-an-a-record-to-your-dns-zone"></a>Добавление записи в зону DNS

Добавьте *запись A* в свою зону DNS с внешним IP-адресом службы NGINX, используя [аз-сети dns, зафиксивающих запись дополнения.][az-network-dns-record-set-a-add-record]

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name * \
    --ipv4-address MY_EXTERNAL_IP
```

> [!NOTE]
> Дополнительно можно настроить IP-адрес контроллера, направляющийся на вскакивую, вместо пользовательского домена. Обратите внимание, что этот образец предназначен для оболочки Bash.
> 
> ```azurecli-interactive
> # Public IP address of your ingress controller
> IP="MY_EXTERNAL_IP"
> 
> # Name to associate with public IP address
> DNSNAME="demo-aks-ingress"
> 
> # Get the resource-id of the public ip
> PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)
> 
> # Update public ip address with DNS name
> az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
> 
> # Display the FQDN
> az network public-ip show --ids $PUBLICIPID --query "[dnsSettings.fqdn]" --output tsv
> ```

## <a name="install-cert-manager"></a>Установка cert-manager

Контроллер входящего трафика NGINX поддерживает обработку подключений TLS для последующей передачи. Получить и настроить сертификаты для HTTPS можно несколькими способами. В этой статье демонстрируется использование проекта [cert-manager][cert-manager], который обеспечивает автоматические функции создания сертификатов [Lets Encrypt][lets-encrypt] и управления ими.

Для установки контроллера сертификат-менеджера:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.13/deploy/manifests/00-crds.yaml

# Label the ingress-basic namespace to disable resource validation
kubectl label namespace ingress-basic cert-manager.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  cert-manager \
  --namespace ingress-basic \
  --version v0.13.0 \
  jetstack/cert-manager
```

Дополнительные сведения о конфигурации cert-manager см. в описании [проекта cert-manager][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Создание издателя кластера ЦС

Для выдачи сертификатов средству cert-manager нужен ресурс [Issuer][cert-manager-issuer] или [ClusterIssuer][cert-manager-cluster-issuer]. Эти ресурсы Kubernetes имеют аналогичную функциональность, однако `Issuer` работает в отдельном пространстве имен, а `ClusterIssuer` — во всех. Дополнительные сведения см. в документации по [издателю cert-manager][cert-manager-issuer].

Создайте издатель кластера, например `cluster-issuer.yaml`, используя приведенный ниже пример манифеста. Измените адрес электронной почты на действительный адрес вашей организации:

```yaml
apiVersion: cert-manager.io/v1alpha2
kind: ClusterIssuer
metadata:
  name: letsencrypt
spec:
  acme:
    server: https://acme-v02.api.letsencrypt.org/directory
    email: MY_EMAIL_ADDRESS
    privateKeySecretRef:
      name: letsencrypt
    solvers:
    - http01:
        ingress:
          class: nginx
```

Чтобы создать издатель, используйте команду `kubectl apply`.

```console
kubectl apply -f cluster-issuer.yaml --namespace ingress-basic
```

## <a name="run-demo-applications"></a>Запуск демонстрационных версий приложений

Контроллер входящего трафика и решение по управлению сертификатами настроены. Теперь запустите две демонстрационные версии приложения в своем кластере AKS. В этом примере шлем используется для развертывания двух экземпляров простого *приложения Hello world.*

Прежде чем установить диаграммы руля образца, добавьте репозиторий образцов Azure в среду руля.

```console
helm repo add azure-samples https://azure-samples.github.io/helm-charts/
```

Создайте демо-приложение под названием *aks-helloworld,* используя диаграмму *azure-samples/aks-helloworld* Helm.

```console
helm install aks-helloworld azure-samples/aks-helloworld --namespace ingress-basic
```

Создайте второй экземпляр демо-приложения под названием *aks-helloworld-two.* Укажите новое название и уникальное название службы, чтобы два приложения визуально отличались от использования *--set*.

```console
helm install aks-helloworld-two azure-samples/aks-helloworld \
    --namespace ingress-basic \
    --set title="AKS Ingress Demo" \
    --set serviceName="aks-helloworld-two"
```

## <a name="create-an-ingress-route"></a>Создание маршрута для входящего трафика

Оба приложения запущены в кластере Kubernetes. Однако они настроены с помощью службы типа `ClusterIP` и не доступны из Интернета. Чтобы сделать их доступными, создайте ресурс входящего трафика Kubernetes. Ресурс входящего трафика настраивает правила, по которым осуществляется маршрутизация трафика к одному из двух приложений.

В следующем примере трафик по адресу *привет-мир-вход. MY_CUSTOM_DOMAIN* направляется на службу *aks-helloworld.* Трафик по адресу *привет-мир-вход. MY_CUSTOM_DOMAIN/привет-мир-два* направляется на *службу aks-helloworld-two.* Трафик в *привет-мир-вход. MY_CUSTOM_DOMAIN/статичный* направляется в службу *aks-helloworld* для статических ресурсов.

Создайте файл `hello-world-ingress.yaml` с именем, используя ниже пример YAML. Обновите *узлы* и *узел*, указав DNS-имя, которое вы создали на предыдущем шаге.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$2
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
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
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /static/$2
    cert-manager.io/cluster-issuer: letsencrypt
spec:
  tls:
  - hosts:
    - hello-world-ingress.MY_CUSTOM_DOMAIN
    secretName: tls-secret
  rules:
  - host: hello-world-ingress.MY_CUSTOM_DOMAIN
    http:
      paths:
      - backend:
          serviceName: aks-helloworld
          servicePort: 80
        path: /static(/|$)(.*)
```

Создайте ресурс входящего трафика, используя команду `kubectl apply`.

```console
kubectl apply -f hello-world-ingress.yaml --namespace ingress-basic
```

## <a name="verify-a-certificate-object-has-been-created"></a>Проверка объекта сертификата была создана

Далее нужно создать ресурс сертификата. Этот ресурс сертификата определяет необходимый сертификат X.509. Для получения дополнительной [информации][cert-manager-certificates]см. Cert-manager автоматически создал объект сертификата для вас с помощью входа-шима, который автоматически развертывается с сертификат-менеджером с v0.2.2. Дополнительные сведения см. в [документации по ingress-shim][ingress-shim].

Чтобы убедиться, что сертификат был `kubectl get certificate --namespace ingress-basic` создан успешно, используйте команду и проверьте *READY* is *True,* что может занять несколько минут.

```
$ kubectl get certificate --namespace ingress-basic

NAME         READY   SECRET       AGE
tls-secret   True    tls-secret   11m
```

## <a name="test-the-ingress-configuration"></a>Проверка конфигурации входящего трафика

Откройте веб-браузер для *привет-мир-вход. MY_CUSTOM_DOMAIN* вашего контроллера Входа Kubernetes. Обратите внимание, что вы перенаправляете использование HTTPS и сертификат доверяют и демо-приложение отображается в веб-браузере. Добавьте */hello-world-two* путь и обратите внимание на второе демо-приложение с пользовательским названием показано.

## <a name="clean-up-resources"></a>Очистка ресурсов

В этой статье для установки компонентов обработки входящего трафика, сертификатов и примеров приложений используется Helm. При развертывании диаграммы Helm создается несколько ресурсов Kubernetes. К ним относятся элементы pod, развертывания и службы. Чтобы очистить эти ресурсы, можно удалить либо весь образец пространства имен, либо отдельные ресурсы.

### <a name="delete-the-sample-namespace-and-all-resources"></a>Удалить пространство имен образца и все ресурсы

Чтобы удалить весь образец пространства `kubectl delete` имен, используйте команду и укажите имя вашего имени. Все ресурсы в пространстве имен удаляются.

```console
kubectl delete namespace ingress-basic
```

Затем удалите репо Helm для приложения AKS hello world:

```console
helm repo remove azure-samples
```

### <a name="delete-resources-individually"></a>Удаление ресурсов по отдельности

Кроме того, более детальный подход заключается в удалении созданных отдельных ресурсов. Во-первых, удалите ресурсы кластерного эмитента:

```console
kubectl delete -f cluster-issuer.yaml --namespace ingress-basic
```

Перечислите релизы `helm list` Helm с командой. Найдите диаграммы *nginx-ingress* и *aks-helloworld*, как показано в следующем примере выходных данных.

```
$ helm list --namespace ingress-basic

NAME                    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART                   APP VERSION
aks-helloworld          ingress-basic   1               2020-01-15 10:24:32.054871 -0600 CST    deployed        aks-helloworld-0.1.0               
aks-helloworld-two      ingress-basic   1               2020-01-15 10:24:37.671667 -0600 CST    deployed        aks-helloworld-0.1.0               
cert-manager            ingress-basic   1               2020-01-15 10:23:36.515514 -0600 CST    deployed        cert-manager-v0.13.0    v0.13.0    
nginx                   ingress-basic   1               2020-01-15 10:09:45.982693 -0600 CST    deployed        nginx-ingress-1.29.1    0.27.0  
```

Удалить выпуски командой `helm delete`. В следующем примере удаляются развертывание контроллера входящего трафика NGINX и два примера приложений hello world для AKS.

```
$ helm uninstall aks-helloworld aks-helloworld-two cert-manager nginx --namespace ingress-basic

release "aks-helloworld" uninstalled
release "aks-helloworld-two" uninstalled
release "cert-manager" uninstalled
release "nginx" uninstalled
```

Затем удалите репозиторий Helm, используемый для приложения hello world для AKS.

```console
helm repo remove azure-samples
```

Удалите маршрут входящего трафика, направлявший трафик в пример приложения.

```console
kubectl delete -f hello-world-ingress.yaml --namespace ingress-basic
```

Наконец, можно удалить само пространство имен. Используйте `kubectl delete` команду и укажите имя в пространстве имен:

```console
kubectl delete namespace ingress-basic
```

## <a name="next-steps"></a>Дальнейшие действия

В данной статье упоминаются некоторые внешние компоненты для AKS. Чтобы узнать больше об этих компонентах, см. следующие страницы проекта:

- [Интерфейс командной строки Helm][helm-cli]
- [Контроллер входящего трафика NGINX][nginx-ingress]
- [cert-manager][cert-manager]

Кроме того, вы можете сделать следующее:

- [Создать базовый контроллер входящего трафика с внешним сетевым подключением.][aks-ingress-basic]
- [Включить надстройку маршрутизации приложений HTTP.][aks-http-app-routing]
- [Создать контроллер входящего трафика, который использует внутреннюю, частную сети и IP-адрес.][aks-ingress-internal]
- [Создать контроллер входящего трафика, который использует ваши собственные сертификаты TLS][aks-ingress-own-tls]
- [Создать контроллер входящего трафика, который использует службу Let's Encrypt для автоматического создания сертификатов TLS со статическим общедоступным IP-адресом][aks-ingress-static-tls]

<!-- LINKS - external -->
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../dns/dns-getstarted-cli.md
[helm-cli]: https://docs.microsoft.com/azure/aks/kubernetes-helm
[cert-manager]: https://github.com/jetstack/cert-manager
[cert-manager-certificates]: https://cert-manager.readthedocs.io/en/latest/reference/certificates.html
[ingress-shim]: https://docs.cert-manager.io/en/latest/tasks/issuing-certificates/ingress-shim.html
[cert-manager-cluster-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/clusterissuers.html
[cert-manager-issuer]: https://cert-manager.readthedocs.io/en/latest/reference/issuers.html
[lets-encrypt]: https://letsencrypt.org/
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
[aks-ingress-own-tls]: ingress-own-tls.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[client-source-ip]: concepts-network.md#ingress-controllers
[install-azure-cli]: /cli/azure/install-azure-cli
