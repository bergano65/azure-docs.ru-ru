---
title: Защита контейнеров pod с помощью политик сети в службе Azure Kubernetes (AKS)
description: Узнайте, как для защиты трафика, проходящего входящий и исходящий модулей с помощью политик сети Kubernetes в службе Azure Kubernetes (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: a0512806ec797f43fc54d8a28a7cbadf86faf1d9
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/07/2019
ms.locfileid: "65230017"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Защита трафика между контейнерами pod с использованием политик сети в Службе Azure Kubernetes (AKS)

При выполнении современных приложений на базе микрослужб в Kubernetes часто требуется управлять тем, какие компоненты могут взаимодействовать друг с другом. Принцип наименьших прав доступа должны применяться к каким образом трафик сможет проходить между POD, содержащихся в кластер Azure Kubernetes Service (AKS). Допустим, скорее всего, вы хотите заблокировать трафик непосредственно на серверной части приложения. *Политики сети* в Kubernetes позволяет определить правила для входящего и исходящего трафика между POD в кластере.

В этой статье показано, как установить модуль политики сети и Создание политик сети Kubernetes для управления потоком трафика между POD, содержащихся в AKS. Политики сети должен использоваться только для узлов под управлением Linux и POD, содержащихся в AKS.

## <a name="before-you-begin"></a>Перед началом работы

Требуется Azure CLI версии 2.0.61 или более поздней версии установлен и настроен. Чтобы узнать версию, выполните команду  `az --version`. Если вам необходимо выполнить установку или обновление, см. статью  [Установка Azure CLI][install-azure-cli].

> [!TIP]
> Если вы использовали функцию политики сети на этапе предварительной версии, мы рекомендуем вам [Создание нового кластера](#create-an-aks-cluster-and-enable-network-policy).
> 
> Если вы хотите продолжить использование существующих тестовых кластеров, которые используются политики сети на этапе предварительной версии, обновление кластера до новых версий Kubernetes последний выпуск общедоступной версии и затем развернуть приведенный ниже манифест yaml-ФАЙЛ, чтобы устранить сбой сервера метрик и Kubernetes панель мониторинга. Это исправление является только необходимые для кластеров, которые использовать обработчик Кошка черепахового сетевой политики.
>
> По соображениям безопасности [просмотрите содержимое этого манифеста YAML] [ calico-aks-cleanup] чтобы понять, что развернуто в кластере AKS.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Общие сведения о политике сети

Все модули POD в кластере AKS может отправлять и получать трафик без ограничений по умолчанию. Для повышения безопасности вы можете определить правила, управляющие потоком трафика. Во внутренних приложениях часто доступны только для необходимых служб интерфейса, например. Или компоненты баз данных доступны только для уровней приложений, которые подключаются к ним.

Политики сети представляет собой спецификацию Kubernetes, определяющий политики доступа для обмена данными между модулей. Используя сетевые политики, определить упорядоченный набор правил для отправки и приема трафика и применить их к коллекцию модулей, которые соответствуют один или несколько меток селекторов.

Эти правила политики сети определяются как манифесты YAML. Политики сети могут быть включены в манифест шире, также создает развертывания или службы.

### <a name="network-policy-options-in-aks"></a>Параметры политики сети в AKS

Azure предоставляет два способа реализации политики сети. При создании кластера AKS, выберите параметр политики сети. Параметр политики нельзя изменить после создания кластера:

* Реализация Azure собственный, вызван *Azure сетевые политики*.
* *Сетевые политики Кошка черепахового*, сеть открытым исходным кодом и безопасности сети, основал по [Tigera][tigera].

Обе реализации использовать Linux *IPTables* для принудительного применения указанной политики. Политики преобразуются в набор разрешенных и запрещенных пары "IP-адрес". Затем эти пары программируются как фильтр правил IPTable.

Политика сети работает только с параметром CNI Azure (Дополнительно). Реализация отличается для двух параметров:

* *Azure сетевые политики* -Azure CNI настраивает моста в узле виртуальной Машины для работы в сети внутри узла. Правила фильтрации применяются в том случае, когда пакеты проходят через мост.
* *Сетевые политики Кошка черепахового* -Azure CNI настраивает локального ядра маршруты для трафика внутри узла. Применяются политики, в сетевом интерфейсе pod.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Различия между Azure и Кошка черепахового политик и их возможности

| Функция                               | Таблицы Azure                      | Кошка черепахового                      |
|------------------------------------------|----------------------------|-----------------------------|
| Поддерживаемые платформы                      | Linux                      | Linux                       |
| Поддерживаемые варианты сетевого взаимодействия             | Azure CNI                  | Azure CNI                   |
| Совместимости со спецификацией Kubernetes | Поддерживаются все типы политики |  Поддерживаются все типы политики |
| Дополнительные функции                      | Нет                       | Расширенная модель политики, состоящие из глобальной политики сети, задать глобальные сети и конечной точки узла. Дополнительные сведения об использовании `calicoctl` CLI для управления эти расширенные функции, см. в разделе [Справочник по пользовательскому calicoctl][calicoctl]. |
| Поддержка                                  | Поддерживаемые поддержки Azure и группы разработчиков | Поддержка сообщества Кошка черепахового. Дополнительные сведения о дополнительных платной поддержки, см. в разделе [варианты поддержки Кошка черепахового проекта][calico-support]. |
| Ведение журналов                                  | Правила добавления / удаления в IPTables регистрируются на каждом узле в разделе */var/log/azure-npm.log* | Дополнительные сведения см. в разделе [Кошка черепахового компонент журналы][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Создание кластера AKS и включение политики сети

Чтобы просмотреть политики сети в действии, давайте создать, а затем развернуть политику, определяющую поток трафика:

* запрет любого трафика к pod;
* разрешение трафика на основе меток pod;
* разрешение трафика на основе пространства имен.

Во-первых давайте создадим кластер AKS, которая поддерживает политики сети. Функция политики сети можно включить только в том случае, при создании кластера. Вы не можете включить политику сети в существующем кластере AKS.

Чтобы использовать политики сети с кластером AKS, необходимо использовать [Azure подключаемом модуле CNI] [ azure-cni] и определить собственные виртуальной сети и подсети. Более подробные сведения о том, как планировать необходимые диапазоны подсетей, см. в разделе [Настройка сети Azure CNI в Службе Azure Kubernetes (AKS)][use-advanced-networking].

Ниже приведен пример сценария, который выполняет следующее:

* Создает виртуальную сеть и подсеть.
* Создает субъект-службу для использования Azure Active Directory (Azure AD) с кластером AKS.
* Назначает субъекту-службе кластера AKS разрешения *Участник* для виртуальной сети.
* Создает кластер AKS в определенной виртуальной сети и включает политики сети.
    * *Azure* используется параметр политики сети. Чтобы использовать Кошка черепахового как параметр политики сети, используйте `--network-policy calico` параметра.

Укажите собственный безопасный пароль *SP_PASSWORD*. Вы можете заменить *имя_группы_ресурсов* и *CLUSTER_NAME* переменных:

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
SP_ID=$(az ad sp create-for-rbac --password $SP_PASSWORD --skip-assignment --query [appId] -o tsv)

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

Создание кластера занимает несколько минут. Когда кластер будет готов, настройте `kubectl` для подключения к кластеру Kubernetes с помощью [az aks get-credentials] [ az-aks-get-credentials] команды. Эта команда скачивает учетные данные и настраивает интерфейс командной строки Kubernetes для их использования.

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Запрещение всего входящего трафика pod

Прежде чем определить правила, разрешающие определенный сетевой трафик, создайте политику сети, чтобы запретить весь трафик. Эта политика предоставляет начальную точку для список разрешений только нужный трафик. Кроме того, вы увидите, как трафик блокируется при применении политики сети.

Пример приложения среды и правила трафика, давайте сначала создать пространство имен с именем *разработки* для запуска примера модулей POD:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Создайте pod серверной части примера, который запускает NGINX. Этот модуль серверной части можно использовать для имитации пример серверной части веб-приложения. Создайте этот pod в пространстве имен *development* и откройте порт *80* для обслуживания веб-трафика. Добавьте к pod метку с помощью *app=webapp,role=backend*. Так мы сможем указать его в политике сети в приведенном ниже разделе.

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Создать другой модуль и подключить сеанс терминала, чтобы проверить, что может успешно связаться с веб-страница NGINX по умолчанию:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

В командной строке оболочки, используйте `wget` для подтверждения того, что можно получить доступ к веб-страница NGINX по умолчанию:

```console
wget -qO- http://backend
```

Следующий пример выходных данных показано, что возвращены веб-страница NGINX по умолчанию:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Выйдите из подключенного сеанса терминала. Модуль теста автоматически удаляется.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Создание и применение политики сети

Теперь, когда вы подтвердите, что можно использовать базовый веб-страницу NGINX в pod серверной части примера, создайте политику сети, чтобы запретить весь трафик. Создайте файл `backend-policy.yaml` и вставьте в него следующий манифест YAML. Использует этот манифест *podSelector* будет применена политика для модулей, которые имеют *app:webapp, роль: серверной части* метки, как образец модуля NGINX. Правила в разделе *ingress* не определены, поэтому запрещен весь входящий трафик к pod.

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

Применение политики сети с помощью [применить kubectl] [ kubectl-apply] команду и укажите имя yaml-ФАЙЛ манифеста:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Проверка политики сети


Давайте посмотрим, если на веб-страницу NGINX в pod серверной части можно использовать снова. Создайте еще один тестовый pod и подключите сеанс терминала.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

В командной строке оболочки, используйте `wget` ли доступ к веб-страница NGINX по умолчанию. На этот раз установите значение времени ожидания в *2* секунды. Политики сети теперь блокирует весь входящий трафик, поэтому не удается загрузить страницу, как показано в следующем примере:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Выйдите из подключенного сеанса терминала. Модуль теста автоматически удаляется.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Разрешение входящего трафика на основе метки pod

В предыдущем разделе было запланировано pod серверной части NGINX и политику сети был создан, чтобы запретить весь трафик. Давайте создания pod переднего плана и обновление политики сети, чтобы разрешить трафик из интерфейсной модулей.

Обновите политику сети, чтобы разрешить трафик от контейнеров pod с метками *app:webapp,role:frontend* в любом пространстве имен. Изменить предыдущий *серверной части policy.yaml* файла и добавьте *matchLabels* правила входящего трафика, чтобы манифест выглядит как в следующем примере:

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
> Эта политика сети использует элементы *namespaceSelector* и *podSelector* для правила входящего трафика. Синтаксис YAML важно для правила входящего трафика, чтобы быть аддитивными. В этом примере оба элемента должны совпадать для применения правила входящего трафика. Версии Kubernetes, предшествующие *1.12* не сможет правильно интерпретировать эти элементы и ограничить сетевой трафик, как ожидается. Дополнительные сведения об этом поведении см. в разделе [поведение и из селекторы][policy-rules].

Применение политики обновленные сети с помощью [применить kubectl] [ kubectl-apply] команду и укажите имя yaml-ФАЙЛ манифеста:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

Запланировать группу pod, помечается как *app = веб-приложение, роль = внешнего интерфейса* и подключите сеанс терминала:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

В командной строке оболочки, используйте `wget` ли доступ к веб-страница NGINX по умолчанию:

```console
wget -qO- http://backend
```

Так как правила входящего трафика разрешает трафик с помощью модулей, имеющих метки *приложения: веб-приложение, роль: frontend*, трафик из внешнего интерфейса модуля. В следующем примере выходных данных показано, возвращаемых веб-страница NGINX по умолчанию:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Выйдите из подключенного сеанса терминала. Модуль автоматически удаляется.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Тестирование pod без соответствующей метки

Политика сети разрешает трафик от контейнеров pod с метками *app:webapp,role:frontend*, но должна блокировать весь остальной трафик. Давайте протестируем ли доступ к другой стручку без эти метки модуля серверной части NGINX. Создайте еще один тестовый pod и подключите сеанс терминала.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

В командной строке оболочки, используйте `wget` ли доступ к веб-страница NGINX по умолчанию. Политики сети блокирует входящий трафик, поэтому не удается загрузить страницу, как показано в следующем примере:

```console
$ wget -qO- --timeout=2 http://backend

wget: download timed out
```

Выйдите из подключенного сеанса терминала. Модуль теста автоматически удаляется.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Разрешение трафика, передаваемого в пределах заданного пространства имен

В предыдущих примерах вы создали политику сети, который запрещен весь трафик, а затем обновить политику, чтобы разрешить трафик из модулей POD с определенной меткой. Чтобы ограничить трафик только в пределах указанного пространства имен является другой часто возникает необходимость. Если для трафика в предыдущих примерах *разработки* пространства имен, создать политику сети, которая предотвращает трафик из другого пространства имен, такие как *рабочей*, попадание POD, содержащихся.

Сначала создайте пространство имен для имитации рабочего пространства имен.

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Запланируйте запуск тестового pod в пространстве имен *production*, которое помечено как *app=webapp,role=frontend*. Подключите сеанс терминала.

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

В командной строке оболочки, используйте `wget` для подтверждения того, что можно получить доступ к веб-страница NGINX по умолчанию:

```console
wget -qO- http://backend.development
```

Поскольку метки для pod совпадают, чем разрешено в настоящее время в политике сети, трафик разрешается. Политика сети проверяет не пространства имен, а метки pod. В следующем примере выходных данных показано, возвращаемых веб-страница NGINX по умолчанию:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Выйдите из подключенного сеанса терминала. Модуль теста автоматически удаляется.

```console
exit
```

### <a name="update-the-network-policy"></a>Обновление политики сети

Давайте обновим правило входящего трафика *namespaceSelector* раздел, чтобы разрешить только трафик из среды *разработки* пространства имен. Измените файл манифеста *backend-policy.yaml*, как показано в следующем примере.

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

В более сложные примеры можно определить несколько правил входящего трафика, например *namespaceSelector* и затем *podSelector*.

Применение политики обновленные сети с помощью [применить kubectl] [ kubectl-apply] команду и укажите имя yaml-ФАЙЛ манифеста:

```azurecli-interactive
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Тестирование обновленной политики сети

Запланировать другой pod в *рабочей* пространства имен и подключите сеанс терминала:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

В командной строке оболочки, используйте `wget` чтобы увидеть, что политики сети теперь запрещает трафик:

```console
$ wget -qO- --timeout=2 http://backend.development

wget: download timed out
```

Выйдите из тестового pod.

```console
exit
```

С трафиком, запрещать из *рабочей* пространства имен, расписание тестирования pod обратно в *разработки* пространства имен и подключите сеанс терминала:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

В командной строке оболочки, используйте `wget` чтобы увидеть, что политики сети разрешает трафик:

```console
wget -qO- http://backend
```

Трафик разрешается, поскольку pod запланировано в пространстве имен, что соответствует чем разрешено в политике сети. В следующем примере вывод примера возвращаются веб-страница NGINX по умолчанию:

```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Выйдите из подключенного сеанса терминала. Модуль теста автоматически удаляется.

```console
exit
```

## <a name="clean-up-resources"></a>Очистка ресурсов

В этой статье мы создали два пространства имен и применения политики сети. Для очистки этих ресурсов, используйте [удалить kubectl] [ kubectl-delete] команду и укажите имена ресурсов:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о сетевых ресурсах, см. в разделе [сети основные понятия для приложений в службе Azure Kubernetes (AKS)][concepts-network].

Дополнительные сведения о политиках см. в разделе [политик сети Kubernetes][kubernetes-network-policies].

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
