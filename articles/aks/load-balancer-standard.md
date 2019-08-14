---
title: Предварительный просмотр. Использование балансировщика нагрузки "Стандартный" в службе Kubernetes Azure (AKS)
description: Узнайте, как использовать подсистему балансировки нагрузки со стандартным SKU для предоставления служб службе Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 06/25/2019
ms.author: zarhoads
ms.openlocfilehash: b5bbc2951e10e132b407e1651a2c146cf22184a5
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/12/2019
ms.locfileid: "68949664"
---
# <a name="preview---use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Предварительный просмотр. Использование балансировщика нагрузки "Стандартный" в службе Kubernetes Azure (AKS)

Чтобы предоставить доступ к приложениям в Azure Kubernetes Service (AKS), можно создать и использовать Azure Load Balancer. Балансировщик нагрузки, выполняющийся в AKS, можно использовать в качестве внутренней или внешней подсистемы балансировки нагрузки. Внутренний балансировщик нагрузки делает службу Kubernetes доступной только для приложений, работающих в той же виртуальной сети, что и кластер AKS. Внешняя подсистема балансировки нагрузки получает один или несколько общедоступных IP-адресов для входящего трафика и делает службу Kubernetes доступной извне с помощью общедоступных IP-адресов.

Доступны два номера SKU Azure Load Balancer: ценовых категорий *Базовый* и *Стандартный*. По умолчанию используется *базовый* номер SKU, если манифест службы используется для создания балансировщика нагрузки в AKS. Использование подсистемы балансировки нагрузки уровня " *стандартный* " предоставляет дополнительные функции и функции, такие как размер внутреннего пула и зоны доступности. Важно понимать различия между *стандартными* и *базовыми* подсистемами балансировки нагрузки перед выбором используемого. После создания кластера AKS вы не сможете изменить SKU подсистемы балансировки нагрузки для этого кластера. Дополнительные сведения о SKU уровня " *базовый* " и " *стандартный* " см. в статье сравнение SKU в подсистеме [балансировки нагрузки Azure][azure-lb-comparison].

В этой статье показано, как создать и использовать Azure Load Balancer с SKU " *стандартный* " с помощью службы Azure Kubernetes Service (AKS).

В этой статье предполагается, что основное понимание Kubernetes и Azure Load Balancer концепций. Дополнительные сведения см. в разделе [Основные понятия Kubernetes Core для службы Kubernetes Azure (AKS)][kubernetes-concepts] и [что Azure Load Balancer?][azure-lb].

Эта функция в настоящее время находится на стадии предварительной версии.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для работы с этой статьей требуется Azure CLI версии 2.0.59 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][install-azure-cli].

## <a name="before-you-begin"></a>Перед началом работы

Субъекту-службе кластера AKS требуется разрешение для управления сетевыми ресурсами, если используется существующая подсеть или группа ресурсов. В общем случае назначьте роль *участника сети* субъекту-службе для делегированных ресурсов. Дополнительные сведения о разрешениях см. в статье [Делегирование AKS доступ к другим ресурсам Azure][aks-sp].

Необходимо создать кластер AKS, который задает для балансировщика нагрузки SKU *Standard* вместо *базового*по умолчанию. Создание кластера AKS рассматривается на более позднем этапе, но сначала необходимо включить несколько предварительных версий функций.

> [!IMPORTANT]
> Функции предварительной версии AKS — это самостоятельная служба. Они предоставляются для сбора отзывов и ошибок от нашего сообщества. В предварительной версии эти функции не предназначены для использования в рабочей среде. Функции в общедоступной предварительной версии доступны в рамках поддержки "наилучших усилий". Помощь из группы технической поддержки AKS можно получить только в течение часового пояса в рабочее время (PST). Дополнительные сведения см. в следующих статьях поддержки:
>
> * [Политики поддержки AKS][aks-support-policies]
> * [Часто задаваемые вопросы о поддержке Azure][aks-faq]

### <a name="install-aks-preview-cli-extension"></a>Установка расширения интерфейса командной строки aks-preview

