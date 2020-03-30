---
title: Как включить Azure Monitor для контейнеров Документы Майкрософт
description: В этой статье описывается, как вы включите и настраиваете Azure Monitor для контейнеров, чтобы вы могли понять, как работает ваш контейнер и какие проблемы, связанные с производительностью, были выявлены.
ms.topic: conceptual
ms.date: 11/18/2019
ms.openlocfilehash: 7aad7e7dd5ec2569377f9276c2e4793c7afd631a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275312"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Как включить Azure Monitor для контейнеров

В этой статье представлен обзор возможностей настройки Azure Monitor для контейнеров для мониторинга производительности рабочих нагрузок, развернутых в средах Kubernetes и размещенных на:

- [Служба Лазурного Кубернета](https://docs.microsoft.com/azure/aks/) (AKS)

- Самоуправляемые кластеры Kubernetes, размещенные на Azure с помощью [AKS Engine.](https://github.com/Azure/aks-engine)

- Самоуправляемые кластеры Kubernetes размещаются в [Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1910) или на месте с помощью AKS Engine.

- [Azure Red Hat OpenShift](../../openshift/intro-openshift.md)

Azure Monitor для контейнеров может быть включен для новых или нескольких существующих развертываний Kubernetes с использованием следующих поддерживаемых методов:

- С портала Azure, Azure PowerShell, или с Azure CLI

- используя [Terraform и AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Предварительные требования

Чтобы начать, у вас должны быть следующие компоненты:

- **Рабочая область Log Analytics.**

    Azure Monitor для контейнеров поддерживает рабочее пространство log Analytics в регионах, перечисленных в продуктах Azure [по регионам.](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor)

    Можно создать рабочее пространство, когда вы включите мониторинг нового кластера AKS или позволить опыту посадки создать рабочее пространство по умолчанию в группе ресурсов по умолчанию в группе кластера AKS. Если вы хотите создать ее самостоятельно, используйте [Azure Resource Manager](../platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) или [портал Azure](../learn/quick-create-workspace.md). Для списка поддерживаемых пар отображения, используемых [Region mapping for Azure Monitor for containers](container-insights-region-mapping.md)для рабочего пространства по умолчанию, см.

- Вы являетесь участником **роли участника Log Analytics** для включения мониторинга контейнеров. Дополнительные сведения о том, как управлять доступом к рабочей области Log Analytics, см. в статье [Управление рабочими областями](../platform/manage-access.md).

- Вы являетесь участником роли **[Владельца](../../role-based-access-control/built-in-roles.md#owner)** на ресурсе кластера AKS.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* Метрики Prometheus не собираются по умолчанию. Перед [настройкой агента](container-insights-prometheus-integration.md) для их сбора важно просмотреть [документацию](https://prometheus.io/) Prometheus, чтобы понять, что можно соскоблить и методы поддерживаются.

## <a name="supported-configurations"></a>Поддерживаемые конфигурации

Следующее официально поддерживается Azure Monitor для контейнеров.

- Среды: Azure Red Hat OpenShift, Kubernetes на месте, и AKS двигатель на Azure и Azure стек. Для получения дополнительной [AKS Engine on Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-overview?view=azs-1908)информации см.
- Версии Kubernetes и политика поддержки такие же, как версии [поддерживаемых AKS.](../../aks/supported-kubernetes-versions.md) 

## <a name="network-firewall-requirements"></a>Требования к брандмауэру в сети

В приведенной в следующей таблице указана информация о конфигурации прокси и брандмауэра, необходимая контейнерному агенту для связи с Azure Monitor для контейнеров. Весь сетевой трафик от агента переправляется в Azure Monitor.

|Ресурс агента|порты; |
|--------------|------|
| *.ods.opinsights.azure.com | 443 |  
| *.oms.opinsights.azure.com | 443 | 
| *.blob.core.windows.net | 443 |
| dc.services.visualstudio.com | 443 |
| *.microsoftonline.com | 443 |
| В.monitoring.azure.com | 443 |
| login.microsoftonline.com | 443 |

В следующей таблице перечислены сведения о конфигурации прокси и брандмауэра для Azure China.

|Ресурс агента|порты; |Описание | 
|--------------|------|-------------|
| Вопросods.opinsights.azure.cn | 443 | Прием данных |
| Oms.opinsights.azure.cn | 443 | OMS на борту |
| *.blob.core.windows.net | 443 | Используется для мониторинга исходящих подключений. |
| microsoft.com | 80 | Используется для подключения к сети. Это необходимо только в том случае, если версия изображения агента ciprod09262019 или раньше. |
| dc.services.visualstudio.com | 443 | Для телеметрии агента с помощью Azure Public Cloud Application Insights. |

В приведенной в следующей таблице приведена информация о конфигурации прокси и брандмауэра для правительства США Azure.

|Ресурс агента|порты; |Описание | 
|--------------|------|-------------|
| *.ods.opinsights.azure.us | 443 | Прием данных |
| *.oms.opinsights.azure.us | 443 | OMS на борту |
| *.blob.core.windows.net | 443 | Используется для мониторинга исходящих подключений. |
| microsoft.com | 80 | Используется для подключения к сети. Это необходимо только в том случае, если версия изображения агента ciprod09262019 или раньше. |
| dc.services.visualstudio.com | 443 | Для телеметрии агента с помощью Azure Public Cloud Application Insights. |

## <a name="components"></a>Компоненты

Ваша способность контролировать производительность основывается на контейнерном агенте Log Analytics для Linux, специально разработанном для Azure Monitor для контейнеров. Этот специализированный агент собирает данные о производительности и событиях со всех узлов кластера, а потом автоматически развертывается и регистрируется в указанной рабочей области Log Analytics во время развертывания. Версия агента — microsoft/oms:ciprod04202018 и выше. Номер представлен датой в формате *ммддгггг*.

>[!NOTE]
>При предварительном выпуске поддержки Windows Server для AKS кластер AKS с узлами Windows Server не имеет агента, установленного для сбора данных и переадресованного на Azure Monitor. Вместо этого узел Linux, автоматически развертываемый в кластере в рамках стандартного развертывания, собирает и перенаправляет данные в Azure Monitor от имени всех узлов Windows в кластере.  
>

При выпуске новой версии агент автоматически обновляется в управляемых кластерах Kubernetes, размещенных в Службе Azure Kubernetes (AKS). Выпущенные версии см. в [объявлениях о выпусках агента](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Если вы уже развернули кластер AKS, то можете включить мониторинг с помощью Azure CLI или предоставив шаблон Azure Resource Manager, как показано далее в этой статье. Невозможно использовать `kubectl` для обновления, удаления, повторного развертывания или развертывания агента.
>Развертывание шаблона должно проходить в той же группе ресурсов, что и у кластера.

Вы позволяет azure Monitor для контейнеров использовать один из следующих методов, описанных в следующей таблице.

| Состояние развертывания | Метод | Описание |
|------------------|--------|-------------|
| Новый кластер AKS Kubernetes | [Создание кластера AKS с помощью Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Можно включить мониторинг нового кластера AKS, который создается с помощью Azure CLI. |
| | [Создание кластера AKS с помощью Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Можно включить мониторинг нового кластера AKS, который вы создаете с помощью инструмента с открытым исходным кодом Terraform. |
| | [Создание кластера OpenShift с помощью шаблона управления ресурсами Azure](container-insights-azure-redhat-setup.md#enable-for-a-new-cluster-using-an-azure-resource-manager-template) | Можно включить мониторинг нового кластера OpenShift, который создается с помощью предварительно настроенного шаблона управления ресурсами Azure. |
| | [Создание кластера OpenShift с помощью Azure CLI](https://docs.microsoft.com/cli/azure/openshift?view=azure-cli-latest#az-openshift-create) | Можно включить мониторинг при развертывании нового кластера OpenShift с помощью Azure CLI. |
| Существующий кластер AKS Kubernetes | [Включить кластер AKS с помощью Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Можно включить мониторинг кластера AKS, уже развернутого с помощью Azure CLI. |
| |[Включить кластер AKS с помощью Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Можно включить мониторинг кластера AKS, уже развернутого с помощью инструмента terraform с открытым исходным кодом. |
| | [Включить кластер AKS от Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Можно включить мониторинг одного или нескольких кластеров AKS, уже развернутых со страницы мультикластеров в Azure Monitor. |
| | [Включение из кластера AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Мониторинг можно осуществлять непосредственно из кластера AKS на портале Azure. |
| | [Включить кластер AKS с помощью шаблона управления ресурсами Azure](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Можно включить мониторинг кластера AKS с предварительно настроенным шаблоном управления ресурсами Azure. |
| | [Включить для гибридного кластера Kubernetes](container-insights-hybrid-setup.md) | Вы можете включить мониторинг AKS Engine, размещенный в Azure Stack или для Kubernetes, размещенных на территории. |
| | [Включить кластер OpenShift с помощью шаблона управления ресурсами Azure](container-insights-azure-redhat-setup.md#enable-using-an-azure-resource-manager-template) | Можно включить мониторинг существующего кластера OpenShift с предварительно настроенным шаблоном управления ресурсами Azure. |
| | [Включить кластер OpenShift от Azure Monitor](container-insights-azure-redhat-setup.md#from-the-azure-portal) | Можно включить мониторинг одного или нескольких кластеров OpenShift, уже развернутых со страницы с несколькими кластерами в Azure Monitor. |

## <a name="next-steps"></a>Дальнейшие действия

- С включенным мониторингом можно начать анализировать производительность кластеров Kubernetes, размещенных в службе Azure Kubernetes (AKS), Azure Stack или другой среде. Чтобы узнать, как использовать Azure Monitor для контейнеров, [см.](container-insights-analyze.md)
