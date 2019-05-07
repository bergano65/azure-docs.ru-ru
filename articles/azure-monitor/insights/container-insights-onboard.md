---
title: Подключение Azure Monitor для контейнеров | Документация Майкрософт
description: В этой статье объясняется, как подключить и настроить Azure Monitor для контейнеров, чтобы получать сведения о работе контейнера и выявленных проблемах с его производительностью.
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
ms.date: 04/25/2019
ms.author: magoedte
ms.openlocfilehash: 10b80a9749c5698195ac5d3493ac3b07fd6e24e1
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073312"
---
# <a name="how-to-onboard-azure-monitor-for-containers"></a>Подключение Azure Monitor для контейнеров  

В этой статье представлен обзор параметров, доступных для настройки Azure Monitor для контейнеров, чтобы отслеживать производительность рабочих нагрузок, развернутых сред Kubernetes и размещенных на [службе Azure Kubernetes](https://docs.microsoft.com/azure/aks/).

Azure Monitor для контейнеров можно включить для новых или существующих развертываний AKS с помощью следующих методов:

* На портале Azure, Azure PowerShell или с помощью Azure CLI
* используя [Terraform и AKS](../../terraform/terraform-create-k8s-cluster-with-tf-and-aks.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Технические условия 
Чтобы начать, у вас должны быть следующие компоненты:

- **Рабочая область Log Analytics.** Ее можно создать при включении мониторинга нового кластера AKS. Кроме того, средство подключения может создать рабочую область по умолчанию в стандартной группе ресурсов подписки кластера AKS. Если вы хотите создать ее самостоятельно, используйте [Azure Resource Manager](../platform/template-workspace-configuration.md), [PowerShell](../scripts/powershell-sample-create-workspace.md?toc=%2fpowershell%2fmodule%2ftoc.json) или [портал Azure](../learn/quick-create-workspace.md).
- Вы являетесь членом **роль "участник" Log Analytics** Чтобы включить мониторинг контейнеров. Дополнительные сведения о том, как управлять доступом к рабочей области Log Analytics, см. в статье [Управление рабочими областями](../platform/manage-access.md).
- Вы являетесь членом **[владельца](../../role-based-access-control/built-in-roles.md#owner)** роли на ресурс кластера AKS. 

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

## <a name="components"></a>Компоненты 

Возможность отслеживать производительность зависит от контейнерного агента Log Analytics для Linux, специально разработанного для Azure Monitor для контейнеров. Этот специализированный агент собирает данные о производительности и событиях со всех узлов кластера, а потом автоматически развертывается и регистрируется в указанной рабочей области Log Analytics во время развертывания. Версия агента — microsoft/oms:ciprod04202018 и выше. Номер представлен датой в формате *ммддгггг*. 

>[!NOTE]
>С помощью предварительной версии поддержка Windows Server для AKS кластер AKS с узлами Windows Server нет агент, установленный для сбора данных и пересылать их в Azure Monitor. Вместо этого автоматически развертывается в кластере как часть стандартного развертывания узла Linux собирает и пересылает данные Azure Monitor от имени всех узлов Windows в кластере.  
>

При выпуске новой версии агент автоматически обновляется в управляемых кластерах Kubernetes, размещенных в Службе Azure Kubernetes (AKS). Выпущенные версии см. в разделе [объявлений о выпуске агента](https://github.com/microsoft/docker-provider/tree/ci_feature_prod). 

>[!NOTE] 
>Если вы уже развернули кластер AKS, то можете включить мониторинг с помощью Azure CLI или предоставив шаблон Azure Resource Manager, как показано далее в этой статье. Невозможно использовать `kubectl` для обновления, удаления, повторного развертывания или развертывания агента. Развертывание шаблона должно проходить в той же группе ресурсов, что и у кластера.

Включение Azure Monitor для контейнеров с помощью одного из следующих методов, описанных в следующей таблице.

| Состояние развертывания | Метод | ОПИСАНИЕ | 
|------------------|--------|-------------| 
| Новый кластер AKS | [Создание кластера с помощью Azure CLI](../../aks/kubernetes-walkthrough.md#create-aks-cluster)| Вы можете включить наблюдение нового кластера AKS, созданный с помощью Azure CLI. | 
| | [Создание кластера с помощью Terraform](container-insights-enable-new-cluster.md#enable-using-terraform)| Вы можете включить наблюдение нового кластера AKS, созданный с помощью инструмента с открытым кодом Terraform. | 
| Существующий кластер AKS | [Включить с помощью Azure CLI](container-insights-enable-existing-clusters.md#enable-using-azure-cli) | Вы можете включить наблюдение за кластер AKS, уже развернут с помощью Azure CLI. | 
| |[Включить с помощью Terraform](container-insights-enable-existing-clusters.md#enable-using-terraform) | Вы можете включить наблюдение за кластер AKS, уже развернут с помощью инструмента с открытым кодом Terraform. | 
| | [Включить из Azure Monitor](container-insights-enable-existing-clusters.md#enable-from-azure-monitor-in-the-portal)| Вы можете включить наблюдение за один или несколько кластеров AKS, уже развернут со страницы несколькими кластера AKS в Azure Monitor. | 
| | [Включить из кластера AKS](container-insights-enable-existing-clusters.md#enable-directly-from-aks-cluster-in-the-portal)| Вы можете включить наблюдение непосредственно из кластера AKS на портале Azure. | 
| | [Включить с помощью шаблона Azure Resource Manager](container-insights-enable-existing-clusters.md#enable-using-an-azure-resource-manager-template)| Вы можете включить мониторинг кластера AKS с помощью предварительно настроенных шаблона Azure Resource Manager. | 

## <a name="next-steps"></a>Дальнейшие действия

* Если включен мониторинг для сбора метрик работоспособности узлов и групп pod кластера AKS, эти метрики будут доступными на портале Azure. Сведения об использовании Azure Monitor для контейнеров см. в руководстве по [просмотру данных работоспособности службы Azure Kubernetes](container-insights-analyze.md).
