---
title: Preview — использовать подсистему балансировки нагрузки уровня "стандартный" в службе Azure Kubernetes (AKS)
description: Сведения об использовании подсистемы балансировки нагрузки с SKU "стандартный", чтобы предоставлять доступ к службам с помощью службы Azure Kubernetes (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/25/2019
ms.author: zarhoads
ms.openlocfilehash: a9cf3db3a15fab5a2f067a146950e02923a20379
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/29/2019
ms.locfileid: "67476807"
---
# <a name="preview---use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Preview — использовать подсистему балансировки нагрузки уровня "стандартный" в службе Azure Kubernetes (AKS)

Чтобы предоставить доступ к приложениям в службе Azure Kubernetes (AKS), можно создать и использовать Azure Load Balancer. Подсистемы балансировки нагрузки на AKS можно использовать как внутренний или внешний балансировщик нагрузки. Внутренний балансировщик нагрузки делает службу Kubernetes, доступный только для приложений, работающих в той же виртуальной сети, что и кластер AKS. Внешний балансировщик нагрузки получает один или несколько общедоступных IP-адресов для входящего трафика и делает доступным извне с помощью общедоступных IP-адресов службы Kubernetes.

Доступны два номера SKU Azure Load Balancer: ценовых категорий *Базовый* и *Стандартный*. По умолчанию *основные* SKU используется в том случае, когда манифест службы используется для создания подсистемы балансировки нагрузки в AKS. С помощью *стандартный* SKU подсистемы балансировки нагрузки предоставляет дополнительные возможности и функции, такие как размер пула серверной части и зоны доступности. Очень важно, что вы понимаете различия между *стандартный* и *основные* подсистемы балансировки нагрузки перед выбором для использования. После создания кластера AKS, нельзя изменить SKU балансировщика нагрузки для этого кластера. Дополнительные сведения о *основные* и *стандартный* номера SKU, см. в разделе [сравнение Конфигураций подсистемы балансировки нагрузки Azure][azure-lb-comparison].

В этой статье показано, как создать и использовать Azure Load Balancer с *стандартный* SKU со службой Azure Kubernetes (AKS).

В этой статье требуется понимание основных концепций Kubernetes и Azure Load Balancer. Дополнительные сведения см. в разделе [Kubernetes ключевые понятия для службы Azure Kubernetes (AKS)][kubernetes-concepts] and [What is Azure Load Balancer?][azure-lb].

Эта функция в настоящее время находится на стадии предварительной версии.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, здесь предполагается, что вы используете Azure CLI версии 2.0.59 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][install-azure-cli].

## <a name="before-you-begin"></a>Перед началом работы

Основной службы кластера AKS требуется разрешение на управление сетевыми ресурсами, при использовании существующей подсетью или группе ресурсов. Как правило, назначить *участник сетей* роль субъекту-службе от полномочного ресурсов. Дополнительные сведения о разрешениях см. в разделе [AKS делегата доступ к другим ресурсам Azure][aks-sp].

Необходимо создать кластер AKS, который задает номер SKU для подсистемы балансировки нагрузки для *стандартный* вместо значения по умолчанию *основные*. Создание кластера AKS рассматривается позже, но сначала необходимо включить несколько функций предварительной версии.

