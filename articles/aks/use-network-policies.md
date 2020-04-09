---
title: Безопасный трафик стручка с сетевой политикой
titleSuffix: Azure Kubernetes Service
description: Узнайте, как обезопасить трафик, который вливается и выходит из стручков, используя сетевые политики Kubernetes в службе Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 05/06/2019
ms.openlocfilehash: 01ba9e7353b6783d1b4fd1649291a64405fd9382
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886710"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Защита трафика между контейнерами pod с использованием политик сети в Службе Azure Kubernetes (AKS)

При выполнении современных приложений на базе микрослужб в Kubernetes часто требуется управлять тем, какие компоненты могут взаимодействовать друг с другом. Принцип наименьших привилегий должен применяться к тому, как трафик может проходить между стручками в кластере Azure Kubernetes Service (AKS). Допустим, вы, вероятно, хотите, чтобы заблокировать трафик непосредственно на бэк-энд приложений. Функция *сетевой политики* в Kubernetes позволяет определить правила входа и накопительного трафика между стручками в кластере.

В этой статье показано, как установить движок сетевой политики и создать сетевые политики Kubernetes для управления потоком трафика между стручками в AKS. Сетевая политика должна использоваться только для узлов и стручков на основе Linux в AKS.

## <a name="before-you-begin"></a>Подготовка к работе

Вам нужна версия Azure CLI 2.0.61 или более поздняя установка и настройка. Чтобы узнать версию, выполните команду  `az --version`. Если вам необходимо выполнить установку или обновление, см. статью  [Установка Azure CLI][install-azure-cli].

