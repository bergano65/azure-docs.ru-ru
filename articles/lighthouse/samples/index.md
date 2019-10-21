---
title: Примеры и шаблоны Azure Lighthouse
description: В этих примерах и шаблонах Azure Resource Manager показано, как подключить клиентов для делегированного управления ресурсами Azure и поддержки сценариев Azure Lighthouse.
author: JnHs
manager: carmonm
ms.service: lighthouse
ms.topic: sample
ms.date: 10/17/2019
ms.author: jenhayes
ms.openlocfilehash: 6d47534026b6fe815f9756a74ba3438dc67a8e02
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72553684"
---
# <a name="azure-lighthouse-samples"></a>Примеры Azure Lighthouse

В приведенной ниже таблице представлены ссылки на шаблоны Azure Resource Manager для Azure Lighthouse. Эти файлы и многое другое также можно найти в [репозитории примеров для Azure Lighthouse](https://github.com/Azure/Azure-Lighthouse-samples/).

## <a name="onboarding-customers-to-azure-delegated-resource-management"></a>Подключение клиентов к системе делегированного управления ресурсами Azure

Мы предоставляем различные шаблоны для конкретных сценариев адаптации. Выберите наиболее подходящий вариант и обязательно измените файл параметров в соответствии со своей средой. Дополнительные сведения о том, как использовать эти файлы в развертывании, см. в статье о [подключении клиентов к системе делегированного управления ресурсами Azure](../how-to/onboard-customer.md).

| **Шаблон** | **Описание** |
|---------|---------|
| [delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegated-resource-management) | Подключение подписки клиента к системе делегированного управления ресурсами Azure. Для каждой подписки необходимо выполнить отдельное развертывание. |
| [rg-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/rg-delegated-resource-management) | Подключение одной или нескольких групп ресурсов клиента к системе делегированного управления ресурсами Azure. С помощью **rgDelegatedResourceManagement** можно подключить одну группу ресурсов, а с помощью **multipleRgDelegatedResourceManagement** — несколько групп ресурсов в одной подписке. |
| [marketplace-delegated-resource-management](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/marketplace-delegated-resource-management) | Если вы [опубликовали предложение управляемых служб в Azure Marketplace](../how-to/publish-managed-services-offers.md), с помощью этого шаблона можно адаптировать ресурсы для клиентов, которые приняли предложение. Значения marketplace в файле параметров должны совпадать со значениями, которые использовались при публикации предложения. |

Как правило, для каждой подписки требуется отдельное развертывание. Но можно развернуть шаблоны и для нескольких подписок.

| **Шаблон** | **Описание** |
|---------|---------|
| [cross-subscription-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-subscription-deployment) | Развертывание шаблонов Azure Resource Manager в нескольких подписках. |

## <a name="azure-policy"></a>Политика Azure

В этих примерах показано, как использовать Политику Azure с подписками, подключенными для делегированного управления ресурсами Azure.

| **Шаблон** | **Описание** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | Назначение политики, которая отвечает за добавление или удаление тега (с помощью действия изменения) в делегированной подписке. Дополнительные сведения см. в статье о [развертывании политики, которую можно исправить в рамках делегированной подписки](../how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | Назначение политики, которая отвечает за аудит для назначений делегирования. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | Назначение политики для диагностики ресурсов Azure Key Vault в делегированных подписках (с использованием эффекта deployIfNotExists). Дополнительные сведения см. в статье о [развертывании политики, которую можно исправить в рамках делегированной подписки](../how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | Назначение нескольких политик для диагностики делегированной подписки, а также подключение всех виртуальных машин Windows и Linux к рабочей области Log Analytics, созданной с помощью политики. Дополнительные сведения см. в статье о [развертывании политики, которую можно исправить в рамках делегированной подписки](../how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | Применение инициативы (нескольких связанных определений политик) к делегированной подписке. |

## <a name="azure-monitor"></a>Azure Monitor

В этих примерах показано, как с помощью Azure Monitor создавать оповещения для подписок, подключенных для делегированного управления ресурсами Azure.

| **Шаблон** | **Описание** |
|---------|---------|
| [alert-using-actiongroup](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/alert-using-actiongroup) | Этот шаблон отвечает за создание оповещения Azure и подключение к существующей группе действий.|
| [multiple-loganalytics-alerts](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/multiple-loganalytics-alerts) | Создание нескольких оповещений журнала на основе запросов Kusto.|
| [delegation-alert-for-customer](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/delegation-alert-for-customer) | Развертывание оповещения в клиенте при делегировании пользователем подписки на управляющий клиент.|

## <a name="additional-cross-tenant-scenarios"></a>Дополнительные межклиентские сценарии

Эти примеры иллюстрируют различные задачи, которые можно выполнять в межклиентских сценариях управления.

| **Шаблон** | **Описание** |
|---------|---------|
| [cross-rg-deployment](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/cross-rg-deployment) | Развертывание учетных записей хранения в двух разных группах ресурсов.|
| [deploy-azure-mgmt-services](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-mgmt-services) | Создание служб управления Azure, их связывание и развертывание дополнительных решений. Шаблон **rgWithAzureMgmt.json** поможет выполнить комплексное развертывание. |
| [deploy-azure-security-center](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-security-center) | Включение и настройка Центра безопасности Azure в целевой подписке Azure. |
| [deploy-azure-sentinel](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-azure-sentinel) | Развертывание и включение Azure Sentinel в существующей рабочей области Log Analytics в делегированной подписке. |
| [deploy-log-analytics-vm-extensions](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/deploy-log-analytics-vm-extensions) | Эти шаблоны позволяют развертывать расширения Log Analytics для виртуальных машин на виртуальных машинах Windows и Linux, подключив их к назначенной рабочей области Log Analytics. |

## <a name="next-steps"></a>Дополнительная информация

- Ознакомьтесь со сведениями о [делегированном управлении ресурсами Azure](../concepts/azure-delegated-resource-management.md).
- Изучите [примеры в этом репозитории Azure Lighthouse](https://github.com/Azure/Azure-Lighthouse-samples/).
