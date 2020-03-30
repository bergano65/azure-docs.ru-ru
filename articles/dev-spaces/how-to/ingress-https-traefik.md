---
title: Используйте пользовательский контроллер traefik ввхода и настроить HTTPS
services: azure-dev-spaces
ms.date: 12/10/2019
ms.topic: conceptual
description: Узнайте, как настроить пространства Azure Dev для использования пользовательского контроллера traefik, и настроить HTTPS с помощью контроллера входа
keywords: Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s
ms.openlocfilehash: fd11b3bbd3f90b75203084ff0753c1485d57a35b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80155435"
---
# <a name="use-a-custom-traefik-ingress-controller-and-configure-https"></a>Используйте пользовательский контроллер traefik ввхода и настроить HTTPS

В этой статье показано, как настроить пространства Azure Dev для использования пользовательского контроллера traefik. В этой статье также показано, как настроить этот пользовательский контроллер входа для использования HTTPS.

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если ее нет, можно создать [бесплатную учетную запись][azure-account-create].
* [Установленный Azure CLI][az-cli].
* [Кластер Azure Kubernetes Service (AKS) с включенными пространствами Azure Dev.][qs-cli]
* [kubectl][kubectl] установлен.
* [Установленная версия Helm 3][helm-installed].
* [Пользовательский домен][custom-domain] с [зоной DNS][dns-zone]. В этой статье предполагается, что пользовательский домен и зона DNS находятся в той же группе ресурсов, что и кластер AKS, но в другой группе ресурсов можно использовать пользовательский домен и зону DNS.

## <a name="configure-a-custom-traefik-ingress-controller"></a>Настройка пользовательского контроллера traefik входа

Подключитесь к кластеру с помощью [kubectl,][kubectl]клиент командной строки Kubernetes. Чтобы настроить `kubectl` на подключение к кластеру Kubernetes, выполните команду [az aks get-credentials][az-aks-get-credentials]. Эта команда скачивает учетные данные и настраивает интерфейс командной строки Kubernetes для их использования.

```azurecli
az aks get-credentials --resource-group myResourceGroup --name myAKS
```

Чтобы проверить подключение к кластеру, используйте команду [kubectl get][kubectl-get] для получения списка узлов кластера.

```console
kubectl get nodes
NAME                                STATUS   ROLES   AGE    VERSION
aks-nodepool1-12345678-vmssfedcba   Ready    agent   13m    v1.14.1
```

Добавьте [официальный стабильный репозиторий Helm,][helm-stable-repo]который содержит диаграмму контроллера traefik ingress Helm.

```console
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

Создайте пространство имен Kubernetes для контроллера traefik `helm`входа и установите его с помощью.

> [!NOTE]
> Если в кластере AKS нет включенного RBAC, удалите параметр *--set rbac.enabled-true.*

```console
kubectl create ns traefik
helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0
```

> [!NOTE]
> Приведенный выше пример создает общедоступную точку для контроллера входа. Если вместо этого необходимо использовать частную конечную точку для контроллера входа, добавьте *--набор service.annotations." сервис\\.beta\\\\.kubernetes .io/azure-load-balancer-internal" -истинный* параметр команды *установки руля.*
> ```console
> helm install traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set fullnameOverride=customtraefik --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --set service.annotations."service\.beta\.kubernetes\.io/azure-load-balancer-internal"=true --version 1.85.0
> ```
> Эта частная конечная точка разоблачается в виртуальной сети, где развертывается кластер AKS.

Получить IP-адрес traefik вход контроллер службы с помощью [kubectl получить][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

На выходе выборки отображаны IP-адреса для всех служб в пространстве имен *traefik.*

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP   PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>     80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Добавьте *запись A* в свою зону DNS с внешним IP-адресом службы traefik, используя [аз-сети dns, зафиксиваемую дополнительную запись.][az-network-dns-record-set-a-add-record]

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_EXTERNAL_IP
```

Вышеприведенный пример добавляет запись *A* в *MY_CUSTOM_DOMAIN* dNS-зону.

