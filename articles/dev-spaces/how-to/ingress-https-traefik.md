---
title: Использование пользовательского контроллера входящих данных траефик и настройка HTTPS
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 08/13/2019
ms.topic: conceptual
description: Узнайте, как настроить Azure Dev Spaces для использования пользовательского контроллера входящих данных траефик и настройки HTTPS с помощью этого контроллера входящего трафика.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: c015fe8e7108f07d66d2464c4f8b6287e8f54446
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73582321"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Использование пользовательского контроллера входящих данных траефик и настройка HTTPS

В этой статье показано, как настроить Azure Dev Spaces для использования пользовательского контроллера входящего трафика траефик. В этой статье также показано, как настроить этот настраиваемый контроллер входящего трафика для использования протокола HTTPS.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если ее нет, можно создать [бесплатную учетную запись][azure-account-create].
* [Установленный Azure CLI][az-cli].
* [Кластер Azure Kubernetes Service (AKS) с включенным Azure dev Spaces][qs-cli].
* [kubectl][kubectl] установлен.
* [Установленный Helm 2.13 или более поздней версии][helm-installed].
* [Личный домен][custom-domain] с [зоной DNS][dns-zone] в той же группе ресурсов, что и кластер AKS.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Настройка пользовательского контроллера входящего трафика траефик

Подключитесь к кластеру с помощью [kubectl][kubectl], клиента командной строки Kubernetes. Чтобы настроить `kubectl` на подключение к кластеру Kubernetes, выполните команду [az aks get-credentials][az-aks-get-credentials]. Эта команда скачивает учетные данные и настраивает интерфейс командной строки Kubernetes для их использования.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Проверьте подключение к кластеру, выполнив команду [kubectl get][kubectl-get], чтобы просмотреть список узлов кластера.

```console
$ kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Создайте пространство имен Kubernetes для контроллера входящих данных траефик и установите его с помощью `helm`.

```console
kubectl create ns traefik
helm init --wait
helm install stable/traefik --name traefik --namespace traefik --set kubernetes.ingressClass=traefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true
```

Получите IP-адрес службы траефик входящего контроллера с помощью [kubectl Get][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

В примере выходных данных показаны IP-адреса всех служб в пространстве имен *траефик* .

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Добавьте запись *A* в зону DNS с внешним IP-адресом службы траефик с помощью команды [AZ Network DNS запись-Set A-Record][az-network-dns-record-set-a-add-record].

```console
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

В приведенном выше примере в зону DNS *MY_CUSTOM_DOMAIN* добавляется запись *A* .

В этой статье на примере [приложения Azure Dev Spaces для аренды велосипедов](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) вы изучите применение Azure Dev Spaces. Клонируйте приложение с GitHub и перейдите в его каталог:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Откройте [Values. YAML][values-yaml] и замените все экземпляры *< REPLACE_ME_WITH_HOST_SUFFIX >* на *траефик. MY_CUSTOM_DOMAIN* , используя домен для *MY_CUSTOM_DOMAIN*. Также замените *kubernetes.IO/Ingress.class: траефик-аздс # dev Spaces, относящийся* к *kubernetes.IO/Ingress.class: Траефик # Custom входной*вход. Ниже приведен пример обновленного файла `values.yaml`.

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
```

Сохраните изменения и закройте файл.

Разверните пример приложения с помощью `helm install`.

```console
helm install -n bikesharing . --dep-up --namespace dev --atomic
```

В приведенном выше примере пример приложения развертывается в пространстве имен *dev* .

Выберите пространство *разработки* с помощью примера приложения, используя `azds space select` и отобразите URL-адреса для доступа к образцу приложения с помощью `azds list-uris`.

```console
azds space select -n dev
azds list-uris
```

В приведенных ниже выходных данных показаны примеры URL-адресов из `azds list-uris`.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Перейдите к службе *bikesharingweb* по общедоступному URL-адресу, который вам предоставила команда `azds list-uris`. В примере выше для службы *bikesharingweb* используется общедоступный URL-адрес `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

Используйте команду `azds space select`, чтобы создать дочернее пространство в разделе *dev* и вывести список URL-адресов для доступа к дочернему пространству разработки.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

