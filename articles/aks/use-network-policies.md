---
title: Защита контейнеров pod с помощью политик сети в службе Azure Kubernetes (AKS)
description: Узнайте, как для защитить входящий и исходящий трафик, передаваемый через контейнеры pod, с помощью политик сети Kubernetes в службе Azure Kubernetes (AKS).
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 02/12/2019
ms.author: iainfou
ms.openlocfilehash: ade5a39273aa807f6c69f76342a0f715c7a96309
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232180"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Защита трафика между контейнерами pod с использованием политик сети в Службе Azure Kubernetes (AKS)

При выполнении современных приложений на базе микрослужб в Kubernetes часто требуется управлять тем, какие компоненты могут взаимодействовать друг с другом. К передаче трафика между контейнерами pod в кластере AKS следует применить принцип наименьших привилегий. Например, скорее всего трафик, передаваемый напрямую в серверные приложения, нужно будет блокировать. В Kubernetes доступны *политики сети*, которые позволяют определить правила для входящего и исходящего трафика, передаваемого между контейнерами pod в кластере.

В этой статье показано, как использовать политики сети для управления потоком трафика между контейнерами pod в AKS.

> [!IMPORTANT]
> Эта функция в настоящее время находится на стадии предварительной версии. Предварительные версии предоставляются только в том случае, если вы принимаете [дополнительные условия использования][terms-of-use]. Некоторые аспекты этой функции могут быть изменены до выхода общедоступной версии.

## <a name="before-you-begin"></a>Перед началом работы

Кроме того, вам необходимо установить и настроить службу Azure CLI версии 2.0.56 или более поздней. Чтобы узнать версию, выполните команду  `az --version`. Если вам необходимо выполнить установку или обновление, см. статью  [Установка Azure CLI][install-azure-cli].

## <a name="overview-of-network-policy"></a>Общие сведения о политике сети

По умолчанию все контейнеры pod в кластере AKS могут отправлять и получать трафик без ограничений. Для повышения безопасности вы можете определить правила, управляющие потоком трафика. Например, серверные приложения часто предоставляются только необходимым внешним службам, или компоненты базы данных доступны только для уровней приложений, которые к ним подключаются.

Политики сети — это ресурсы Kubernetes, которые позволяют контролировать поток трафика между контейнерами pod. Вы можете разрешать или запрещать трафик в зависимости от параметров, например назначенных меток, пространства имен или порта трафика. Политики сети определяются посредством YAML-файлов манифестов и могут быть включены в более глобальный манифест, который помимо политики сети создает развертывание или службу.

Чтобы посмотреть, как действуют политики сети, давайте создадим и развернем политику, определяющую поток трафика следующим образом:

* запрет любого трафика к pod;
* разрешение трафика на основе меток pod;
* разрешение трафика на основе пространства имен.

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Создание кластера AKS и включение политики сети

Политику сети можно включить только при создании кластера. Вы не можете включить политику сети в существующем кластере AKS. Чтобы создать кластер AKS с политикой сети, необходимо сначала включить соответствующий флаг компонента в своей подписке. Чтобы зарегистрировать флаг компонента *EnableNetworkPolicy*, используйте команду [az feature register][az-feature-register], как показано в следующем примере.

```azurecli-interactive
az feature register --name EnableNetworkPolicy --namespace Microsoft.ContainerService
```

Через несколько минут отобразится состояние *Registered* (Зарегистрировано). Состояние регистрации можно проверить с помощью команды [az feature list][az-feature-list].

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableNetworkPolicy')].{Name:name,State:properties.state}"
```

Когда все будет готово, обновите регистрацию поставщика ресурсов *Microsoft.ContainerService* с помощью команды [az provider register][az-provider-register].

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

Чтобы использовать политику сети для кластера AKS, необходимо использовать [подключаемый модуль Azure CNI][azure-cni], а также определить собственную виртуальную сеть и подсети. Более подробные сведения о том, как планировать необходимые диапазоны подсетей, см. в разделе [Настройка сети Azure CNI в Службе Azure Kubernetes (AKS)][use-advanced-networking]. Ниже приведен пример сценария, который выполняет следующее:

* Создает виртуальную сеть и подсеть.
* Создает субъект-службу Azure Active Directory (AD) для кластера AKS.
* Назначает субъекту-службе кластера AKS разрешения *Участник* для виртуальной сети.
* Создает кластер AKS в определенной виртуальной сети и включает политику сети.

Укажите собственный безопасный пароль *SP_PASSWORD*. При необходимости замените переменные *RESOURCE_GROUP_NAME* и *CLUSTER_NAME*.

```azurecli-interactive
SP_PASSWORD=mySecurePassword
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
read SP_ID <<< $(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)