Для использования SKU "Стандартный" балансировщика нагрузки Azure требуется расширение CLI *AKS-Preview* версии 0.4.1 или более поздней. Установите расширение Azure CLI *AKS-Preview* с помощью команды [AZ Extension Add][az-extension-add] , а затем проверьте наличие доступных обновлений с помощью команды [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-aksazurestandardloadbalancer-preview-feature"></a>Регистрация функции предварительной версии Аксазурестандардлоадбаланцер

Чтобы создать кластер AKS, который может использовать подсистему балансировки нагрузки со стандартным SKU, необходимо включить флаг функции *аксазурестандардлоадбаланцер* в подписке. Функция *аксазурестандардлоадбаланцер* также использует *вмсспревиев* при создании кластера с помощью масштабируемых наборов виртуальных машин. Эта функция предоставляет последний набор усовершенствований службы при настройке кластера. Хотя это и не является обязательным, рекомендуется включить флаг компонента *вмсспревиев* .

> [!CAUTION]
> Регистрация компонента в подписке в данный момент невозможна. После включения функций предварительной версии можно использовать значения по умолчанию для всех кластеров AKS, созданных в подписке. Не включайте предварительные версии функций в производственных подписках. Используйте отдельную подписку для тестирования функций предварительной версии и сбора отзывов.

Зарегистрируйте флаги компонентов *вмсспревиев* и *аксазурестандардлоадбаланцер* с помощью команды [AZ Feature Register][az-feature-register] , как показано в следующем примере:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "VMSSPreview"
az feature register --namespace "Microsoft.ContainerService" --name "AKSAzureStandardLoadBalancer"
```

> [!NOTE]
> Любой кластер AKS, созданный после успешной регистрации флагов функций *вмсспревиев* или *аксазурестандардлоадбаланцер* , используется для предварительной версии кластера. Чтобы продолжить создание обычных, полностью поддерживаемых кластеров, не включайте предварительные версии функций в производственных подписках. Используйте отдельную тестовую или новую подписку Azure для тестирования предварительных версий функций.

Через несколько минут отобразится состояние *Registered* (Зарегистрировано). Проверить состояние регистрации можно с помощью команды [AZ Feature List][az-feature-list] .

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/VMSSPreview')].{Name:name,State:properties.state}"
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSAzureStandardLoadBalancer')].{Name:name,State:properties.state}"
```

Когда все будет готово, обновите регистрацию поставщика ресурсов *Microsoft. ContainerService* с помощью команды [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

### <a name="limitations"></a>Ограничения

При создании кластеров AKS, поддерживающих подсистему балансировки нагрузки со стандартным SKU, применяются следующие ограничения.

* При использовании SKU " *стандартный* " для подсистемы балансировки нагрузки необходимо разрешить общедоступные адреса и избежать создания политики Azure, Ban создание IP-адресов. Кластер AKS автоматически создает общедоступный IP-адрес *стандартного* SKU в той же группе ресурсов, которая была создана для кластера AKS, который обычно называется с *MC_* в начале. AKS назначает общедоступный IP-адрес подсистеме балансировки нагрузки уровня " *стандартный* ". Общедоступный IP-адрес необходим для разрешения исходящего трафика из кластера AKS. Этот общедоступный IP-адрес также необходим для поддержания подключения между узлами "плоскость управления" и "агент", а также для обеспечения совместимости с предыдущими версиями AKS.
* При использовании SKU " *стандартный* " для подсистемы балансировки нагрузки необходимо использовать Kubernetes версии 1.13.5 или более поздней.
* При использовании [компонента общедоступного IP-адреса узла](use-multiple-node-pools.md#assign-a-public-ip-per-node-in-a-node-pool) со стандартными подсистемами балансировки нагрузки можно установить либо правило исходящего трафика SLB, либо общедоступный IP-адрес для узла. Необходимо выбрать один или другой, так как одна виртуальная машина одновременно не может быть подключена одновременно к исходящему и общедоступному IP-адресу SLB.

Хотя эта функция находится на этапе предварительной версии, применяются следующие дополнительные ограничения.

* При использовании SKU " *стандартный* " для подсистемы балансировки нагрузки в AKS нельзя задать собственный общедоступный IP-адрес для исходящего трафика балансировщика нагрузки. Необходимо использовать IP-адрес AKS, который назначается подсистеме балансировки нагрузки.

## <a name="create-a-resource-group"></a>Создать группу ресурсов

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
Чтобы запустить кластер AKS, который поддерживает подсистему балансировки нагрузки со *стандартным* SKU, кластеру необходимо установить для параметра *балансировки нагрузки-SKU* значение *Standard*. Этот параметр создает подсистему балансировки нагрузки с номером SKU " *стандартный* " при создании кластера. При запуске службы балансирования нагрузки в кластере конфигурация уровня " *стандартный* " подсистемы балансировки загрузки SK обновляется с конфигурацией службы. Используйте команду [AZ AKS Create][az-aks-create] , чтобы создать кластер AKS с именем *myAKSCluster*.

> [!NOTE]
> Свойство *балансировки нагрузки — SKU* можно использовать только при создании кластера. Вы не можете изменить SKU балансировщика нагрузки после создания кластера AKS. Кроме того, в одном кластере можно использовать только один тип SKU балансировщика нагрузки.

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

Управлять кластером Kubernetes можно c помощью [kubectl][kubectl], клиента командной строки Kubernetes. Если вы используете Azure Cloud Shell, `kubectl` уже установлен. Чтобы установить `kubectl` локально, используйте команду [az aks install-cli][az-aks-install-cli]:

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

## <a name="verify-your-cluster-uses-the-standard-sku"></a>Убедитесь, что в кластере используется SKU " *стандартный* "

Чтобы отобразить конфигурацию кластера, используйте команду [AZ AKS Показать][az-aks-show] .

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

Убедитесь, что для свойства *лоадбаланцерску* указано значение *стандартный*.

## <a name="use-the-load-balancer"></a>Использование балансировщика нагрузки

Чтобы использовать подсистему балансировки нагрузки в кластере, создайте манифест службы с типом службы"балансировщик". Чтобы отобразить работоспособную подсистему балансировки нагрузки, создайте другой манифест с примером приложения для запуска в кластере. Этот пример приложения предоставляется в подсистеме балансировки нагрузки и может быть просмотрен в браузере.

Создайте манифест с именем `sample.yaml` , как показано в следующем примере:

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

Приведенный выше манифест настраивает два развертывания: *Azure-голосовать-Front* и *Azure-голосовать-Back*. Чтобы настроить доступ к среде *Azure-голосовать-Front* , используя подсистему балансировки нагрузки, создайте манифест `standard-lb.yaml` с именем, как показано в следующем примере:

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

Служба *Azure-голосовать-Front* использует тип балансировщика , чтобы настроить подсистему балансировки нагрузки в кластере AKS для подключения к *Azure-голосовому* развертыванию.

Разверните пример приложения и подсистему балансировки нагрузки с помощью [kubectl Apply][kubectl-apply] и укажите имена манифестов YAML:

```console
kubectl apply -f sample.yaml
kubectl apply -f standard-lb.yaml
```

Теперь подсистема балансировки нагрузки уровня SKU " *стандартный* " настроена для предоставления примера приложения. Просмотрите сведения о службе *Azure-голосовать-Front* с помощью [kubectl Get][kubectl-get] , чтобы увидеть общедоступный IP-адрес балансировщика нагрузки. Общедоступный IP-адрес балансировщика нагрузки отображается в столбце *External-IP* . Изменение IP-адреса с *\<ожидания\>* на фактический внешний IP-адрес может занять одну или две минуты, как показано в следующем примере:

```
$ kubectl get service azure-vote-front

NAME                TYPE           CLUSTER-IP     EXTERNAL-IP     PORT(S)        AGE
azure-vote-front    LoadBalancer   10.0.227.198   52.179.23.131   80:31201/TCP   16s
```

Перейдите к общедоступному IP-адресу в браузере и убедитесь, что вы видите пример приложения. В приведенном выше примере общедоступный IP `52.179.23.131`-адрес —.

![Изображение перехода к приложению Azure для голосования](media/container-service-kubernetes-walkthrough/azure-vote.png)

> [!NOTE]
> Вы также можете настроить подсистему балансировки нагрузки как внутреннюю и не предоставлять общедоступный IP-адрес. Чтобы настроить подсистему балансировки нагрузки как внутреннюю `service.beta.kubernetes.io/azure-load-balancer-internal: "true"` , добавьте ее в качестве заметки в службу балансировщика *нагрузки* . Пример манифеста YAML, а также дополнительные сведения о внутреннем подсистеме балансировки нагрузки можно увидеть [здесь][internal-lb-yaml].

## <a name="clean-up-the-standard-sku-load-balancer-configuration"></a>Очистка конфигурации "Стандартный" балансировщика нагрузки SKU

Чтобы удалить образец приложения и конфигурацию подсистемы балансировки нагрузки, используйте [kubectl Delete][kubectl-delete]:

```console
kubectl delete -f sample.yaml
kubectl delete -f standard-lb.yaml
```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о Kubernetes Services см. в [документации по службам Kubernetes][kubernetes-services].

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