> [!TIP]
> Если вы использовали функцию сетевой политики во время предварительного просмотра, мы рекомендуем [создать новый кластер.](#create-an-aks-cluster-and-enable-network-policy)
> 
> Если вы хотите продолжить использование существующих тестовых кластеров, которые использовали сетевую политику во время предварительного просмотра, обновите кластер до новой версии Kubernetes для последнего выпуска GA, а затем развернуть следующий манифест YAML для исправления сервера сбоев метрик и панели мониторинга Kubernetes. Это исправление требуется только для кластеров, которые использовали движок сетевой политики Calico.
>
> В качестве наилучшей практики безопасности [просмотрите содержимое этого манифеста YAML,][calico-aks-cleanup] чтобы понять, что развертывается в кластере AKS.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Общие сведения о политике сети

Все стручки в кластере AKS могут отправлять и получать трафик без ограничений, по умолчанию. Для повышения безопасности вы можете определить правила, управляющие потоком трафика. Приложения бэк-энда часто подвергаются воздействию только необходимых фронтовых служб, например. Или компоненты базы данных доступны только для уровней приложений, которые подключаются к ним.

Network Policy — это спецификация Kubernetes, определяющая политики доступа для связи между стручками. Используя сетевые политики, вы определяете упорядоченный набор правил для отправки и получения трафика и применяете их к коллекции стручков, которые соответствуют одному или несколько селекторам меток.

Эти правила сетевой политики определяются как манифесты YAML. Сетевые политики могут быть включены в более широкий манифест, который также создает развертывание или службу.

### <a name="network-policy-options-in-aks"></a>Варианты сетевой политики в AKS

Azure предоставляет два способа реализации сетевой политики. При создании кластера AKS вы выбираете опцию сетевой политики. Опция политики не может быть изменена после создания кластера:

* Собственная реализация Azure называется *сетевыми политиками Azure.*
* *Политика сети Calico*, сетевое решение с открытым исходным кодом и сетевой безопасности, основанное [Tigera][tigera].

Обе реализации используют Linux *IPTables* для обеспечения соблюдения указанных политик. Политики переводятся в наборы разрешенных и запрещенных IP-пар. Затем эти пары запрограммированы в виде правил фильтра IPTable.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Различия между политиками Azure и Calico и их возможностями

| Функция                               | Azure                      | Калико                      |
|------------------------------------------|----------------------------|-----------------------------|
| Поддерживаемые платформы                      | Linux                      | Linux                       |
| Поддерживаемые сетевые опции             | Лазурный CNI                  | Azure CNI и кубенет       |
| Соответствие спецификации Kubernetes | Все поддерживаемые типы политик |  Все поддерживаемые типы политик |
| Дополнительные функции                      | Отсутствуют                       | Расширенная модель политики, состоящая из глобальной сетевой политики, глобального сета сети и конечной точки хоста. Для получения дополнительной `calicoctl` информации об использовании CLI для управления этими расширенными функциями, см. [calicoctl user reference][calicoctl] |
| Поддержка                                  | При поддержке группы поддержки и инженерного обеспечения Azure | Поддержка сообщества Calico. Для получения дополнительной информации [Project Calico support options][calico-support]о дополнительной платной поддержке см. |
| Logging                                  | Правила, добавленные/ удаленные в IPTables, регистрируются на каждом хосте под */var/log/azure-npm.log* | Для получения дополнительной [информации][calico-logs] см. |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Создание кластера AKS и включение политики сети

Чтобы увидеть сетевые политики в действии, давайте создадим и расширим политику, определяющую поток трафика:

* запрет любого трафика к pod;
* разрешение трафика на основе меток pod;
* разрешение трафика на основе пространства имен.

Во-первых, давайте создадим кластер AKS, поддерживающий сетевую политику. 

> [!IMPORTANT]
>
> Функция сетевой политики может быть включена только при создании кластера. Вы не можете включить политику сети в существующем кластере AKS.

Чтобы использовать сетевую политику Azure, необходимо использовать [плагин Azure CNI][azure-cni] и определить собственную виртуальную сеть и подсети. Более подробные сведения о том, как планировать необходимые диапазоны подсетей, см. в разделе [Настройка сети Azure CNI в Службе Azure Kubernetes (AKS)][use-advanced-networking]. Сетевая политика Calico может использоваться либо с этим же плагином Azure CNI, либо с плагином Kubenet CNI.

Ниже приведен пример сценария, который выполняет следующее:

* Создает виртуальную сеть и подсеть.
* Создает принцип службы Active Directory (Azure AD) для использования в кластере AKS.
* Назначает субъекту-службе кластера AKS разрешения *Участник* для виртуальной сети.
* Создает кластер AKS в определенной виртуальной сети и позволяет проводить сетевую политику.
    * Используется параметр политики *лазурной* сети. Вместо этого воспользуйтесь Calico в `--network-policy calico` качестве опции сетевой политики, используйте параметр. Примечание: Calico может быть `--network-plugin azure` `--network-plugin kubenet`использован с любым или .

Укажите собственный безопасный пароль *SP_PASSWORD*. Вы можете заменить переменные *RESOURCE_GROUP_NAME* и *CLUSTER_NAME:*

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

Создание кластера занимает несколько минут. Когда кластер будет готов, `kubectl` наверсоняйте для подключения к кластеру Kubernetes с помощью команды [az aks get-credentials.][az-aks-get-credentials] Эта команда скачивает учетные данные и настраивает интерфейс командной строки Kubernetes для их использования.

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Запрещение всего входящего трафика pod

Прежде чем определить правила, разрешающие определенный сетевой трафик, создайте политику сети, чтобы запретить весь трафик. Эта политика дает вам отправную точку, чтобы начать в белый список только желаемого трафика. Кроме того, вы увидите, как трафик блокируется при применении политики сети.

Для среды применения образца и правил дорожного движения давайте сначала создадим пространство имен, называемое *разработкой,* для запуска примерных стручков:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Создайте пример бэк-энда стручка, который работает NGINX. Этот задний конец стручка может быть использован для имитации образца задней веб-приложений. Создайте этот pod в пространстве имен *development* и откройте порт *80* для обслуживания веб-трафика. Добавьте к pod метку с помощью *app=webapp,role=backend*. Так мы сможем указать его в политике сети в приведенном ниже разделе.

```console
kubectl run backend --image=nginx --labels app=webapp,role=backend --namespace development --expose --port 80 --generator=run-pod/v1
```

Создайте еще один модуль и приложите сеанс терминала для проверки, что вы можете успешно достичь веб-страницы NGINX по умолчанию:

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

В запросе оболочки используйте `wget` для подтверждения того, что вы можете получить доступ к веб-странице NGINX по умолчанию:

```console
wget -qO- http://backend
```

Следующий вывод выборки показывает, что веб-страница NGINX по умолчанию вернулась:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Выйдите из подключенного сеанса терминала. Тестовый модуль автоматически удаляется.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Создание и применение политики сети

Теперь, когда вы подтвердили, что вы можете использовать базовую веб-страницу NGINX на примере бэк-энда стручка, создать сетевую политику, чтобы отказать в трафике. Создайте файл `backend-policy.yaml` и вставьте в него следующий манифест YAML. Этот манифест использует *podSelector,* чтобы прикрепить политику к стручкам, которые имеют *ярлык app:webapp,role:backend,* как и ваш образец стручка NGINX. Правила в разделе *ingress* не определены, поэтому запрещен весь входящий трафик к pod.

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

Перейдите [https://shell.azure.com](https://shell.azure.com) к открытию облачной оболочки Azure в браузере.

Примените сетевой политики, используя команду [kubectl применить][kubectl-apply] и указать имя вашего манифеста YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Проверка политики сети

Давайте посмотрим, если вы можете использовать веб-страницу NGINX на задней части стручка снова. Создайте еще один тестовый pod и подключите сеанс терминала.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

В запросе оболочки используйте, `wget` чтобы увидеть, если вы можете получить доступ к веб-странице NGINX по умолчанию. На этот раз установите значение времени ожидания в *2* секунды. Сетевая политика теперь блокирует весь входящий трафик, поэтому страница не может быть загружена, как показано в следующем примере:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Выйдите из подключенного сеанса терминала. Тестовый модуль автоматически удаляется.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Разрешение входящего трафика на основе метки pod

В предыдущем разделе был запланирован подход NGINX с задним приводом, и была создана сетевая политика, чтобы отказать в трафике. Давайте создадим фронтовой стручок и обновим сетевую политику, чтобы обеспечить трафик с передних стручков.

Обновите политику сети, чтобы разрешить трафик от контейнеров pod с метками *app:webapp,role:frontend* в любом пространстве имен. Отодемонстрируйте предыдущий файл *backend-policy.yaml* и добавьте правила вхобры *совпадений,* чтобы ваш манифест выглядел следующим примером:

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
> Эта политика сети использует элементы *namespaceSelector* и *podSelector* для правила входящего трафика. Синтаксис YAML важен для того, чтобы правила входа были добавками. В этом примере оба элемента должны совпадать для применения правила входящего трафика. Версии Kubernetes до *1.12* могут неправильно интерпретировать эти элементы и ограничить сетевой трафик, как вы ожидаете. Для получения дополнительной информации об этом [поведении, см.][policy-rules]

Примените обновленную сетевую политику, используя команду [kubectl и][kubectl-apply] укажите название манифеста YAML:

```console
kubectl apply -f backend-policy.yaml
```

Расписание стручка, который помечен как *app'webapp,role-frontend* и прикрепите сеанс терминала:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

В запросе оболочки используйте, `wget` чтобы увидеть, если вы можете получить доступ к веб-странице NGINX по умолчанию:

```console
wget -qO- http://backend
```

Потому что вход правило позволяет трафик с стручками, которые имеют этикетки *приложение: webapp, роль: передний*, трафик с переднего конца стручка допускается. Следующий пример вывода показывает, что возвращенная веб-страница NGINX по умолчанию:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Выйдите из подключенного сеанса терминала. Стручок автоматически удаляется.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Тестирование pod без соответствующей метки

Политика сети разрешает трафик от контейнеров pod с метками *app:webapp,role:frontend*, но должна блокировать весь остальной трафик. Давайте проверим, может ли другой стручок без этих меток получить доступ к подкоженому NGINX. Создайте еще один тестовый pod и подключите сеанс терминала.

```console
kubectl run --rm -it --image=alpine network-policy --namespace development --generator=run-pod/v1
```

В запросе оболочки используйте, `wget` чтобы увидеть, если вы можете получить доступ к веб-странице NGINX по умолчанию. Политика сети блокирует входящий трафик, поэтому страница не может быть загружена, как показано в следующем примере:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Выйдите из подключенного сеанса терминала. Тестовый модуль автоматически удаляется.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Разрешение трафика, передаваемого в пределах заданного пространства имен

В предыдущих примерах вы создали сетевую политику, которая отклоняет весь трафик, а затем обновили политику, чтобы разрешить трафик из стручков с определенной меткой. Другая распространенная потребность заключается в ограничении трафика только в пределах данного пространства имен. Если предыдущие примеры были для трафика в пространстве имен *разработки,* создайте сетевую политику, которая предотвращает трафик из другого пространства имен, например *производственного,* от достижения стручков.

Сначала создайте пространство имен для имитации рабочего пространства имен.

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Запланируйте запуск тестового pod в пространстве имен *production*, которое помечено как *app=webapp,role=frontend*. Подключите сеанс терминала.

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

В запросе оболочки используйте `wget` для подтверждения того, что вы можете получить доступ к веб-странице NGINX по умолчанию:

```console
wget -qO- http://backend.development
```

Поскольку метки для стручка соответствуют тому, что в настоящее время разрешено в сетевой политике, трафик разрешен. Политика сети проверяет не пространства имен, а метки pod. Следующий пример вывода показывает, что возвращенная веб-страница NGINX по умолчанию:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Выйдите из подключенного сеанса терминала. Тестовый модуль автоматически удаляется.

```console
exit
```

### <a name="update-the-network-policy"></a>Обновление политики сети

Давайте обновим раздел *namespacespacespaceselector* правила входа, чтобы разрешить трафик только из пространства имен *разработки.* Измените файл манифеста *backend-policy.yaml*, как показано в следующем примере.

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

В более сложных примерах можно определить несколько правил входа, *например, namespaceSelector,* а затем *podSelector.*

Примените обновленную сетевую политику, используя команду [kubectl и][kubectl-apply] укажите название манифеста YAML:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Тестирование обновленной политики сети

Запланируйте другую *капсулу* в производственном пространстве и прикрепите сеанс терминала:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace production --generator=run-pod/v1
```

В запросе оболочки используйте, `wget` чтобы увидеть, что сетевой политик теперь отрицает трафик:

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

Выйдите из тестового pod.

```console
exit
```

С трафиком, *production* отклоняемым из производственного пространства, запланируйте тестовый модуль обратно в пространство имен *разработки* и приложите сеанс терминала:

```console
kubectl run --rm -it frontend --image=alpine --labels app=webapp,role=frontend --namespace development --generator=run-pod/v1
```

В запросе оболочки используйте, `wget` чтобы увидеть, что сетевой политик позволяет трафик:

```console
wget -qO- http://backend
```

Трафик разрешен, поскольку стручок запланирован в пространстве имен, которое соответствует тому, что разрешено в сетевой политике. Следующий выход образца показывает возвращенную веб-страницу NGINX по умолчанию:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Выйдите из подключенного сеанса терминала. Тестовый модуль автоматически удаляется.

```console
exit
```

## <a name="clean-up-resources"></a>Очистка ресурсов

В этой статье мы создали два пространства имен и применили сетевую политику. Для очистки этих ресурсов используйте команду [удаления kubectl][kubectl-delete] и укажите имена ресурсов:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Дальнейшие действия

Подробнее о сетевых ресурсах можно узнать [в службах Azure Kubernetes Service (AKS).][concepts-network]

Чтобы узнать больше о политиках, [см.][kubernetes-network-policies]

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/v3.9/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/v3.9/maintenance/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-advanced-networking.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