# Create the AKS cluster and specify the virtual network and service principal information
# Enable network policy using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --kubernetes-version 1.12.4 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy calico
```

Создание кластера занимает несколько минут. По завершении, чтобы настроить `kubectl` для подключения к кластеру Kubernetes, выполните команду [az aks get-credentials][az-aks-get-credentials]. Эта команда скачивает учетные данные и настраивает интерфейс командной строки Kubernetes для их использования.

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Запрещение всего входящего трафика pod

Прежде чем определить правила, разрешающие определенный сетевой трафик, создайте политику сети, чтобы запретить весь трафик. Эта политика представляет собой отправную точку для создания списка разрешений только для нужного трафика. Кроме того, вы увидите, как трафик блокируется при применении политики сети.

Давайте сначала создадим для нашей среды с примером приложения и правилами трафика пространство имен *development* для запуска примеров pod.

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Теперь создайте пример внутреннего pod, который запускает NGINX. Этот внутренний pod можно использовать для имитации внутреннего веб-приложения. Создайте этот pod в пространстве имен *development* и откройте порт *80* для обслуживания веб-трафика. Добавьте к pod метку с помощью *app=webapp,role=backend*. Так мы сможем указать его в политике сети в приведенном ниже разделе.

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Чтобы проверить, можно ли связаться с веб-страницей NGINX по умолчанию, создайте другой pod и подключите сеанс терминала.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Когда в командной строке оболочки появится запрос, введите `wget`, чтобы подтвердить, что вы можете получить доступ к веб-странице NGINX по умолчанию.

```console
wget -qO- http://backend
```

В следующем примере выходных данных показано, что возвращена веб-страница NGINX по умолчанию.

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Выйдите из подключенного сеанса терминала. Тестовый pod автоматически удаляется.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Создание и применение политики сети

Теперь, когда вы убедились, что базовая веб-страница NGINX в примере внутреннего pod доступна, создайте политику сети, чтобы запретить весь трафик. Создайте файл `backend-policy.yaml` и вставьте в него следующий манифест YAML. Этот манифест использует *podSelector* для подключения политики к контейнерам pod с меткой *app:webapp,role:backend* — как у вашего примера pod NGINX. Правила в разделе *ingress* не определены, поэтому запрещен весь входящий трафик к pod.

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

Примените политику сети с помощью команды [kubectl apply][kubectl-apply] и укажите имя манифеста YAML.

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Проверка политики сети

Давайте узнаем, сможете ли вы еще раз получить доступ к веб-странице NGINX во внутреннем pod. Создайте еще один тестовый pod и подключите сеанс терминала.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Когда в командной строке оболочки появится запрос, введите `wget`, чтобы узнать, можете ли вы получить доступ к веб-странице NGINX по умолчанию. На этот раз установите значение времени ожидания в *2* секунды. Теперь политика сети блокирует весь входящий трафик, поэтому загрузить страницу не удается, как показано в следующем примере.

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Выйдите из подключенного сеанса терминала. Тестовый pod автоматически удаляется.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Разрешение входящего трафика на основе метки pod

В предыдущем разделе был запланирован запуск внутреннего pod NGINX и создана политика сети, которая запрещает весь трафик. Теперь давайте создадим внешний pod и обновим политику сети, чтобы разрешить трафик от внешних контейнеров pod.

Обновите политику сети, чтобы разрешить трафик от контейнеров pod с метками *app:webapp,role:frontend* в любом пространстве имен. Измените предыдущий файл *backend-policy.yaml* и добавьте правила входящего трафика *matchLabels*, чтобы манифест выглядел как в следующем примере.

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

Примените обновленную политику сети с помощью команды [kubectl apply][kubectl-apply] и укажите имя манифеста YAML.

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Теперь запланируйте запуск pod с метками *app=webapp,role=frontend* и подключите сеанс терминала.

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Когда в командной строке оболочки появится запрос, введите `wget`, чтобы узнать, можете ли вы получить доступ к веб-странице NGINX по умолчанию.

```console
wget -qO- http://backend
```

Так как правило входящего трафика разрешает трафик от контейнеров pod, имеющих метки *app:webapp,role:frontend*, трафик из внешнего pod разрешен. В следующем примере выходных данных показано, что возвращена веб-страница NGINX по умолчанию.

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Выйдите из подключенного сеанса терминала. Контейнер pod автоматически удаляется.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Тестирование pod без соответствующей метки

Политика сети разрешает трафик от контейнеров pod с метками *app:webapp,role:frontend*, но должна блокировать весь остальной трафик. Давайте проверим, сможет ли другой pod без этих меток получить доступ к внутреннему pod NGINX. Создайте еще один тестовый pod и подключите сеанс терминала.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

Когда в командной строке оболочки появится запрос, введите `wget`, чтобы узнать, можете ли вы получить доступ к веб-странице NGINX по умолчанию. Политика сети блокирует входящий трафик, поэтому загрузить страницу не удается, как показано в следующем примере.

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Выйдите из подключенного сеанса терминала. Тестовый pod автоматически удаляется.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Разрешение трафика, передаваемого в пределах заданного пространства имен

В предыдущих примерах вы создали политику сети, которая запрещает весь трафик, а затем изменили ее, чтобы разрешить трафик от контейнеров pod с определенной меткой. Нередко требуется разрешить только, передаваемый трафик в пределах указанного пространства имен. Если предыдущие примеры предназначались для трафика в пространстве имен *development*, то вам может потребоваться создать политику сети, которая блокирует трафик из другого пространства имен, например *production*, не пропуская его к контейнерам pod.

Сначала создайте пространство имен для имитации рабочего пространства имен.

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Запланируйте запуск тестового pod в пространстве имен *production*, которое помечено как *app=webapp,role=frontend*. Подключите сеанс терминала.

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Когда в командной строке оболочки появится запрос, введите `wget`, чтобы подтвердить, что вы можете получить доступ к веб-странице NGINX по умолчанию.

```console
wget -qO- http://backend.development
```

Так как метки для pod совпадают с метками, разрешенными в политике сети, трафик не блокируется. Политика сети проверяет не пространства имен, а метки pod. В следующем примере выходных данных показано, что возвращена веб-страница NGINX по умолчанию.

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Выйдите из подключенного сеанса терминала. Тестовый pod автоматически удаляется.

```console
exit
```

### <a name="update-the-network-policy"></a>Обновление политики сети

Теперь давайте обновим раздел *namespaceSelector* правила входящего трафика, чтобы разрешить только трафик из пространства имен *development*. Измените файл манифеста *backend-policy.yaml*, как показано в следующем примере.

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

В более сложных примерах может быть определено несколько правил входящего трафика, например, использующих *namespaceSelector*, а затем — *podSelector*.

Примените обновленную политику сети с помощью команды [kubectl apply][kubectl-apply] и укажите имя манифеста YAML.

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Тестирование обновленной политики сети

Теперь запланируйте запуск еще одного pod в пространстве имен *production* и подключите сеанс терминала.

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

Когда в командной строке оболочки отобразится запрос, используйте `wget`, чтобы увидеть, что теперь политика сети запрещает трафик.

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Выйдите из тестового pod.

```console
exit
```

Итак, вы запретили трафик пространства имен *production*. Теперь запланируйте запуск тестового pod в пространстве имен *development* и подключите сеанс терминала.

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

Когда в командной строке оболочки отобразится запрос, используйте `wget`, чтобы увидеть, что теперь политика сети разрешает трафик.

```console
wget -qO- http://backend
```

Так запуск pod запланирован в пространстве имен, разрешенным в политике сети, трафик не блокируется. В следующем примере выходных данных показана возвращенная веб-страница NGINX по умолчанию.

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Выйдите из подключенного сеанса терминала. Тестовый pod автоматически удаляется.

```console
exit
```

## <a name="clean-up-resources"></a>Очистка ресурсов

В этой статье мы создали два пространства имен и применили политику сети. Чтобы очистить эти ресурсы, используйте команду [kubectl delete][kubectl-delete] и укажите имена ресурсов, как показано ниже.

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о сетевых ресурсах см. в разделе [Основные понятия сети в Службе Azure Kubernetes (AKS)][concepts-network].

Чтобы узнать больше об использовании политик, ознакомьтесь с разделом [Network Policies][kubernetes-network-policies] (Политики сети).

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
