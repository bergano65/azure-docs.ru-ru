---
title: Создание контроллера входящего трафика HTTPS с помощью кластера Службы Azure Kubernetes (AKS)
description: Узнайте, как установить и настроить входящий контроллер NGINX, использующий Let's Encrypt для автоматического создания сертификата TLS в кластер Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/24/2019
ms.author: mlearned
ms.openlocfilehash: 30f25ad9152bc722b54a834ef0ed037ac1666014
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615290"
---
# <a name="create-an-https-ingress-controller-on-azure-kubernetes-service-aks"></a>Создание контроллера входящего трафика HTTPS в Службе Azure Kubernetes (AKS)

Контроллер входящего трафика является программным компонентом, предоставляющим для служб Kubernetes обратный прокси-сервер, настраиваемую маршрутизацию трафика и обработку подключений TLS для последующей передачи. Ресурсы входящего трафика Kubernetes используются при настройке правил входящего трафика и маршрутов для отдельных служб Kubernetes. Применяя контроллер и правила входящего трафика, для маршрутизации трафика в несколько служб в кластере Kubernetes можно использовать один IP-адрес.

В этой статье показано, как развернуть [входящего контроллера NGINX][nginx-ingress] in an Azure Kubernetes Service (AKS) cluster. The [cert-manager][cert-manager] проект используется для автоматического создания и настройки [Let's Encrypt][позволяет шифровать]сертификаты. Наконец, в кластере AKS запущено два приложения, каждое из которых доступно по одному IP-адресу.

Также можно:

- [Создание основных входящего контроллера с помощью подключения к внешней сети][aks-ingress-basic]
- [Включение маршрутизации надстройки приложения HTTP][aks-http-app-routing]
- [Создайте входящий контроллер, используется внутренней частной сети и IP-адрес][aks-ingress-internal]
- [Создайте входящий контроллер, использующий собственные сертификаты TLS][aks-ingress-own-tls]
- [Создайте входящий контроллер, использующий Let's Encrypt для автоматического создания TLS-сертификатами с статический общедоступный IP-адрес][aks-ingress-static-tls]

## <a name="before-you-begin"></a>Перед началом работы

В этой статье предполагается, что у вас есть кластер AKS. Если вам нужен кластер AKS, см. в этом кратком руководстве AKS [с помощью Azure CLI][aks-quickstart-cli] or [using the Azure portal][aks-quickstart-portal].

В этой статье для установки контроллера входящего трафика NGINX cert-manager и примера веб-приложения используется Helm. Поэтому необходимо инициализировать Helm в кластере AKS и использовать учетную запись службы для Tiller. Убедитесь, что вы используете последний выпуск Helm. Инструкции по обновлению см. в разделе [Helm установите документация][helm-install]. For more information on configuring and using Helm, see [Install applications with Helm in Azure Kubernetes Service (AKS)][use-helm].

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

NAME                                             TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)                      AGE
billowing-kitten-nginx-ingress-controller        LoadBalancer   10.0.182.160   51.145.155.210  80:30920/TCP,443:30426/TCP   20m
billowing-kitten-nginx-ingress-default-backend   ClusterIP      10.0.255.77    <none>          80/TCP                       20m
```

Правила доступа еще не созданы. Если вы перейдете на общедоступный IP-адрес, для контроллера входящего трафика NGINX по умолчанию отобразится страница 404.

## <a name="configure-a-dns-name"></a>Настройка DNS-имени

Чтобы сертификаты HTTPS работали правильно, настройте для IP-адреса контроллера входящего трафика полное доменное имя. Укажите в этом скрипте IP-адрес контроллера входящего трафика и уникальное имя, которое вы хотите использовать в качестве полного доменного имени.

```azurecli-interactive
#!/bin/bash

# Public IP address of your ingress controller
IP="51.145.155.210"

# Name to associate with public IP address
DNSNAME="demo-aks-ingress"

# Get the resource-id of the public ip
PUBLICIPID=$(az network public-ip list --query "[?ipAddress!=null]|[?contains(ipAddress, '$IP')].[id]" --output tsv)

# Update public ip address with DNS name
az network public-ip update --ids $PUBLICIPID --dns-name $DNSNAME
```

Теперь к контроллеру входящего трафика можно получить доступ по полному доменному имени.

## <a name="install-cert-manager"></a>Установка cert-manager

Контроллер входящего трафика NGINX поддерживает обработку подключений TLS для последующей передачи. Получить и настроить сертификаты для HTTPS можно несколькими способами. В этой статье демонстрируется использование [диспетчера сертификатов][cert-manager] , which provides automatic [Lets Encrypt][lets-encrypt] сертификатов и создания функций.

> [!NOTE]
> В этой статье используется среда для Let's Encrypt `staging`. В производственных развертываниях используйте `letsencrypt-prod` и `https://acme-v02.api.letsencrypt.org/directory` в определениях ресурсов и при установке диаграммы Helm.

Чтобы установить контроллер cert-manager в кластере с поддержкой RBAC, используйте следующую команду `helm install`:

```console
# Install the CustomResourceDefinition resources separately
kubectl apply -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.8/deploy/manifests/00-crds.yaml

# Create the namespace for cert-manager
kubectl create namespace cert-manager

# Label the cert-manager namespace to disable resource validation
kubectl label namespace cert-manager certmanager.k8s.io/disable-validation=true

# Add the Jetstack Helm repository
helm repo add jetstack https://charts.jetstack.io

# Update your local Helm chart repository cache
helm repo update

# Install the cert-manager Helm chart
helm install \
  --name cert-manager \
  --namespace cert-manager \
  --version v0.8.0 \
  jetstack/cert-manager
```

Дополнительные сведения о конфигурации диспетчера сертификатов, см. в разделе [проекта диспетчера сертификатов][cert-manager].

## <a name="create-a-ca-cluster-issuer"></a>Создание издателя кластера ЦС

Прежде чем сертификаты могут выдаваться, диспетчера сертификатов требует [издателя][cert-manager-issuer] or [ClusterIssuer][cert-manager-cluster-issuer] ресурсов. Эти ресурсы Kubernetes имеют аналогичную функциональность, однако `Issuer` работает в отдельном пространстве имен, а `ClusterIssuer` — во всех. Дополнительные сведения см. в разделе [диспетчера сертификатов издателя][cert-manager-issuer] документации.

Создайте издатель кластера, например `cluster-issuer.yaml`, используя приведенный ниже пример манифеста. Измените адрес электронной почты на действительный адрес вашей организации:

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: ClusterIssuer
metadata:
  name: letsencrypt-staging
  namespace: ingress-basic
spec:
  acme:
    server: https://acme-staging-v02.api.letsencrypt.org/directory
    email: user@contoso.com
    privateKeySecretRef:
      name: letsencrypt-staging
    http01: {}
```

Чтобы создать издатель, используйте команду `kubectl apply -f cluster-issuer.yaml`.

```
$ kubectl apply -f cluster-issuer.yaml

clusterissuer.certmanager.k8s.io/letsencrypt-staging created
```

## <a name="run-demo-applications"></a>Запуск демонстрационных версий приложений

Контроллер входящего трафика и решение по управлению сертификатами настроены. Теперь запустите две демонстрационные версии приложения в своем кластере AKS. В этом примере для развертывания двух экземпляров простого приложения Hello World применяется Helm.

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

В следующем примере трафик по адресу `https://demo-aks-ingress.eastus.cloudapp.azure.com/` направляется в службу `aks-helloworld`. Трафик по адресу `https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two` направляется в службу `ingress-demo`. Обновите *узлы* и *узел*, указав DNS-имя, которое вы создали на предыдущем шаге.

Создайте файл `hello-world-ingress.yaml` и скопируйте в него следующий пример кода YAML:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: hello-world-ingress
  namespace: ingress-basic
  annotations:
    kubernetes.io/ingress.class: nginx
    certmanager.k8s.io/cluster-issuer: letsencrypt-staging
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo-aks-ingress.eastus.cloudapp.azure.com
    secretName: tls-secret
  rules:
  - host: demo-aks-ingress.eastus.cloudapp.azure.com
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

## <a name="create-a-certificate-object"></a>Создание объекта сертификата

Далее нужно создать ресурс сертификата. Этот ресурс сертификата определяет необходимый сертификат X.509. Дополнительные сведения см. в разделе [сертификатов диспетчера сертификатов][cert-manager-certificates].

cert-manager скорее всего автоматически создал объект сертификата с помощью ingress-shim — оболочки совместимости, которая автоматически развертывается с помощью cert-manager, начиная с версии 0.2.2. Дополнительные сведения см. в разделе [входящих данных оболочек документации][ingress-shim].

Чтобы убедиться, что сертификат был успешно создан, используйте команду `kubectl describe certificate tls-secret --namespace ingress-basic`.

Если сертификат был выдан, отобразятся выходные данные, аналогичные этим.
```
Type    Reason          Age   From          Message
----    ------          ----  ----          -------
  Normal  CreateOrder     11m   cert-manager  Created new ACME order, attempting validation...
  Normal  DomainVerified  10m   cert-manager  Domain "demo-aks-ingress.eastus.cloudapp.azure.com" verified with "http-01" validation
  Normal  IssueCert       10m   cert-manager  Issuing certificate...
  Normal  CertObtained    10m   cert-manager  Obtained certificate from ACME server
  Normal  CertIssued      10m   cert-manager  Certificate issued successfully
```

Если вам нужно создать дополнительный ресурс сертификата, вы можете это сделать с помощью приведенного ниже примера манифеста. Обновите *dnsNames* и *домены*, указав для них DNS-имя, которое вы создали на предыдущем шаге. Если вы используете только внутренний контроллер входящего трафика, укажите внутреннее имя DNS для службы.

```yaml
apiVersion: certmanager.k8s.io/v1alpha1
kind: Certificate
metadata:
  name: tls-secret
  namespace: ingress-basic
spec:
  secretName: tls-secret-staging
  dnsNames:
  - demo-aks-ingress.eastus.cloudapp.azure.com
  acme:
    config:
    - http01:
        ingressClass: nginx
      domains:
      - demo-aks-ingress.eastus.cloudapp.azure.com
  issuerRef:
    name: letsencrypt-staging
    kind: ClusterIssuer
```

Чтобы создать ресурс сертификата, используйте команду `kubectl apply -f certificates.yaml`.

```
$ kubectl apply -f certificates.yaml

certificate.certmanager.k8s.io/tls-secret-staging created
```

## <a name="test-the-ingress-configuration"></a>Проверка конфигурации входящего трафика

Откройте в браузере адрес с полным доменным именем вашего контроллера входящего трафика Kubernetes, например *https://demo-aks-ingress.eastus.cloudapp.azure.com* .

Поскольку в этих примерах используется `letsencrypt-staging`, браузер не доверяет выданному сертификату SSL. Чтобы продолжить работу с приложением, примите предупреждение. Информация о сертификате свидетельствует о том, что этот сертификат *Fake LE Intermediate X1* выдан Let's Encrypt. Этот поддельный сертификат указывает на то, что `cert-manager` правильно обработал запрос и получил сертификат от поставщика.

![Промежуточный сертификат Let's Encrypt](media/ingress/staging-certificate.png)

Когда вы меняете настройки Let's Encrypt, чтобы использовать `prod` вместо `staging`, применяется доверенный сертификат, выданный Let's Encrypt, как показано в следующем примере:

![Сертификат Let's Encrypt](media/ingress/certificate.png)

В браузере отображается демонстрационная версия приложения:

![Первый пример приложения](media/ingress/app-one.png)

Теперь добавьте к FQDN путь */hello-world-two*, например *https://demo-aks-ingress.eastus.cloudapp.azure.com/hello-world-two* . Отобразится второе демонстрационное приложение с пользовательским заголовком:

![Второй пример приложения](media/ingress/app-two.png)

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

Кроме того более детализированный подход — удалить отдельные ресурсы, созданные. Во-первых удалите ресурсы сертификата:

```console
kubectl delete -f certificates.yaml
kubectl delete -f cluster-issuer.yaml
```

Теперь получите список выпусков Helm с помощью команды `helm list`. Найдите диаграммы *nginx-ingress*, *cert-manager* и *aks-helloworld*, как показано в следующем примере выходных данных.

```
$ helm list

NAME                    REVISION    UPDATED                     STATUS      CHART                   APP VERSION NAMESPACE
billowing-kitten        1           Wed Mar  6 19:37:43 2019    DEPLOYED    nginx-ingress-1.3.1     0.22.0      kube-system
loitering-waterbuffalo  1           Wed Mar  6 20:25:01 2019    DEPLOYED    cert-manager-v0.6.6     v0.6.2      kube-system
flabby-deer             1           Wed Mar  6 20:27:54 2019    DEPLOYED    aks-helloworld-0.1.0                default
linting-echidna         1           Wed Mar  6 20:27:59 2019    DEPLOYED    aks-helloworld-0.1.0                default
```

Удалить выпуски командой `helm delete`. В следующем примере удаляются развертывание контроллера входящего трафика NGINX, диспетчер сертификатов и два примера приложений hello world для AKS.

```
$ helm delete billowing-kitten loitering-waterbuffalo flabby-deer linting-echidna

release "billowing-kitten" deleted
release "loitering-waterbuffalo" deleted
release "flabby-deer" deleted
release "linting-echidna" deleted
```

Затем удалите репозиторий Helm, используемый для приложения hello world для AKS.

```console
helm repo remove azure-samples
```

Удаление самого пространства имен. Используйте `kubectl delete` команду и укажите имя пространства имен:

```console
kubectl delete namespace ingress-basic
```

Наконец, удалите маршрут входящего трафика, направлявший трафик в пример приложения.

```console
kubectl delete -f hello-world-ingress.yaml
```

## <a name="next-steps"></a>Следующие шаги

В данной статье упоминаются некоторые внешние компоненты для AKS. Чтобы узнать больше об этих компонентах, см. следующие страницы проекта:

- [Helm CLI][helm-cli]
- [Контроллер входящего трафика NGINX][nginx-ingress]
- [cert-manager][cert-manager]

Также можно:

- [Создание основных входящего контроллера с помощью подключения к внешней сети][aks-ingress-basic]
- [Включение маршрутизации надстройки приложения HTTP][aks-http-app-routing]
- [Создайте входящий контроллер, используется внутренней частной сети и IP-адрес][aks-ingress-internal]
- [Создайте входящий контроллер, использующий собственные сертификаты TLS][aks-ingress-own-tls]
- [Создайте входящий контроллер, использующий Let's Encrypt для автоматического создания TLS-сертификатами с статический общедоступный IP-адрес][aks-ingress-static-tls]

<!-- LINKS - external -->
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