В этой статье на примере [приложения Azure Dev Spaces для аренды велосипедов](https://github.com/Azure/dev-spaces/tree/master/samples/BikeSharingApp) вы изучите применение Azure Dev Spaces. Клонируйте приложение с GitHub и перейдите в его каталог:

```cmd
git clone https://github.com/Azure/dev-spaces
cd dev-spaces/samples/BikeSharingApp/charts
```

Откройте [values.yaml][values-yaml] и сделайте следующие обновления:
* Замените все экземпляры *<REPLACE_ME_WITH_HOST_SUFFIX>* *трафиком. MY_CUSTOM_DOMAIN* использовать домен для *MY_CUSTOM_DOMAIN.* 
* Замените *kubernetes.io/ingress.class: traefik-azds - Dev Spaces-специфические* с *kubernetes.io/ingress.class: traefik и пользовательский Ingress*. 

Ниже приведен пример обновленного `values.yaml` файла:

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

Создайте пространство *для разработчиков* с помощью `azds space select`примера приложения.

```console
azds space select -n dev -y
```

Развертывание образца `helm install`приложения с помощью .

```console
helm install bikesharingsampleapp . --dependency-update --namespace dev --atomic
```

Вышеприведенный пример развертывает применение образца в пространство имен *разработчиков.*

Отображение URL-адресов `azds list-uris`для доступа к примеру приложения с помощью .

```console
azds list-uris
```

Ниже выход показывает пример URL-адреса из `azds list-uris`.

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Перейдите к службе *bikesharingweb* по общедоступному URL-адресу, который вам предоставила команда `azds list-uris`. В примере выше для службы *bikesharingweb* используется общедоступный URL-адрес `http://dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

> [!NOTE]
> Если вы видите страницу ошибки вместо сервиса *bikesharingweb,* убедитесь, что вы обновили **как** *аннотацию kubernetes.io/ingress.class,* так и хост в файле *values.yaml.*

Используйте `azds space select` команду, чтобы создать детское пространство под *dev* и перечислить URL-адреса для доступа к пространству девятого ребенка.

```console
azds space select -n dev/azureuser1 -y
azds list-uris
```

Ниже приведены данные, `azds list-uris` на которых показаны URL-адреса примера, чтобы получить доступ к примеру приложения в пространстве разработчиков для разработчиков *azureuser1.*

```console
Uri                                                  Status
---------------------------------------------------  ---------
http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/  Available
http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/         Available
```

Перейдите к службе *bikesharingweb* в пространстве разработчиков ребенка *azureuser1,* открыв общедоступный URL-адрес `azds list-uris` команды. В приведенном выше примере, общедоступный URL для *сервиса bikesharingweb* в *пространстве разработчиков azureuser1* является `http://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/`.

## <a name="configure-the-traefik-ingress-controller-to-use-https"></a>Налажить контроллер traefik для использования HTTPS

Используйте [сертификат-менеджер][cert-manager] для автоматизации управления сертификатом TLS при настройке контроллера traefik для использования HTTPS. Используйте `helm` для установки диаграммы *сертификат-менеджера.*

```console
kubectl apply --validate=false -f https://raw.githubusercontent.com/jetstack/cert-manager/release-0.12/deploy/manifests/00-crds.yaml --namespace traefik
kubectl label namespace traefik certmanager.k8s.io/disable-validation=true
helm repo add jetstack https://charts.jetstack.io
helm repo update
helm install cert-manager --namespace traefik --version v0.12.0 jetstack/cert-manager --set ingressShim.defaultIssuerName=letsencrypt --set ingressShim.defaultIssuerKind=ClusterIssuer
```

Создайте `letsencrypt-clusterissuer.yaml` файл и обновите поле электронной почты с помощью адреса электронной почты.

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
> Для тестирования также есть [промежуточный сервер,][letsencrypt-staging-issuer] который можно использовать для *clusterIssuer.*

Используйте `kubectl` `letsencrypt-clusterissuer.yaml`для применения .

```console
kubectl apply -f letsencrypt-clusterissuer.yaml --namespace traefik
```

Удалите предыдущую *traefik* *ClusterRole* и *ClusterRoleBinding,* затем `helm`обновите traefik для использования HTTPS с помощью.

> [!NOTE]
> Если в кластере AKS нет включенного RBAC, удалите параметр *--set rbac.enabled-true.*

```console
kubectl delete ClusterRole traefik
kubectl delete ClusterRoleBinding traefik
helm upgrade traefik stable/traefik --namespace traefik --set kubernetes.ingressClass=traefik --set rbac.enabled=true --set kubernetes.ingressEndpoint.useDefaultPublishedService=true --version 1.85.0 --set ssl.enabled=true --set ssl.enforced=true --set ssl.permanentRedirect=true
```

Получить обновленный IP-адрес traefik вход контроллер службы с помощью [kubectl получить][kubectl-get].

```console
kubectl get svc -n traefik --watch
```

На выходе выборки отображаны IP-адреса для всех служб в пространстве имен *traefik.*

```console
NAME      TYPE           CLUSTER-IP    EXTERNAL-IP          PORT(S)                      AGE
traefik   LoadBalancer   10.0.205.78   <pending>            80:32484/TCP,443:30620/TCP   20s
...
traefik   LoadBalancer   10.0.205.78   MY_NEW_EXTERNAL_IP   80:32484/TCP,443:30620/TCP   60s
```

Добавьте *запись A* в свою зону DNS с новым внешним IP-адресом службы traefik, используя [аз-сети dns, зафиксивают надстройку][az-network-dns-record-set-a-add-record] и удалите предыдущую запись *с* помощью [аз-сети dns, зафиксиваемзапись удаления.][az-network-dns-record-set-a-remove-record]

```azurecli
az network dns record-set a add-record \
    --resource-group myResourceGroup \
    --zone-name MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address MY_NEW_EXTERNAL_IP

az network dns record-set a remove-record \
    --resource-group myResourceGroup \
    --zone-name  MY_CUSTOM_DOMAIN \
    --record-set-name *.traefik \
    --ipv4-address PREVIOUS_EXTERNAL_IP
```

Приведенный выше пример обновляет *MY_CUSTOM_DOMAIN* запись *A* в MY_CUSTOM_DOMAIN dNS-зоне для использования *PREVIOUS_EXTERNAL_IP.*

Обновление [values.yaml,][values-yaml] чтобы включить детали для использования *сертификат-менеджера* и HTTPS. Ниже приведен пример обновленного `values.yaml` файла:

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

Обновление образца `helm`приложения с помощью:

```console
helm upgrade bikesharingsampleapp . --namespace dev --atomic
```

Перейдите к примеру приложения в детском пространстве *dev/azureuser1* и обратите внимание, что вы перенаправлены на использование HTTPS.

> [!IMPORTANT]
> Для завершения dNS-изменений и доступности примера может потребоваться 30 минут или более.

Также обратите внимание, что страница загружается, но браузер показывает некоторые ошибки. Открытие консоли браузера показывает, что ошибка связана со страницей HTTPS, пытаясь загрузить ресурсы HTTP. Пример:

```console
Mixed Content: The page at 'https://azureuser1.s.dev.bikesharingweb.traefik.MY_CUSTOM_DOMAIN/devsignin' was loaded over HTTPS, but requested an insecure resource 'http://azureuser1.s.dev.gateway.traefik.MY_CUSTOM_DOMAIN/api/user/allUsers'. This request has been blocked; the content must be served over HTTPS.
```

Чтобы исправить эту ошибку, обновите [BikeSharingWeb/azds.yaml,][azds-yaml] чтобы использовать *traefik* для *kubernetes.io/ingress.class* и пользовательский домен за *$(hostSuffix)*. Пример:

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

Обновление [BikeSharingWeb/package.json][package-json] с зависимостью для *url-пакета.*

```json
{
...
    "react-responsive": "^6.0.1",
    "universal-cookie": "^3.0.7",
    "url": "0.11.0"
  },
...
```

Обновление метода *getApiHostAsync* в [BikeSharingWeb/lib/helpers.js][helpers-js] для использования HTTPS:

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

Перейдите `BikeSharingWeb` в каталог `azds up` и используйте для запуска обновленного сервиса BikeSharingWeb.

```console
cd ../BikeSharingWeb/
azds up
```

Перейдите к примеру приложения в детском пространстве *dev/azureuser1* и обратите внимание, что вы перенаправлены на использование HTTPS без каких-либо ошибок.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как в Azure Dev Spaces можно разрабатывать более сложные приложения в нескольких контейнерах и как упростить совместную разработку, используя разные версии и ветви кода в разных средах.

> [!div class="nextstepaction"]
> [Краткое руководство. Коллективная разработка в Kubernetes с использованием Azure Dev Spaces][team-development-qs]


[az-cli]: /cli/azure/install-azure-cli?view=azure-cli-latest
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-network-dns-record-set-a-add-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-add-record
[az-network-dns-record-set-a-remove-record]: /cli/azure/network/dns/record-set/a?view=azure-cli-latest#az-network-dns-record-set-a-remove-record
[custom-domain]: ../../app-service/manage-custom-dns-buy-domain.md#buy-the-domain
[dns-zone]: ../../dns/dns-getstarted-cli.md
[qs-cli]: ../quickstart-cli.md
[team-development-qs]: ../quickstart-team-development.md

[azds-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/azds.yaml
[azure-account-create]: https://azure.microsoft.com/free
[cert-manager]: https://cert-manager.io/
[helm-installed]: https://helm.sh/docs/intro/install/
[helm-stable-repo]: https://helm.sh/docs/intro/quickstart/#initialize-a-helm-chart-repository
[helpers-js]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/lib/helpers.js#L7
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[letsencrypt-staging-issuer]: https://cert-manager.io/docs/configuration/acme/#creating-a-basic-acme-issuer
[package-json]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/BikeSharingWeb/package.json
[values-yaml]: https://github.com/Azure/dev-spaces/blob/master/samples/BikeSharingApp/charts/values.yaml