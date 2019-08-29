---
title: Защита контейнеров pod с помощью политик сети в службе Azure Kubernetes (AKS)
description: Узнайте, как защитить входящий и исходящий трафик в модулях Pod с помощью политик сети Kubernetes в службе Kubernetes Azure (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: mlearned
ms.openlocfilehash: 1339fe66a4925104d459c0491caccdd7db5998a7
ms.sourcegitcommit: 8e1fb03a9c3ad0fc3fd4d6c111598aa74e0b9bd4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70114467"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Защита трафика между контейнерами pod с использованием политик сети в Службе Azure Kubernetes (AKS)

При выполнении современных приложений на базе микрослужб в Kubernetes часто требуется управлять тем, какие компоненты могут взаимодействовать друг с другом. Принцип наименьших привилегий должен применяться для передачи трафика между модулями Pod в кластере Azure Kubernetes Service (AKS). Предположим, что вам, скорее всего, потребуется блокировать трафик непосредственно на серверные приложения. Функция *сетевой политики* в Kubernetes позволяет определять правила для входящего и исходящего трафика между модулями Pod в кластере.

В этой статье показано, как установить модуль политики сети и создать Kubernetes сетевые политики для управления потоком трафика между модулями Pod в AKS. Сетевую политику следует использовать только для узлов под управлением Linux и модулей Pod в AKS.

## <a name="before-you-begin"></a>Перед началом работы

Требуется Azure CLI версии 2.0.61 или более поздней. Чтобы узнать версию, выполните команду  `az --version`. Если необходимо установить или обновить, см. раздел [install Azure CLI][install-azure-cli].

> [!TIP]
> Если вы использовали функцию сетевой политики во время предварительной версии, рекомендуется [создать новый кластер](#create-an-aks-cluster-and-enable-network-policy).
> 
> Если вы хотите продолжить использовать существующие тестовые кластеры, которые использовали политику сети во время предварительной версии, обновите кластер до новой версии Kubernetes для получения последнего общедоступного выпуска, а затем разверните следующий манифест YAML, чтобы устранить сбой сервера метрик и Kubernetes панели мониторинга. Это исправление требуется только для кластеров, в которых использовался модуль политики сети Калико.
>
> В целях безопасности рекомендуется [изучить содержимое этого манифеста YAML][calico-aks-cleanup] , чтобы понять, что развертывается в кластере AKS.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Общие сведения о политике сети

По умолчанию все модули Pod в кластере AKS могут отправлять и получать трафик без ограничений. Для повышения безопасности вы можете определить правила, управляющие потоком трафика. Приложения серверной части часто предоставляются только для требуемых интерфейсных служб, например. Или компоненты базы данных доступны только для тех уровней приложений, которые подключаются к ним.

Политика сети — это спецификация Kubernetes, определяющая политики доступа для обмена данными между модулями Pod. Используя политики сети, вы определяете упорядоченный набор правил для отправки и получения трафика и их применения к коллекции модулей Pod, соответствующей одному или нескольким селекторам меток.

Эти правила сетевой политики определены как манифесты YAML. Сетевые политики могут быть включены в состав более широкого манифеста, который также создает развертывание или службу.

### <a name="network-policy-options-in-aks"></a>Параметры сетевой политики в AKS

Azure предоставляет два способа реализации сетевой политики. При создании кластера AKS вы выбираете параметр сетевой политики. Невозможно изменить параметр политики после создания кластера:

* Собственная реализация Azure, называемая *политиками сети Azure*.
* *Калико сетевые политики*, решение для обеспечения безопасности сети и сети с открытым исходным кодом, основанное на [Tiger][tigera].

Для применения указанных политик в обеих реализациях используется Linux *iptables* . Политики преобразуются в наборы разрешенных и запрещенных пар IP. Эти пары затем запрограммированы как правила фильтрации Иптабле.

Политика сети работает только с параметром Azure CNI (дополнительно). Реализация отличается для двух вариантов:

* *Политики сети Azure* . CNI Azure настраивает мост на узле виртуальной машины для сетей внутри узла. Правила фильтрации применяются, когда пакеты проходят через мост.
* *Политики сети Калико* . CNI Azure настраивает Локальные маршруты ядра для трафика внутри узла. Политики применяются к сетевому интерфейсу Pod.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Различия между политиками Azure и Калико и их возможностями

| Возможность                               | Azure                      | калико                      |
|------------------------------------------|----------------------------|-----------------------------|
| Поддерживаемые платформы                      | Linux                      | Linux                       |
| Поддерживаемые параметры сети             | Azure CNI                  | Azure CNI                   |
| Соответствие спецификации Kubernetes | Все поддерживаемые типы политик |  Все поддерживаемые типы политик |
| Дополнительные функции                      | Отсутствуют                       | Расширенная модель политики, состоящая из глобальной сетевой политики, набора глобальных сетей и конечной точки узла. Дополнительные сведения об использовании `calicoctl` интерфейса командной строки для управления этими расширенными функциями см. в разделе [Справочник пользователя по каликоктл][calicoctl]. |
| Поддержка                                  | Поддерживаются группой поддержки и инженеров по технической поддержке Azure | Поддержка сообщества Калико. Дополнительные сведения о дополнительной платной поддержке см. в разделе [варианты поддержки Project Калико][calico-support]. |
| Ведение журнала                                  | Правила, добавленные или удаленные в IPTables, регистрируются на каждом узле в разделе */Вар/лог/Азуре-НПМ.лог* | Дополнительные сведения см. в разделе [журналы компонентов Калико][calico-logs] . |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Создание кластера AKS и включение политики сети

Чтобы просмотреть политики сети в действии, создадим и развернув политику, определяющую поток трафика:

* запрет любого трафика к pod;
* разрешение трафика на основе меток pod;
* разрешение трафика на основе пространства имен.

Сначала создадим кластер AKS, который поддерживает сетевую политику. Компонент политики сети можно включить только при создании кластера. Вы не можете включить политику сети в существующем кластере AKS.

Чтобы использовать сетевую политику с кластером AKS, необходимо использовать [подключаемый модуль Azure CNI][azure-cni] и определить собственную виртуальную сеть и подсети. Дополнительные сведения о планировании требуемых диапазонов подсетей см. в разделе [Настройка расширенной сети][use-advanced-networking].

Ниже приведен пример сценария, который выполняет следующее:

* Создает виртуальную сеть и подсеть.
* Создание субъекта-службы Azure Active Directory (Azure AD) для использования с кластером AKS.
* Назначает субъекту-службе кластера AKS разрешения *Участник* для виртуальной сети.
* Создает кластер AKS в определенной виртуальной сети и включает сетевую политику.
    * Используется параметр политики сети *Azure* . Чтобы вместо этого использовать Калико в качестве параметра сетевой политики, используйте `--network-policy calico` параметр.

Укажите собственный безопасный пароль *SP_PASSWORD*. Переменные *RESOURCE_GROUP_NAME* и *CLUSTER_NAME* можно заменить:

```azurecli-interactive
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
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

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
# Enable network policy by using the `--network-policy` parameter
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --network-plugin azure \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-policy azure
```

Создание кластера занимает несколько минут. Когда кластер будет готов, настройте `kubectl` для подключения к кластеру Kubernetes с помощью команды [AZ AKS Get-credentials][az-aks-get-credentials] . Эта команда скачивает учетные данные и настраивает интерфейс командной строки Kubernetes для их использования.

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Запрещение всего входящего трафика pod

Прежде чем определить правила, разрешающие определенный сетевой трафик, создайте политику сети, чтобы запретить весь трафик. Эта политика дает отправную точку, чтобы начать список разрешений только нужный трафик. Кроме того, вы увидите, как трафик блокируется при применении политики сети.

Для примера среды приложения и правил трафика давайте создадим пространство имен, именуемое *Development* , для запуска примеров модулей Pod:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Создайте пример серверного модуля, на котором выполняется NGINX. Этот серверный модуль можно использовать для имитации примера серверного веб-приложения. Создайте этот pod в пространстве имен *development* и откройте порт *80* для обслуживания веб-трафика. Добавьте к pod метку с помощью *app=webapp,role=backend*. Так мы сможем указать его в политике сети в приведенном ниже разделе.

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Создайте другой Pod и подключите сеанс терминала, чтобы убедиться, что можно успешно открыть веб-страницу NGINX по умолчанию:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

В командной строке оболочки используйте `wget` команду, чтобы убедиться, что вы можете получить доступ к веб-странице nginx по умолчанию:

```console
wget -qO- http://backend
```

В следующем примере выходных данных показано, что возвращается веб-страница NGINX по умолчанию:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Выйдите из подключенного сеанса терминала. Модуль тестирования автоматически удаляется.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Создание и применение политики сети

Теперь, когда вы подтвердили, можно использовать базовую веб-страницу NGINX в образце серверного модуля, Создайте сетевую политику для запрета всего трафика. Создайте файл `backend-policy.yaml` и вставьте в него следующий манифест YAML. Этот манифест использует *подселектор* для присоединения политики к модулям Pod, имеющим метку *app: webapp, role: серверной* , например, пример nginx. Правила в разделе *ingress* не определены, поэтому запрещен весь входящий трафик к pod.

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

Примените сетевую политику с помощью команды [kubectl Apply][kubectl-apply] и укажите имя манифеста YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Проверка политики сети


Посмотрим, можно ли снова использовать веб-страницу NGINX в серверном модуле Pod. Создайте еще один тестовый pod и подключите сеанс терминала.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

В командной строке оболочки используйте `wget` , чтобы узнать, есть ли доступ к веб-странице nginx по умолчанию. На этот раз установите значение времени ожидания в *2* секунды. Политика сети теперь блокирует весь входящий трафик, поэтому страница не может быть загружена, как показано в следующем примере:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Выйдите из подключенного сеанса терминала. Модуль тестирования автоматически удаляется.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Разрешение входящего трафика на основе метки pod

В предыдущем разделе был запланирован серверный модуль NGINX и была создана сетевая политика для запрета всего трафика. Давайте создадим интерфейсный модуль и обновляем политику сети, чтобы разрешить трафик от интерфейсных модулей.

Обновите политику сети, чтобы разрешить трафик от контейнеров pod с метками *app:webapp,role:frontend* в любом пространстве имен. Измените предыдущий файл *серверной части-Policy. YAML* и добавьте правила входящего трафика *матчлабелс* , чтобы манифест был похож на следующий пример:

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

> [!NOTE]
> Эта политика сети использует элементы *namespaceSelector* и *podSelector* для правила входящего трафика. Синтаксис YAML важен, чтобы правила входящих данных были аддитивными. В этом примере оба элемента должны совпадать для применения правила входящего трафика. Kubernetes версии до *1,12* могут неправильно интерпретировать эти элементы и ограничить сетевой трафик. Дополнительные сведения об этом поведении см. [в разделе поведение функций и из селекторов][policy-rules].

Примените обновленную сетевую политику с помощью команды [kubectl Apply][kubectl-apply] и укажите имя манифеста YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Запланируйте модуль Pod, помеченный как *app = webapp, role = внешний интерфейс* , и подключите сеанс терминала:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

В командной строке оболочки используйте `wget` , чтобы узнать, есть ли доступ к веб-странице nginx по умолчанию:

```console
wget -qO- http://backend
```

Так как правило входящего трафика разрешает трафик с модулями Pod, у которых есть *приложение меток: webapp, role:* Front, трафик из модуля внешнего интерфейса разрешается. В следующем примере выходных данных показана возвращаемая веб-страница NGINX по умолчанию:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Выйдите из подключенного сеанса терминала. Модуль POD автоматически удаляется.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Тестирование pod без соответствующей метки

Политика сети разрешает трафик от контейнеров pod с метками *app:webapp,role:frontend*, но должна блокировать весь остальной трафик. Проверим, может ли другой Pod без этих меток получить доступ к модулю NGINX. Создайте еще один тестовый pod и подключите сеанс терминала.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

В командной строке оболочки используйте `wget` , чтобы узнать, есть ли доступ к веб-странице nginx по умолчанию. Сетевая политика блокирует входящий трафик, поэтому страницу невозможно загрузить, как показано в следующем примере:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Выйдите из подключенного сеанса терминала. Модуль тестирования автоматически удаляется.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Разрешение трафика, передаваемого в пределах заданного пространства имен

В предыдущих примерах вы создали сетевую политику, которая отклонила весь трафик, а затем обновила политику, чтобы разрешить трафик из модулей Pod с определенной меткой. Другой распространенной потребностью является ограничение трафика только в пределах заданного пространства имен. Если в предыдущих примерах использовался трафик в пространстве имен *разработки* , Создайте сетевую политику, которая не достигают трафика из другого пространства имен, например *рабочей среды*, в среду.

Сначала создайте пространство имен для имитации рабочего пространства имен.

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Запланируйте запуск тестового pod в пространстве имен *production*, которое помечено как *app=webapp,role=frontend*. Подключите сеанс терминала.

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

В командной строке оболочки используйте `wget` команду, чтобы убедиться, что вы можете получить доступ к веб-странице nginx по умолчанию:

```console
wget -qO- http://backend.development
```

Так как метки для Pod соответствуют тем, которые в настоящее время разрешены в политике сети, трафик разрешается. Политика сети проверяет не пространства имен, а метки pod. В следующем примере выходных данных показана возвращаемая веб-страница NGINX по умолчанию:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Выйдите из подключенного сеанса терминала. Модуль тестирования автоматически удаляется.

```console
exit
```

### <a name="update-the-network-policy"></a>Обновление политики сети

Давайте изменим раздел входящих правил *намеспацеселектор* , допуская только трафик из пространства имен *Development* . Измените файл манифеста *backend-policy.yaml*, как показано в следующем примере.

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

В более сложных примерах можно определить несколько правил входящего трафика, таких как *намеспацеселектор* , а затем *подселектор*.

Примените обновленную сетевую политику с помощью команды [kubectl Apply][kubectl-apply] и укажите имя манифеста YAML:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Тестирование обновленной политики сети

Запланируйте еще один модуль в рабочем пространстве имен и подключите сеанс терминала:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

В командной строке оболочки используйте `wget` , чтобы увидеть, что политика сети запрещает трафик:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Выйдите из тестового pod.

```console
exit
```

Если трафик отклонен из *рабочего* пространства имен, запланируйте тестовый модуль в пространстве имен *разработки* и подключите сеанс терминала:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

В командной строке оболочки используйте `wget` , чтобы увидеть, что сетевая политика разрешает трафик:

```console
wget -qO- http://backend
```

Трафик разрешен, поскольку модуль Pod запланирован в пространстве имен, которое соответствует разрешенному в политике сети. В следующем примере выходных данных показана возвращаемая веб-страница NGINX по умолчанию:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Выйдите из подключенного сеанса терминала. Модуль тестирования автоматически удаляется.

```console
exit
```

## <a name="clean-up-resources"></a>Очистка ресурсов

В этой статье мы создали два пространства имен и применили политику сети. Чтобы очистить эти ресурсы, используйте команду [kubectl Delete][kubectl-delete] и укажите имена ресурсов:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о сетевых ресурсах см. [в разделе Основные понятия сети для приложений в службе Azure Kubernetes Service (AKS)][concepts-network].

Дополнительные сведения о политиках см. в разделе [Kubernetes Network Policies][kubernetes-network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.6/reference/calicoctl/
[calico-support]: https://www.projectcalico.org/support
[calico-logs]: https://docs.projectcalico.org/v3.6/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
