---
title: Обновление кластера службы Azure Kubernetes (AKS)
description: Узнайте, как обновить кластер Azure Kubernetes Service (AKS), чтобы получить новейшие функции и обновления для системы безопасности.
services: container-service
ms.topic: article
ms.date: 05/28/2020
ms.openlocfilehash: ea9f0154c221fe99d683cc58d5f6dccfce8d948c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800500"
---
# <a name="upgrade-an-azure-kubernetes-service-aks-cluster"></a>Обновление кластера службы Azure Kubernetes (AKS)

Частое обновление Kubernetes до последней версии является частью жизненного цикла кластера AKS. Очень важно применять последние выпуски безопасности Kubernetes или обновлять последние функции. В этой статье показано, как обновить главные компоненты или один пул узлов по умолчанию в кластере AKS.

Для кластеров AKS, использующих несколько пулов узлов или узлов Windows Server (в настоящее время находится в предварительной версии в AKS), см. статью [Обновление пула узлов в AKS][nodepool-upgrade].

## <a name="before-you-begin"></a>Перед началом

Для работы с этой статьей требуется Azure CLI версии 2.0.65 или более поздней. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0][azure-cli-install].

> [!WARNING]
> Обновление кластера AKS активирует Cordon и сток узлов. При наличии свободной квоты вычислений обновление может завершиться ошибкой. Дополнительные сведения см. в разделе [увеличение квот](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) .

## <a name="check-for-available-aks-cluster-upgrades"></a>Проверка доступных обновлений кластера AKS