В приведенных ниже выходных данных показаны примеры URL-адресов из `azds list-uris` для доступа к образцу приложения в дочерней области разработки *azureuser1* .

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Перейдите к службе *бикешарингвеб* в пространстве дочернего пространства разработки *azureuser1* , открыв общедоступный URL-адрес из команды `azds list-uris`. В приведенном выше примере общедоступный URL-адрес службы *бикешарингвеб* в пространстве дочернего разработчика *azureuser1* `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>Настройка контроллера входящего трафика траефик для использования протокола HTTPS

Создайте файл `dev-spaces/samples/BikeSharingApp/traefik-values.yaml`, как показано ниже. Обновите значение *электронной почты* , используя собственный адрес электронной почты, который будет использоваться для создания сертификата с шифрованием.

```yaml
fullnameOverride: traefik
replicas: 1
cpuLimit: 400m
memoryRequest: 200Mi
memoryLimit: 500Mi
externalTrafficPolicy: Local
kubernetes:
  ingressClass: traefik
  ingressEndpoint:
    useDefaultPublishedService: true
dashboard:
  enabled: false
debug:
  enabled: false
accessLogs:
  enabled: true
  fields:
    defaultMode: keep
    headers:
      defaultMode: keep
      names:
        Authorization: redact
acme:
  enabled: true
  email: "someone@example.com"
  staging: false
  challengeType: tls-alpn-01
ssl:
  enabled: true
  enforced: true
  permanentRedirect: true
  tlsMinVersion: VersionTLS12
  cipherSuites:
    - TLS_ECDHE_ECDSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_ECDSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256
    - TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384
    - TLS_RSA_WITH_AES_128_GCM_SHA256
    - TLS_RSA_WITH_AES_256_GCM_SHA384
    - TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256
```

Обновите службу *траефик* , используя `helm repo update` и включив созданный файл *траефик-Values. YAML* .

```console
cd ..
helm upgrade traefik stable/traefik --namespace traefik --values traefik-values.yaml
```

Приведенная выше команда запускает новую версию службы траефик, используя значения из *траефик-Values. YAML* и удаляя предыдущую службу. Служба траефик также создает TLS-сертификат, используя шифрование и применяя перенаправление сетевого трафика для использования протокола HTTPS.

> [!NOTE]
> Для запуска новой версии службы траефик может потребоваться несколько минут. Ход выполнения можно проверить с помощью `kubectl get pods --namespace traefik --watch`.

Перейдите к образцу приложения в дочернем пространстве *dev/azureuser1* и обратите внимание, что вы перенаправлялись на использование протокола HTTPS. Также обратите внимание, что страница загружается, но в браузере отображаются некоторые ошибки. При открытии консоли браузера отображается ошибка, связанная с HTTPS-страницей, пытающейся загрузить ресурсы HTTP. Например:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Чтобы устранить эту ошибку, обновите [бикешарингвеб/аздс. YAML][azds-yaml] , чтобы использовать *траефик* для *kubernetes.IO/Ingress.class* , и личный домен для *$ (хостсуффикс)* . Например:

```yaml
...
    ingress:
      annotations:
        kubernetes.io/ingress.class: traefik
      hosts:
      # This expands to [space.s.][rootSpace.]bikesharingweb.<random suffix>.<region>.azds.io
      - $(spacePrefix)$(rootSpacePrefix)bikesharingweb.traefik.MY_CUSTOM_DOMAIN
...
```

Обновите [бикешарингвеб/Package. JSON][package-json] с зависимостью для пакета *URL-адреса* .

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Обновите метод *жетапихостасинк* в [бикешарингвеб/Pages/helps. js][helpers-js] , чтобы использовать HTTPS:

```javascript
...
    getApiHostAsync: async function() {
        const apiRequest = await fetch('/api/host');
        const data = await apiRequest.json();
        
        var urlapi = require('url');
        var url = urlapi.parse(data.apiHost);

        console.log('apiHost: ' + "https://"+url.host);
        return "https://"+url.host;
    },
...
```

Перейдите в каталог `BikeSharingWeb` и используйте `azds up` для запуска обновленной службы Бикешарингвеб.

```console
cd BikeSharingWeb/
azds up
```

Перейдите к образцу приложения в дочернем пространстве *dev/azureuser1* и обратите внимание, что вы перенаправлялись на использование протокола HTTPS без ошибок.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как в Azure Dev Spaces можно разрабатывать более сложные приложения в нескольких контейнерах и как упростить совместную разработку, используя разные версии и ветви кода в разных средах.

> [!div class="nextstepaction"]
> [Краткое руководство. Коллективная разработка в Kubernetes с использованием Azure Dev Spaces][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[helm-installed]: https://helm.sh/docs/using_helm/#installing-helm
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/pages/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml