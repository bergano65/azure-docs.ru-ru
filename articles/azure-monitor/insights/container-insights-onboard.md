---
title: Включение Azure Monitor для контейнеров | Документация Майкрософт
description: В этой статье описывается, как включить и настроить Azure Monitor для контейнеров, чтобы вы могли понять, как работает контейнер и какие проблемы с производительностью были обнаружены.
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/12/2019
ms.author: magoedte
ms.openlocfilehash: 25be8f166fec8a311fdc2ed1fa3fca6339185e94
ms.sourcegitcommit: 10251d2a134c37c00f0ec10e0da4a3dffa436fb3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/13/2019
ms.locfileid: "67867535"
---
# <a name="how-to-enable-azure-monitor-for-containers"></a>Включение Azure Monitor для контейнеров

В этой статье представлен обзор параметров, доступных для установки Azure Monitor контейнерах для мониторинга производительности рабочих нагрузок, развернутых в средах Kubernetes и размещенных в [службе Kubernetes Azure](https://docs.microsoft.com/azure/aks/).

Azure Monitor для контейнеров можно включить для новых или существующих развертываний AKS с помощью следующих методов:

* Из портал Azure, Azure PowerShell или с Azure CLI
* используя [Terraform и AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>предварительные требования
Чтобы начать, у вас должны быть следующие компоненты:

* **Рабочая область Log Analytics.**

    Azure Monitor для контейнеров поддерживает рабочую область Log Analytics в регионах, перечисленных в списке [продуктов Azure по регионам](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=monitor), исключая **US gov (Вирджиния)** региона.

    Вы можете создать рабочую область, включив наблюдение за новым кластером AKS или добавим рабочую область по умолчанию в группе ресурсов по умолчанию для подписки кластера AKS. Если вы хотите создать ее самостоятельно, используйте [Azure Resource Manager](../platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) или [портал Azure](../learn/quick-create-workspace.md). Список поддерживаемых пар сопоставлений, используемых для рабочей области по умолчанию, см. в разделе [сопоставление регионов для Azure Monitor для контейнеров](container-insights-region-mapping.md).

* Вы являетесь членом **роли участника log Analytics** , чтобы включить мониторинг контейнеров. Дополнительные сведения о том, как управлять доступом к рабочей области Log Analytics, см. в статье [Управление рабочими областями](../platform/manage-access.md).

* Вы являетесь членом роли " **[владелец](../../role-based-access-control/built-in-roles.md#owner)** " в ресурсе кластера AKS.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

* Метрики Prometheus по умолчанию не собираются. Перед [настройкой агента](container-insights-agent-config.md) для их получения важно ознакомиться с [документацией](https://prometheus.io/) по Prometheus, чтобы понять, что можно определить.

## <a name="components"></a>Компоненты

Возможность отслеживать производительность зависит от контейнерного агента Log Analytics для Linux, специально разработанного для Azure Monitor для контейнеров. Этот специализированный агент собирает данные о производительности и событиях со всех узлов кластера, а потом автоматически развертывается и регистрируется в указанной рабочей области Log Analytics во время развертывания. Версия агента — microsoft/oms:ciprod04202018 и выше. Номер представлен датой в формате *ммддгггг*.

>[!NOTE]
>В предварительном выпуске поддержки Windows Server для AKS в кластере AKS с узлами Windows Server не установлен агент для получения данных и пересылки в Azure Monitor. Вместо этого узел Linux, автоматически развернутый в кластере в рамках стандартного развертывания, собирает и пересылает данные Azure Monitor от имени всех узлов Windows в кластере.  
>

При выпуске новой версии агент автоматически обновляется в управляемых кластерах Kubernetes, размещенных в Службе Azure Kubernetes (AKS). Выпущенные версии см. в разделе [объявлений о выпуске агента](https://github.com/microsoft/docker-provider/tree/ci_feature_prod).

>[!NOTE]
>Если вы уже развернули кластер AKS, то можете включить мониторинг с помощью Azure CLI или предоставив шаблон Azure Resource Manager, как показано далее в этой статье. Невозможно использовать `kubectl` для обновления, удаления, повторного развертывания или развертывания агента.
>Развертывание шаблона должно проходить в той же группе ресурсов, что и у кластера.

Включить Azure Monitor для контейнеров можно с помощью одного из следующих методов, описанных в следующей таблице.

| Состояние развертывания | Метод | Описание |
|------------------|--------|-------------|
| Новый кластер AKS | [Создание кластера с помощью Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Вы можете включить мониторинг нового кластера AKS, созданного с помощью Azure CLI. |
| | [Создание кластера с помощью terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Вы можете включить мониторинг нового кластера AKS, созданного с помощью средства с открытым кодом terraform. |
| Существующий кластер AKS | [Включить использование Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Вы можете включить мониторинг кластера AKS, уже развернутого с помощью Azure CLI. |
| |[Включить использование terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Вы можете включить мониторинг кластера AKS, который уже развернут с помощью средства с открытым кодом terraform. |
| | [Включить из Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Вы можете включить мониторинг одного или нескольких кластеров AKS, уже развернутых на странице AKS с несколькими кластерами в Azure Monitor. |
| | [Включить из кластера AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Мониторинг можно включить непосредственно из кластера AKS в портал Azure. |
| | [Включение с помощью шаблона Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Вы можете включить мониторинг кластера AKS с помощью предварительно настроенного шаблона Azure Resource Manager. |

## <a name="next-steps"></a>Следующие шаги

* Если включен мониторинг для сбора метрик работоспособности узлов и групп pod кластера AKS, эти метрики будут доступными на портале Azure. Сведения об использовании Azure Monitor для контейнеров см. в руководстве по [просмотру данных работоспособности службы Azure Kubernetes](container-insights-analyze.md).
