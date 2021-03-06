---
title: Миграция в службу Kubernetes Azure (AKS)
description: Переход на службу Azure Kubernetes Service (AKS).
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 11/07/2018
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 8070e209910425f9baa0ae81aca349a067c70f76
ms.sourcegitcommit: 51ed913864f11e78a4a98599b55bbb036550d8a5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2020
ms.locfileid: "75658548"
---
# <a name="migrate-to-azure-kubernetes-service-aks"></a>Миграция в службу Kubernetes Azure (AKS)

Эта статья поможет вам спланировать и выполнить успешную миграцию в службу Azure Kubernetes (AKS). Чтобы помочь вам принять основные решения, в этом разделе содержатся сведения о текущей рекомендуемой конфигурации для AKS. В этой статье не рассматриваются все сценарии, и там, где это необходимо, содержатся ссылки на более подробные сведения о планировании успешной миграции.

Этот документ можно использовать для поддержки следующих сценариев:

* Миграция кластера AKS, поддерживаемого группами [доступности](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) , в [масштабируемые наборы виртуальных машин](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview)
* Миграция кластера AKS для использования [балансировщика нагрузки SKU](https://docs.microsoft.com/azure/aks/load-balancer-standard) уровня "Стандартный"
* Миграция из [службы контейнеров Azure (ACS) — прекращение 31 января 2020 г](https://azure.microsoft.com/updates/azure-container-service-will-retire-on-january-31-2020/) . до AKS
* Миграция с [AKS Engine](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908) на AKS
* Миграция с кластеров Kubernetes, отличных от Azure, в AKS

При миграции убедитесь, что Целевая версия Kubernetes находится в поддерживаемом окне для AKS. Если используется более старая версия, она может не находиться в поддерживаемом диапазоне и требовать обновления версий, поддерживаемых AKS. Дополнительные сведения см. в разделе [Поддерживаемые версии Kubernetes AKS](https://docs.microsoft.com/azure/aks/supported-kubernetes-versions) .

Если вы выполняете миграцию на более новую версию Kubernetes, ознакомьтесь с разработкой [политики поддержки отклонений версий и версий Kubernetes](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-versions).

Некоторые средства с открытым кодом могут помочь при миграции в зависимости от сценария.

* [Велеро](https://velero.io/) (требуется Kubernetes 1.7 +)
* [Расширение CLI Azure KUBE](https://github.com/yaron2/azure-kube-cli)
* [Сдвиг](https://github.com/mhausenblas/reshifter)

В этой статье приводятся сводные сведения о миграции:

> [!div class="checklist"]
> * AKS с Load Balancer (цен. категория "Стандартный") и масштабируемыми наборами виртуальных машин
> * Существующие подключенные службы Azure
> * Обеспечение допустимых квот
> * Высокий уровень доступности и непрерывность бизнес-процессов
> * Рекомендации для приложений без отслеживания состояния
> * Рекомендации для отслеживании приложений
> * Развертывание конфигурации кластера

## <a name="aks-with-standard-load-balancer-and-virtual-machine-scale-sets"></a>AKS с Load Balancer (цен. категория "Стандартный") и масштабируемыми наборами виртуальных машин

AKS — это управляемая служба, предлагающая уникальные возможности с более низкими затратами на управление. В результате управляемой службы необходимо выбрать из набора [регионов](https://docs.microsoft.com/azure/aks/quotas-skus-regions) , поддерживаемых AKS. Переход из существующего кластера в AKS может потребовать изменения существующих приложений, чтобы они оставались работоспособными в управляемой плоскости управления AKS.

Мы рекомендуем использовать кластеры AKS, которые поддерживаются [масштабируемыми наборами виртуальных машин](https://docs.microsoft.com/azure/virtual-machine-scale-sets) и [Load Balancer (цен. Категория "Стандартный") Azure](https://docs.microsoft.com/azure/aks/load-balancer-standard) , чтобы получить такие компоненты, как [несколько пулов узлов](https://docs.microsoft.com/azure/aks/use-multiple-node-pools), [зоны доступности](https://docs.microsoft.com/azure/availability-zones/az-overview), [диапазоны разрешенных IP-адресов](https://docs.microsoft.com/azure/aks/api-server-authorized-ip-ranges), [Автомасштабирование кластера](https://docs.microsoft.com/azure/aks/cluster-autoscaler), [Политика Azure для AKS](https://docs.microsoft.com/azure/governance/policy/concepts/rego-for-aks)и другие новые функции по мере их выпуска.   

В кластерах AKS, поддерживающих [группы доступности виртуальных машин](https://docs.microsoft.com/azure/virtual-machine-scale-sets/availability#availability-sets) , отсутствует поддержка многих из этих функций.

В следующем примере создается кластер AKS с одним пулом узлов, поддерживаемым масштабируемым набором виртуальных машин. В нем используется стандартный балансировщик нагрузки. Он также включает Автомасштабирование кластера в пуле узлов для кластера и задает минимум *1* и максимум *3* узла:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

## <a name="existing-attached-azure-services"></a>Существующие подключенные службы Azure

При миграции кластеров могут быть присоединены внешние службы Azure. Для этого не требуется отдых ресурсов, но для поддержания функциональности им потребуется обновить подключения с предыдущих на новые кластеры.

* Реестр контейнеров Azure
* Log Analytics
* Application Insights
* Диспетчер трафика
* Учетная запись хранения
* Внешние базы данных

## <a name="ensure-valid-quotas"></a>Обеспечение допустимых квот

Так как дополнительные виртуальные машины будут развернуты в рамках подписки во время миграции, следует убедиться, что квоты и ограничения достаточны для этих ресурсов. Может потребоваться запросить увеличение [квоты виртуальных ЦП](https://docs.microsoft.com/azure/azure-supportability/per-vm-quota-requests).

Может потребоваться запросить увеличение [квоты сети](https://docs.microsoft.com/azure/azure-supportability/networking-quota-requests) , чтобы не допустить исчерпания IP-адресов. Дополнительные сведения см. в разделе [сети и диапазоны IP-адресов для AKS](https://docs.microsoft.com/azure/aks/configure-kubenet) .

Дополнительные сведения см. в статье [Подписка Azure и ограничения службы](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits). Чтобы проверить текущие квоты, в портал Azure перейдите в [колонку подписки](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade), выберите свою подписку и щелкните **использование + квоты**.

## <a name="high-availability-and-business-continuity"></a>Высокий уровень доступности и непрерывность бизнес-процессов

Если приложение не может справиться со временем простоя, необходимо следовать рекомендациям для сценариев миграции высокого уровня доступности.  Рекомендации по комплексному планированию непрерывности бизнес-процессов, аварийному восстановлению и максимальному времени работы выходят за рамки этого документа.  Дополнительные сведения см. в статье рекомендации [по обеспечению непрерывности бизнес-процессов и аварийному восстановлению в службе Azure Kubernetes (AKS)](https://docs.microsoft.com/azure/aks/operator-best-practices-multi-region) .

Миграция сложных приложений обычно выполняется в течение некоторого времени, а не одномоментно. Это означает, что старым и новым средам может потребоваться взаимодействие по сети. Приложения, которые ранее использовали `ClusterIP` служб для взаимодействия, могут быть предоставлены как тип `LoadBalancer` и должны быть защищены соответствующим образом.

Чтобы завершить миграцию, необходимо указать клиентам новые службы, работающие на AKS. Мы рекомендуем перенаправить трафик, обновив DNS, чтобы указать Load Balancer, расположенную перед кластером AKS.

[Диспетчер трафика Azure](https://docs.microsoft.com/azure/traffic-manager/) может направлять клиентов в нужный кластер Kubernetes и экземпляр приложения.  Диспетчер трафика — это балансировщик нагрузки трафика на основе DNS, который может распределять сетевой трафик между регионами.  Чтобы обеспечить оптимальную производительность и избыточность, направьте весь трафик приложения через диспетчер трафика, прежде чем он перейдет в кластер AKS.  При развертывании в многокластерной среде клиенты должны подключаться к DNS-имени диспетчера трафика, которое указывает на службы в каждом кластере AKS. Определите эти службы с помощью конечных точек диспетчера трафика. Каждая конечная точка — это *IP-адрес балансировщика нагрузки службы*. Используйте эту конфигурацию, чтобы направить сетевой трафик из конечной точки диспетчера трафика в одном регионе в конечную точку в другом регионе.

![AKS с диспетчером трафика](media/operator-best-practices-bc-dr/aks-azure-traffic-manager.png)

[Служба "Передняя дверца Azure](https://docs.microsoft.com/azure/frontdoor/front-door-overview) " — это еще один вариант для маршрутизации трафика для кластеров AKS.  Azure Front Door Service позволяет определять, управлять и отслеживать глобальную маршрутизацию для вашего веб-трафика посредством оптимизации для обеспечения наилучшей производительности и мгновенной глобальной отработки отказа для обеспечения высокой доступности. 

### <a name="considerations-for-stateless-applications"></a>Рекомендации для приложений без отслеживания состояния 

Перенос приложений без отслеживания состояния — это самый простой случай. Примените определения ресурсов (YAML или Helm) к новому кластеру, убедитесь, что все работает правильно, и перенаправление трафика для активации нового кластера.

### <a name="considers-for-stateful-applications"></a>Учитывает приложения с отслеживанием состояния

Тщательно спланируйте миграцию приложений с отслеживанием состояния, чтобы избежать потери данных или непредвиденного простоя.

Если вы используете службу файлов Azure, вы можете подключить общую папку как том в новом кластере:
* [Подключение статических файлов Azure в качестве тома](https://docs.microsoft.com/azure/aks/azure-files-volume#mount-the-file-share-as-a-volume)

Если вы используете управляемые диски Azure, вы можете подключить диск только в том случае, если он не подключен к какой бы то ни было виртуальной машине:
* [Подключение статического диска Azure в качестве тома](https://docs.microsoft.com/azure/aks/azure-disk-volume#mount-disk-as-volume)

Если ни один из этих подходов не работает, можно использовать параметры резервного копирования и восстановления.
* [Велеро в Azure](https://github.com/heptio/velero/blob/master/site/docs/master/azure-config.md)

#### <a name="azure-files"></a>Файлы Azure

В отличие от дисков, файлы Azure можно одновременно подключить к нескольким узлам. В кластере AKS Azure и Kubernetes не препятствуют созданию Pod, который по-прежнему используется кластером ACS. Чтобы избежать потери данных и непредвиденного поведения, убедитесь, что кластеры не записывают одни и те же файлы одновременно.

Если в приложении может размещаться несколько реплик, которые указывают на один и тот же общий файловый ресурс, следуйте шагам миграции без отслеживания состояния и разверните определения YAML в новом кластере. В противном случае для миграции возможен следующий порядок действий.

* Проверьте, правильно ли работает приложение.
* Наведите динамический трафик в новый кластер AKS.
* Отключите старый кластер.

Если вы хотите начать с пустой общей папки и создать копию исходных данных, можно использовать команды [`az storage file copy`](https://docs.microsoft.com/cli/azure/storage/file/copy?view=azure-cli-latest) для переноса данных.


#### <a name="migrating-persistent-volumes"></a>Перенос постоянных томов

При переносе существующих постоянных томов в AKS, как правило, необходимо выполнить следующие действия:

* Замораживание записи в приложение. (Этот шаг является необязательным и требует простоя.)
* Создание моментальных снимков дисков.
* Создание управляемых дисков из моментальных снимков.
* Создайте постоянные тома в AKS.
* Обновите спецификации Pod, чтобы [использовать существующие тома](https://docs.microsoft.com/azure/aks/azure-disk-volume) , а не персистентволумеклаимс (статическая подготовка).
* Разверните приложение в AKS.
* Проверьте, правильно ли работает приложение.
* Наведите динамический трафик в новый кластер AKS.

> [!IMPORTANT]
> Если вы решили не заморозить операции записи, необходимо реплицировать данные в новое развертывание. В противном случае данные, записанные после создания моментальных снимков диска, будут пропущены.

Некоторые средства с открытым кодом могут помочь в создании управляемых дисков и переносе томов между кластерами Kubernetes:

* [Azure CLIное расширение копирования дисков](https://github.com/noelbundick/azure-cli-disk-copy-extension) копирует и преобразует диски в группах ресурсов и регионах Azure.
* [Расширение CLI Azure KUBE](https://github.com/yaron2/azure-kube-cli) перечисляет тома Kubernetes ACS и переносит их в кластер AKS.


### <a name="deployment-of-your-cluster-configuration"></a>Развертывание конфигурации кластера

Мы рекомендуем использовать существующий конвейер непрерывной интеграции (CI) и непрерывной поставки (CD) для развертывания известной работоспособной конфигурации в AKS. Вы можете использовать Azure Pipelines для [создания и развертывания приложений в AKS](https://docs.microsoft.com/azure/devops/pipelines/ecosystems/kubernetes/aks-template?view=azure-devops) клонировать существующие задачи развертывания и убедиться, что `kubeconfig` указывает на новый кластер AKS.

Если это невозможно, экспортируйте определения ресурсов из существующего кластера Kubernetes и примените их к AKS. Для экспорта объектов можно использовать `kubectl`.

```console
kubectl get deployment -o=yaml --export > deployments.yaml
```

В этой статье мы обобщены сведения о миграции для:

> [!div class="checklist"]
> * AKS с Load Balancer (цен. категория "Стандартный") и масштабируемыми наборами виртуальных машин
> * Существующие подключенные службы Azure
> * Обеспечение допустимых квот
> * Высокий уровень доступности и непрерывность бизнес-процессов
> * Рекомендации для приложений без отслеживания состояния
> * Рекомендации для отслеживании приложений
> * Развертывание конфигурации кластера
