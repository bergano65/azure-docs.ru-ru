---
title: Использование пользовательского контроллера входящих данных траефик и настройка HTTPS
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Узнайте, как настроить Azure Dev Spaces для использования пользовательского контроллера входящих данных траефик и настройки HTTPS с помощью этого контроллера входящего трафика.
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: 6cac50ea9caeea42b0a7522dbeb5039a8461dd2c
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78389673"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Использование пользовательского контроллера входящих данных траефик и настройка HTTPS

В этой статье показано, как настроить Azure Dev Spaces для использования пользовательского контроллера входящего трафика траефик. В этой статье также показано, как настроить этот настраиваемый контроллер входящего трафика для использования протокола HTTPS.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если ее нет, можно создать [бесплатную учетную запись][azure-account-create].
* [Установленный Azure CLI][az-cli].
* [Кластер Azure Kubernetes Service (AKS) с включенным Azure dev Spaces][qs-cli].
* [kubectl][kubectl] установлен.
* [Установленная версия Helm 3][helm-installed].
* [Личный домен][custom-domain] с [зоной DNS][dns-zone]. В этой статье предполагается, что личный домен и зона DNS находятся в той же группе ресурсов, что и кластер AKS, но можно использовать личный домен и зону DNS в другой группе ресурсов.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Настройка пользовательского контроллера входящего трафика траефик

Подключитесь к кластеру с помощью [kubectl][kubectl], клиента командной строки Kubernetes. Чтобы настроить `kubectl` на подключение к кластеру Kubernetes, выполните команду [az aks get-credentials][az-aks-get-credentials]. Эта команда скачивает учетные данные и настраивает интерфейс командной строки Kubernetes для их использования.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Чтобы проверить подключение к кластеру, используйте команду [kubectl get][kubectl-get] для получения списка узлов кластера.

```console
$ kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Добавьте [официальный стабильный репозиторий Helm][helm-stable-repo], который содержит диаграмму Helm для контроллера входящих данных траефик.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

Создайте пространство имен Kubernetes для контроллера входящих данных траефик и установите его с помощью `helm`.

> [!NOTE]
> Если AKS не включен RBAC, удалите параметр *--Set RBAC. Enabled = True* .

```console
kubectl create ns traefik
helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0
```

> [!NOTE]
> В приведенном выше примере создается общедоступная конечная точка для контроллера входящего трафика. Если вместо этого необходимо использовать частную конечную точку для контроллера входящего трафика, добавьте параметр *--Set Service. Annotations. Служба\\. бета-\\. kubernetes\\. IO/Azure-Load-балансировщик — внутренний параметр = true* для команды *Helm Install* .
> ```console
> helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --set service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.85.0
> ```
> Эта частная конечная точка предоставляется в виртуальной сети, в которой развернут кластер AKS.

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

В приведенном выше примере запись *A* добавляется в зону DNS *MY_CUSTOM_DOMAIN* .

В этой статье на примере [приложения Azure Dev Spaces для аренды велосипедов](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) вы изучите применение Azure Dev Spaces. Клонируйте приложение с GitHub и перейдите в его каталог:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Откройте [Values. YAML][values-yaml] и внесите следующие обновления:
* Замените все экземпляры *< REPLACE_ME_WITH_HOST_SUFFIX >* *траефик. MY_CUSTOM_DOMAIN* использование домена для *MY_CUSTOM_DOMAIN*. 
* Замените *kubernetes.IO/Ingress.class: траефик-аздс # dev Spaces, относящийся* к *kubernetes.IO/Ingress.class: Траефик # Custom входной*вход. 

Ниже приведен пример обновленного файла `values.yaml`.

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

Создайте пространство *разработки* с помощью примера приложения, используя `azds space select`.

```console
azds space select -n dev -y
```

Разверните пример приложения с помощью `helm install`.

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

В приведенном выше примере пример приложения развертывается в пространстве имен *dev* .

Отображение URL-адресов для доступа к образцу приложения с помощью `azds list-uris`.

```console
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

> [!NOTE]
> Если вместо службы *бикешарингвеб* отображается страница ошибки, убедитесь **, что вы обновили** заметку *kubernetes.IO/Ingress.class* и узел в файле *Values. YAML* .

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

Используйте [Диспетчер сертификатов][cert-manager] для автоматизации управления сертификатом TLS при настройке контроллера входящих подключений траефик для использования протокола HTTPS. Используйте `helm`, чтобы установить диаграмму *цертманажер* .

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace traefik
kubectl label namespace traefik certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace traefik --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Создайте файл `letsencrypt-clusterissuer.yaml` и обновите поле электронной почты, указав свой адрес электронной почты.

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
            class: traefik
```

> [!NOTE]
> Для тестирования также существует [промежуточный сервер][letsencrypt-staging-issuer] , который можно использовать для *клустериссуер*.

Используйте `kubectl`, чтобы применить `letsencrypt-clusterissuer.yaml`.

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace traefik
```

Обновите траефик, чтобы использовать HTTPS с помощью `helm`.

```console
helm upgrade traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0 --set ssl.enabled=true --set ssl.enforced=true --set ssl.permanentRedirect=true
```

Обновите [Values. YAML][values-yaml] , чтобы включить сведения об использовании *ДИСПЕТЧЕРА сертификатов* и HTTPS. Ниже приведен пример обновленного файла `values.yaml`.

```yaml
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.

bikesharingweb:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-bikesharingweb-secret

gateway:
  ingress:
    annotations:
      kubernetes.io/ingress.class: traefik  # Custom Ingress
      cert-manager.io/cluster-issuer: letsencrypt
    hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN  # Assumes deployment to the 'dev' space
    tls:
    - hosts:
      - dev.gateway.traefik.MY_CUSTOM_DOMAIN
      secretName: dev-gateway-secret
```

Обновите пример приложения с помощью `helm`:

```console
helm upgrade bikesharing . --namespace dev --atomic
```

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
cd ../BikeSharingWeb/
azds up
```

Перейдите к образцу приложения в дочернем пространстве *dev/azureuser1* и обратите внимание, что вы перенаправлялись на использование протокола HTTPS без ошибок.

## <a name="next-steps"></a>Следующие шаги

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
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/pages/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml