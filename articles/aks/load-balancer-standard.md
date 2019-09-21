---
title: Использование балансировщика нагрузки "Стандартный" в службе Kubernetes Azure (AKS)
description: Узнайте, как использовать подсистему балансировки нагрузки со стандартным SKU для предоставления служб службе Azure Kubernetes Service (AKS).
services: container-service
author: zr-msft
ms.service: container-service
ms.topic: article
ms.date: 09/05/2019
ms.author: zarhoads
ms.openlocfilehash: d2a0ff5db6707c4f765c71937a7d0f0749401959
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/20/2019
ms.locfileid: "71172209"
---
# <a name="use-a-standard-sku-load-balancer-in-azure-kubernetes-service-aks"></a>Использование балансировщика нагрузки "Стандартный" в службе Kubernetes Azure (AKS)

Чтобы предоставить доступ к приложениям в Azure Kubernetes Service (AKS), можно создать и использовать Azure Load Balancer. Балансировщик нагрузки, выполняющийся в AKS, можно использовать в качестве внутренней или внешней подсистемы балансировки нагрузки. Внутренний балансировщик нагрузки делает службу Kubernetes доступной только для приложений, работающих в той же виртуальной сети, что и кластер AKS. Внешняя подсистема балансировки нагрузки получает один или несколько общедоступных IP-адресов для входящего трафика и делает службу Kubernetes доступной извне с помощью общедоступных IP-адресов.

Доступны два номера SKU Azure Load Balancer: ценовых категорий *Базовый* и *Стандартный*. По умолчанию используется *базовый* номер SKU, если манифест службы используется для создания балансировщика нагрузки в AKS. Использование подсистемы балансировки нагрузки уровня " *стандартный* " предоставляет дополнительные функции и функции, такие как размер внутреннего пула и зоны доступности. Важно понимать различия между *стандартными* и *базовыми* подсистемами балансировки нагрузки перед выбором используемого. После создания кластера AKS вы не сможете изменить SKU подсистемы балансировки нагрузки для этого кластера. Дополнительные сведения о SKU уровня " *базовый* " и " *стандартный* " см. в статье [Сравнение SKU в подсистеме балансировки нагрузки Azure][azure-lb-comparison].

В этой статье показано, как создать и использовать Azure Load Balancer с SKU " *стандартный* " с помощью службы Azure Kubernetes Service (AKS).

В этой статье предполагается, что основное понимание Kubernetes и Azure Load Balancer концепций. Дополнительные сведения см. в разделе [Основные понятия Kubernetes Core для службы Kubernetes Azure (AKS)][kubernetes-concepts] и [что Azure Load Balancer?][azure-lb].

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать CLI локально, для работы с этой статьей требуется Azure CLI версии 2.0.59 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][install-azure-cli].

## <a name="before-you-begin"></a>Перед началом работы

Субъекту-службе кластера AKS требуется разрешение для управления сетевыми ресурсами, если используется существующая подсеть или группа ресурсов. В общем случае назначьте роль *участника сети* субъекту-службе для делегированных ресурсов. Дополнительные сведения о разрешениях см. в статье [Делегирование AKS доступ к другим ресурсам Azure][aks-sp].

Необходимо создать кластер AKS, который задает для балансировщика нагрузки SKU *Standard* вместо *базового*по умолчанию.

### <a name="install-aks-preview-cli-extension"></a>Установка расширения интерфейса командной строки aks-preview