> [!IMPORTANT]
> Функции предварительной версии AKS: самообслуживания, согласиться. Они предназначены для сбора отзывов и ошибки нашего сообщества. В предварительной версии эти функции не предназначены для использования в рабочей среде. Функции в общедоступной предварительной версии подпадают под поддержки «оптимальных затрат». Помощь от групп разработчиков AKS Техническая поддержка доступна во время рабочих часов тихоокеанского часового пояса (по тихоокеанскому времени) только. Дополнительные сведения обратитесь в следующие справочные статьи:
>
> * [Политики поддержки AKS][aks-support-policies]
> * [Часто задаваемые вопросы о поддержке Azure][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Установка расширения интерфейса командной строки aks-preview

Чтобы использовать Azure load balancer SKU "стандартный", вам потребуется *предварительной версии aks* CLI версия расширения 0.4.1 или более поздней версии. Установка *предварительной версии aks* расширение Azure CLI с помощью [добавить расширения az][az-extension-add] command, then check for any available updates using the [az extension update][az-extension-update] команда::

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-aksazurestandardloadbalancer-preview-feature"></a>Зарегистрировать AKSAzureStandardLoadBalancer предварительной версии функции

Чтобы создать кластер AKS, можно использовать подсистему балансировки нагрузки с помощью *стандартный* SKU, необходимо включить *AKSAzureStandardLoadBalancer* компонентов флаг в вашей подписке. *AKSAzureStandardLoadBalancer* компонентов также использует *VMSSPreview* при создании кластера с помощью масштабируемых наборов виртуальных машин. Этот компонент предоставляет последний набор улучшений службы при настройке кластера. Хотя это не обязательно, рекомендуется включить *VMSSPreview* компонентов также флаг.

> [!CAUTION]
> При регистрации компонента для подписки, вы не можете сейчас отменить регистрацию этой функции. После включения некоторые возможности предварительной версии, можно использовать значения по умолчанию для всех кластеров AKS, затем создается в подписке. Не включайте функции предварительной версии на рабочие подписки. Используйте отдельную подписку для тестирования функции предварительной версии и собирайте отзывы.

Зарегистрировать *VMSSPreview* и *AKSAzureStandardLoadBalancer* флаги компонентов с помощью [az функция register][az-feature-register] команды, как показано в следующем примере:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "VMSSPreview"
az feature register --namespace "Microsoft.ContainerService" --name "AKSAzureStandardLoadBalancer"
```

> [!NOTE]
> Создается после успешной регистрации кластера AKS *VMSSPreview* или *AKSAzureStandardLoadBalancer* флагов компонентов используйте этой предварительной версии кластера. Чтобы продолжить создавать кластеры обычный, полностью поддерживаются, не включите функции предварительной версии на рабочие подписки. Используйте отдельный тестовый или разработка подписки Azure для тестирования функции предварительной версии.

Через несколько минут отобразится состояние *Registered* (Зарегистрировано). Вы можете проверить состояние регистрации с помощью [список функций az][az-feature-list] команды:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
```

Когда все будет готово, обновить регистрацию *Microsoft.ContainerService* поставщик ресурсов с помощью [az provider register][az-provider-register] команды:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Ограничения

Применяются следующие ограничения, касающиеся создания и управление кластерами AKS, которые поддерживают подсистему балансировки нагрузки с помощью *стандартный* SKU:

* При использовании *стандартный* SKU для load balancer, необходимо разрешить общедоступные адреса и не рекомендуется создавать любую политику Azure, эти создания IP-адрес. Автоматически создает кластер AKS *стандартный* SKU общедоступного IP-адреса в одной группе ресурсов, созданных для кластера AKS, который обычно называется с *MC_* в начале. Общедоступный IP-адресов и назначает AKS *стандартный* SKU подсистемы балансировки нагрузки. Общедоступный IP-адрес необходим для разрешения исходящий трафик из кластера AKS. Этот общедоступный IP-адрес также требуется для поддержки связи между плоскости и агента узлы управления также для сохранения совместимости с предыдущими версиями AKS.
* При использовании *стандартный* SKU для подсистемы балансировки нагрузки, необходимо использовать Kubernetes версии 1.13.5 или более поздней версии.

Пока эта функция находится на этапе предварительной версии, применяются следующие дополнительные ограничения.

* При использовании *стандартный* SKU для load balancer в AKS, невозможно задать собственный общедоступный IP-адрес для исходящего трафика для балансировщика нагрузки. Необходимо использовать IP-адрес, назначает AKS для подсистемы балансировки нагрузки.

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов Azure — это логическая группа, в которой выполняется развертывание и администрирование ресурсов Azure. Во время создания группы ресурсов вам будет предложено указать расположение. В этом расположении сохраняются метаданные группы ресурсов, а также выполняется их работа в Azure, если во время создания ресурса вы не указали другой регион. Создайте группу ресурсов с помощью команды [az group create][az-group-create].

В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

В следующем примере выходных данных показано, что группа ресурсов успешно создана:

```json
{
  "id": "/subscriptions/<guid>/resourceGroups/myResourceGroup",
  "location": "eastus",
  "managedBy": null,
  "name": "myResourceGroup",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

## <a name="create-aks-cluster"></a>Создание кластера AKS
Для запуска кластера AKS, поддерживающий подсистему балансировки нагрузки с помощью *стандартный* в конфигурации кластера требуется задать *load-balancer-sku* параметр *стандартный*. Этот параметр создает подсистему балансировки нагрузки с помощью *стандартный* SKU при создании кластера. При запуске *LoadBalancer* службы в кластере, конфигурацию *стандартный* подсистемы балансировки нагрузки SK обновляется с помощью конфигурации службы. Используйте [создать az aks][az-aks-create] команду, чтобы создать кластер службы контейнеров AZURE с именем *myAKSCluster*.

> [!NOTE]
> *Load-balancer-sku* свойство может использоваться только при создании кластера. Номер SKU подсистемы балансировки нагрузки невозможно изменить после создания кластера AKS. Кроме того можно использовать только один тип номера SKU подсистемы балансировки нагрузки в одном кластере.

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-vmss \
    --node-count 1 \
    --kubernetes-version 1.14.0 \
    --load-balancer-sku standard \
    --generate-ssh-keys
```

Через несколько минут выполнение команды завершается и отображаются сведения о кластере в формате JSON.

## <a name="connect-to-the-cluster"></a>Подключение к кластеру

Управлять кластером Kubernetes можно использовать [kubectl][kubectl], клиент командной строки Kubernetes. Если вы используете Azure Cloud Shell, `kubectl` уже установлен. Чтобы установить `kubectl` локально, используйте [az aks install-cli][az-aks-install-cli] команды:

```azurecli
az aks install-cli
```

Чтобы настроить `kubectl` на подключение к кластеру Kubernetes, выполните команду [az aks get-credentials][az-aks-get-credentials]. Эта команда скачивает учетные данные и настраивает интерфейс командной строки Kubernetes для их использования.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Проверьте подключение к кластеру, выполнив команду [kubectl get][kubectl-get], чтобы просмотреть список узлов кластера.

```azurecli-interactive
kubectl get nodes
```

В следующем примере показан единый узел, созданный на предыдущих шагах. Убедитесь, что узел находится в состоянии *Ready* (Готово):

```
NAME                       STATUS   ROLES   AGE     VERSION
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.14.0
```

## <a name="verify-your-cluster-uses-the-standard-sku"></a>Проверьте ваш кластер использует *стандартный* SKU

Используйте [az aks show][az-aks-show] для отображения конфигурации кластера.

```console
$ az aks show --resource-group myResourceGroup --name myAKSCluster

{
  "aadProfile": null,
  "addonProfiles": null,
   ...
   "networkProfile": {
    "dnsServiceIp": "10.0.0.10",
    "dockerBridgeCidr": "172.17.0.1/16",
    "loadBalancerSku": "standard",
    ...
```

Проверьте *loadBalancerSku* показывает, как *стандартный*.

## <a name="use-the-load-balancer"></a>Использовать подсистему балансировки нагрузки

Чтобы использовать подсистему балансировки нагрузки в кластере, создайте манифест службы с типом службы *LoadBalancer*. Чтобы отобразить работы подсистемы балансировки нагрузки, создайте другой манифест с помощью примера приложения для запуска в кластере. В этом образце приложения предоставляется через подсистему балансировки нагрузки, а также можно просмотреть с помощью браузера.

Создание манифеста с именем `sample.yaml` как показано в следующем примере:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-back
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-back
  template:
    metadata:
      labels:
        app: azure-vote-back
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-back
        image: redis
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 6379
          name: redis
---
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-back
spec:
  ports:
  - port: 6379
  selector:
    app: azure-vote-back
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: azure-vote-front
spec:
  replicas: 1
  selector:
    matchLabels:
      app: azure-vote-front
  template:
    metadata:
      labels:
        app: azure-vote-front
    spec:
      nodeSelector:
        "beta.kubernetes.io/os": linux
      containers:
      - name: azure-vote-front
        image: microsoft/azure-vote-front:v1
        resources:
          requests:
            cpu: 100m
            memory: 128Mi
          limits:
            cpu: 250m
            memory: 256Mi
        ports:
        - containerPort: 80
        env:
        - name: REDIS
          value: "azure-vote-back"
```

Манифест выше настраивает два развертывания: *azure-vote-front* и *azure-vote-back*. Чтобы настроить *azure-vote-front* развертывания должен быть предоставлен с помощью подсистемы балансировки нагрузки, создайте манифест с именем `standard-lb.yaml` как показано в следующем примере:

```yaml
apiVersion: v1
kind: Service
metadata:
  name: azure-vote-front
spec:
  type: LoadBalancer
  ports:
  - port: 80
  selector:
    app: azure-vote-front
```

Служба *azure-vote-front* использует *LoadBalancer* тип для настройки подсистемы балансировки нагрузки в кластере AKS для подключения к *azure-vote-front* развертывания.

Развертывание примера приложения и загрузки с помощью подсистемы балансировки [kubectl применить][kubectl-apply] и укажите имя манифесты YAML:

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

*Стандартный* SKU подсистемы балансировки нагрузки теперь настроен для предоставления образца приложения. Просмотр сведений о службе *azure-vote-front* с помощью [kubectl get][kubectl-get] для просмотра общедоступного IP-адреса подсистемы балансировки нагрузки. Общедоступный IP-адрес подсистемы балансировки нагрузки отображается в *EXTERNAL-IP* столбца. Может занять одну-две IP-адреса для изменения из минуты *\<ожидающие\>* фактический внешний IP-адрес, как показано в следующем примере:

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

Перейдите на общедоступный IP-адрес в браузере и убедитесь, что вы видите пример приложения. В приведенном выше примере общедоступный IP-адрес является `52.179.23.131`.

![Изображение перехода к приложению Azure для голосования](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> Можно также настроить как внутренний балансировщик нагрузки и не предоставляют общедоступный IP-адрес. Чтобы настроить подсистему балансировки нагрузки как внутренние, добавьте `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` как заметки к *Подсистема балансировки нагрузки* службы. Вы увидите пример yaml манифеста, а также дополнительные сведения о внутреннего балансировщика нагрузки [здесь][internal-lb-yaml].

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>Очистить конфигурацию подсистемы балансировки нагрузки уровня "стандартный"

Чтобы удалить образец приложения и загрузка конфигурации подсистемы балансировки, используйте [kubectl удалить][kubectl-delete]:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о службах Kubernetes в [документации по службам Kubernetes][kubernetes-services].

<!-- LINKS - External -->
[kubectl]: https://kubernetes.io/docs/user-guide/kubectl/
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/
[aks-engine]: https://github.com/Azure/aks-engine

<!-- LINKS - Internal -->
[advanced-networking]: configure-azure-cni.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[aks-sp]: kubernetes-service-principal.md#delegate-access-to-other-azure-resources
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-aks-install-cli]: /cli/azure/aks?view=azure-cli-latest#az-aks-install-cli
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-group-create]: /cli/azure/group#az-group-create
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