С помощью команды [az aks get-upgrades][az-aks-get-upgrades] проверьте выпуски Kubernetes, доступные для кластера. В следующем примере выполняется проверка доступных обновлений для кластера *myAKSCluster* в группе ресурсов *myResourceGroup*.

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster --output table
```

> [!NOTE]
> После обновления кластера AKS промежуточные версии Kubernetes невозможно пропустить. Например, Допускается обновление с параметром *1.12. x*  ->  *1.13. x* или *1.13. x*  ->  *1.14. x* , однако *1.12. x*  ->  *1.14. x* — нет.
>
> Чтобы выполнить обновление, начиная с *1.12. x*  ->  *1.14. x*, сначала выполните обновление с *1.12. x*  ->  *1.13. x*, а затем выполните обновление с *1.13. x*  ->  *1.14. x*.

В следующем примере выходных данных показано, что кластер можно обновить до версии *1.13.9* и *1.13.10*:

```console
Name     ResourceGroup     MasterVersion    NodePoolVersion    Upgrades
-------  ----------------  ---------------  -----------------  ---------------
default  myResourceGroup   1.12.8           1.12.8             1.13.9, 1.13.10
```
Если обновление недоступно, вы получите следующее:
```console
ERROR: Table output unavailable. Use the --query option to specify an appropriate query. Use --debug for more info.
```

## <a name="customize-node-surge-upgrade-preview"></a>Настройка обновления нехватка узлов (Предварительная версия)

> [!Important]
> Исчерпание узлов требует квоты подписки на запрошенное максимальное число скачков для каждой операции обновления. Например, кластер с 5 пулами узлов, каждый из которых имеет количество 4 узлов, всего 20 узлов. Если для каждого пула узлов задано максимальное значение всплеска напряжения 50%, для завершения обновления требуется дополнительная квота вычислений и IP-адреса, равная 10 узлам (2 узла * 5 пулов).
>
> Если вы используете Azure CNI, проверьте наличие доступных IP-адресов в подсети и [соответствие требованиям, предъявляемым службой Azure CNI](configure-azure-cni.md).

По умолчанию AKS настраивает обновление на всплеск с помощью одного дополнительного узла. Значение по умолчанию для параметра max всплеска напряжения позволяет AKS минимизировать перерыв рабочей нагрузки путем создания дополнительного узла перед тем, как Cordon/сток существующих приложений заменит узел с более ранними версиями. Максимальное значение всплеска напряжения можно настроить для каждого пула узлов, чтобы обеспечить компромисс между скоростью обновления и перерывом в обновлении. Увеличивая максимальное значение всплеска напряжения, процесс обновления выполняется быстрее, но установка большого значения для параметра max всплеска напряжения может привести к сбоям во время процесса обновления. 

Например, максимальное значение всплеска напряжения 100% обеспечивает самый быстрый возможный процесс обновления (удвоение числа узлов), но также приводит к тому, что все узлы в пуле узлов будут одновременно остановлены. Вы можете использовать более высокое значение, например, для тестовых сред. Для пулов производственных узлов рекомендуется использовать параметр max_surge 33%.

AKS принимает целочисленные значения и процентное значение для максимального всплеска напряжения. Целое число, например "5", означает пять дополнительных узлов для всплеска. Значение "50%" указывает на значение всплеска в половину текущего числа узлов в пуле. Максимальное значение процента всплеска напряжения может быть не менее 1% и не более 100%. Значение процента округляется до ближайшего числа узлов. Если максимальное значение всплеска напряжения ниже текущего числа узлов во время обновления, то для максимального значения всплеска используется текущее число узлов.

Во время обновления максимальное значение всплеска напряжения может быть не менее 1, а максимальное значение равно количеству узлов в пуле узлов. Можно задать большие значения, но максимальное количество узлов, используемых для максимального всплеска напряжения, не будет превышать число узлов в пуле во время обновления.

### <a name="set-up-the-preview-feature-for-customizing-node-surge-upgrade"></a>Настройка функции предварительной версии для настройки обновления нехватка узлов

```azurecli-interactive
# register the preview feature
az feature register --namespace "Microsoft.ContainerService" --name "MaxSurgePreview"
```

Регистрация займет несколько минут. Используйте приведенную ниже команду, чтобы убедиться, что компонент зарегистрирован.

```azurecli-interactive
# Verify the feature is registered:
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MaxSurgePreview')].{Name:name,State:properties.state}"
```

На этапе предварительной версии требуется расширение CLI *AKS-Preview* , чтобы использовать максимальный всплеск. Используйте команду [AZ Extension Add][az-extension-add] , а затем проверьте наличие доступных обновлений с помощью команды [AZ Extension Update][az-extension-update] .

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

> [!Important]
> Параметр максимального увеличения напряжения в пуле узлов является постоянным.  Этот параметр будет использоваться для последующих обновлений Kubernetes или обновлений версий узла. Максимальное значение всплеска для пулов узлов можно изменить в любое время. Для пулов производственных узлов рекомендуется использовать параметр max-всплеска на 33%.

Используйте следующие команды, чтобы задать максимальные значения всплесков для новых или существующих пулов узлов.

```azurecli-interactive
# Set max surge for a new node pool
az aks nodepool add -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 33%
```

```azurecli-interactive
# Update max surge for an existing node pool 
az aks nodepool update -n mynodepool -g MyResourceGroup --cluster-name MyManagedCluster --max-surge 5
```

## <a name="upgrade-an-aks-cluster"></a>Обновление кластера AKS

С помощью команды [az aks upgrade][az-aks-upgrade] обновите кластер AKS, используя список доступных версий. В процессе обновления AKS добавляет в кластер новый узел, который запускает указанную версию Kubernetes, а затем аккуратно [Cordon и][kubernetes-drain] настраивает один из старых узлов, чтобы минимизировать перерывы в работе приложений. Когда новый узел будет подтвержден как работающий модули приложения, старый узел удаляется. Этот процесс повторяется до тех пор, пока не будут обновлены все узлы в кластере.

```azurecli-interactive
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --kubernetes-version KUBERNETES_VERSION
```

Время, требуемое для выполнения обновления кластера, зависит от количества узлов.

> [!NOTE]
> Существует общее допустимое время для завершения обновления кластера. Это время вычисляется с помощью продукта `10 minutes * total number of nodes in the cluster` . Например, в кластере из 20 узлов операции обновления должны успешно пройти через 200 минут, иначе AKS не сможет выполнить операцию, чтобы избежать неисправимого состояния кластера. Чтобы восстановиться при сбое обновления, повторите операцию обновления после того, как будет достигнуто время ожидания.

Чтобы проверить, что обновление выполнено успешно, введите команду [az aks show][az-aks-show].

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --output table
```

В следующем примере выходных данных показано, что кластер теперь выполняет *1.13.10*:

```json
Name          Location    ResourceGroup    KubernetesVersion    ProvisioningState    Fqdn
------------  ----------  ---------------  -------------------  -------------------  ---------------------------------------------------------------
myAKSCluster  eastus      myResourceGroup  1.13.10               Succeeded            myaksclust-myresourcegroup-19da35-90efab95.hcp.eastus.azmk8s.io
```

## <a name="next-steps"></a>Дальнейшие шаги

В этой статье было показано, как выполнить обновление существующего кластера AKS. Дополнительные сведения о развертывании AKS и управлении ею см. в следующей статье.

> [!div class="nextstepaction"]
> [Руководство. Подготовка приложения для Службы Azure Kubernetes (AKS)][aks-tutorial-prepare-app].

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