Для использования SKU "Стандартный" балансировщика нагрузки Azure требуется расширение CLI *AKS-Preview* версии 0.4.12 или более поздней. Установите расширение Azure CLI *AKS-Preview* с помощью команды [AZ Extension Add][az-extension-add] , а затем проверьте наличие доступных обновлений с помощью команды [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="limitations"></a>Ограничения

При создании кластеров AKS, поддерживающих подсистему балансировки нагрузки со *стандартным* SKU, применяются следующие ограничения.

* По крайней мере один общедоступный IP-адрес или префикс IP-адреса необходим для разрешения исходящего трафика из кластера AKS. Общедоступный IP-адрес или префикс IP-адреса также требуется для поддержания подключения между узлами управления и агентами, а также для обеспечения совместимости с предыдущими версиями AKS. Существуют следующие варианты указания общедоступных IP-адресов или префиксов IP с подсистемой балансировки нагрузки уровня " *стандартный* ":
    * Предоставьте собственные общедоступные IP-адреса.
    * Предоставьте собственные префиксы общедоступных IP-адресов.
    * Укажите число до 100, чтобы разрешить кластеру AKS создавать общедоступные IP-адреса *стандартного* SKU в той же группе ресурсов, которая была создана как кластер AKS, которая обычно называется с *MC_* в начале. AKS назначает общедоступный IP-адрес подсистеме балансировки нагрузки уровня " *стандартный* ". По умолчанию один общедоступный IP-адрес будет автоматически создан в той же группе ресурсов, что и кластер AKS, если не указан общедоступный IP-адрес, префикс общедоступного IP-адреса или число адресов IP. Кроме того, необходимо разрешить общедоступные адреса и избежать создания политики Azure, Ban создание IP-адресов.
* При использовании SKU " *стандартный* " для подсистемы балансировки нагрузки необходимо использовать Kubernetes версии 1,13 или более поздней.
* Определение номера SKU подсистемы балансировки нагрузки может выполняться только при создании кластера AKS. Вы не можете изменить SKU балансировщика нагрузки после создания кластера AKS.
* В одном кластере можно использовать только один номер SKU балансировщика нагрузки.

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
Чтобы запустить кластер AKS, который поддерживает подсистему балансировки нагрузки со *стандартным* SKU, кластеру необходимо установить для параметра *балансировки нагрузки-SKU* значение *Standard*. Этот параметр создает подсистему балансировки нагрузки с номером SKU " *стандартный* " при создании кластера. При *запуске службы балансирования нагрузки в* кластере конфигурация конфигурации " *стандартный* " SKU обновляется с конфигурацией службы. Используйте команду [AZ AKS Create][az-aks-create] , чтобы создать кластер AKS с именем *myAKSCluster*.

> [!NOTE]
> Свойство *балансировки нагрузки — SKU* можно использовать только при создании кластера. Вы не можете изменить SKU балансировщика нагрузки после создания кластера AKS. Кроме того, в одном кластере можно использовать только один тип SKU балансировщика нагрузки.
> 
> Если вы хотите использовать собственные общедоступные IP-адреса, используйте параметры, зафиксированные в *подсистеме балансировки нагрузки (Outbound*-IP-адреса) или с помощью пакетов *балансировки нагрузки с префиксами* . Оба эти параметра можно также использовать при [обновлении кластера](#optional---provide-your-own-public-ips-or-prefixes-for-egress).

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
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
aks-nodepool1-31718369-0   Ready    agent   6m44s   v1.13.10
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

Чтобы использовать подсистему балансировки нагрузки в кластере, создайте манифест службы *с типом службы*"балансировщик". Чтобы отобразить работоспособную подсистему балансировки нагрузки, создайте другой манифест с примером приложения для запуска в кластере. Этот пример приложения предоставляется в подсистеме балансировки нагрузки и может быть просмотрен в браузере.

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

Служба *Azure-голосовать-Front* *использует тип балансировщика, чтобы* настроить подсистему балансировки нагрузки в кластере AKS для подключения к *Azure-голосовому* развертыванию.

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

## <a name="optional---scale-the-number-of-managed-public-ips"></a>Необязательно. масштабирование количества управляемых общедоступных IP-адресов

При использовании балансировщика нагрузки " *стандартный* " с управляемыми исходящими общедоступными IP-адресами, которые создаются по умолчанию, можно масштабировать количество управляемых исходящих общедоступных IP-адресов с помощью параметра *подсистемы балансировки нагрузки, управляемого счетчиком* .

Чтобы обновить существующий кластер, выполните следующую команду. Этот параметр также можно задать во время создания кластера, чтобы иметь несколько управляемых исходящих общедоступных IP-адресов.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-managed-outbound-ip-count 2
```

В приведенном выше примере в качестве количества управляемых исходящих общедоступных IP-адресов устанавливается значение *2* для кластера *myAKSCluster* в *myResourceGroup*. 

Можно также использовать параметр *балансировки нагрузки-Managed-IP-Count* , чтобы задать начальное число управляемых исходящих общедоступных адресов при создании кластера, добавив `--load-balancer-managed-outbound-ip-count` параметр и задав для него нужное значение. По умолчанию общее число управляемых исходящих общедоступных IP-адресов равно 1.

## <a name="optional---provide-your-own-public-ips-or-prefixes-for-egress"></a>Необязательно. Предоставьте собственные общедоступные IP-адреса или префиксы для исходящего трафика.

При использовании балансировщика нагрузки *стандартного* SKU кластер AKS автоматически создает общедоступный IP-адрес в той же группе ресурсов, которая создана для кластера AKS, и назначает общедоступный IP-адрес подсистеме балансировки нагрузки уровня " *стандартный* ". Кроме того, вы можете назначить собственный общедоступный IP-адрес во время создания кластера или обновить свойства подсистемы балансировки нагрузки существующего кластера.

Путем объединения нескольких IP-адресов или префиксов можно определить несколько служб резервного копирования при определении IP-адреса за одним объектом балансировщика нагрузки. Конечная точка исходящего трафика конкретных узлов будет зависеть от того, с какой службой они связаны.

> [!IMPORTANT]
> Вы должны использовать общедоступные IP-адреса уровня " *стандартный* " для исходящего трафика с помощью *стандартного* SKU балансировщика нагрузки. Номер SKU общедоступных IP-адресов можно проверить с помощью команды [AZ Network public-IP-шоу][az-network-public-ip-show] :
>
> ```azurecli-interactive
> az network public-ip show --resource-group myResourceGroup --name myPublicIP --query sku.name -o tsv
> ```

Чтобы получить список идентификаторов общедоступных IP-адресов, используйте команду [AZ Network public-IP-шоу][az-network-public-ip-show] .

```azurecli-interactive
az network public-ip show --resource-group myResourceGroup --name myPublicIP --query id -o tsv
```

Приведенная выше команда показывает идентификатор общедоступного IP-адреса *myPublicIP* в группе ресурсов *myResourceGroup* .

Используйте команду *AZ AKS Update* с параметром *Load-балансировщика-Outbound-IP* , чтобы обновить кластер с помощью общедоступных адресов.

В следующем примере используется параметр *балансировки нагрузки — исходящие IP-адреса* с идентификаторами из предыдущей команды.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Вы также можете использовать префиксы общедоступных IP-адресов для исходящего трафика с помощью балансировщика нагрузки *стандартного* SKU. В следующем примере используется команда [AZ Network public-IP prefix показывать][az-network-public-ip-prefix-show] , чтобы получить список идентификаторов префиксов общедоступных IP-адресов:

```azurecli-interactive
az network public-ip prefix show --resource-group myResourceGroup --name myPublicIPPrefix --query id -o tsv
```

Приведенная выше команда показывает идентификатор для префикса общедоступного IP-адреса *мипублиЦиппрефикс* в группе ресурсов *myResourceGroup* .

В следующем примере используется параметр *Loading-outbounding-IP-префиксы* с идентификаторами из предыдущей команды.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

> [!IMPORTANT]
> Общедоступные IP-адреса и префиксы IP должны находиться в том же регионе и в той же подписке, что и кластер AKS. 

### <a name="define-your-own-public-ip-or-prefixes-at-cluster-create-time"></a>Определение собственных общедоступных IP-адресов или префиксов во время создания кластера

Вы можете приложить собственные IP-адреса или префиксы IP-адресов для исходящего трафика во время создания кластера для поддержки таких сценариев, как конечные точки исходящего трафика список разрешений. Добавьте те же параметры, показанные выше, на шаг создания кластера, чтобы определить собственные общедоступные IP-адреса и префиксы IP в начале жизненного цикла кластера.

Чтобы создать новый кластер с общедоступными IP-адресами в начале, используйте команду *AZ AKS Create* с параметром *балансировки нагрузки — исходящие IP-адреса* .

```
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ips <publicIpId1>,<publicIpId2>
```

Используйте команду *AZ AKS Create* с параметром *Load-балансировщик-Outbound-IP-префиксы* , чтобы создать новый кластер с префиксами общедоступного IP-адреса в начале.

```
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 1 \
    --load-balancer-sku standard \
    --generate-ssh-keys \
    --load-balancer-outbound-ip-prefixes <publicIpPrefixId1>,<publicIpPrefixId2>
```

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
[az-network-public-ip-show]: /cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-show
[az-network-public-ip-prefix-show]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest#az-network-public-ip-prefix-show
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[azure-lb]: ../load-balancer/load-balancer-overview.md
[azure-lb-comparison]: ../load-balancer/load-balancer-overview.md#skus
[install-azure-cli]: /cli/azure/install-azure-cli
[internal-lb-yaml]: internal-lb.md#create-an-internal-load-balancer
[kubernetes-concepts]: concepts-clusters-workloads.md
[use-kubenet]: configure-kubenet.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update

