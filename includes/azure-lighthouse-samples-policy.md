---
title: включить файл
description: включить файл
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 10/12/2020
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 20d80fe1a09d388552b6289f8a9b23c878672f94
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/13/2020
ms.locfileid: "91974297"
---
В этих примерах показано, как использовать Политику Azure с подписками, подключенными к Azure Lighthouse.

| **Шаблон** | **Описание** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-add-or-replace-tag) | Назначение политики, которая отвечает за добавление или удаление тега (с помощью действия изменения) в делегированной подписке. Дополнительные сведения см. в статье о [развертывании политики, которую можно исправить в рамках делегированной подписки](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-allow-certain-managing-tenants](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-allow-certain-managing-tenants) | Назначение политики, разрешающей делегирование Azure Lighthouse только для определенных управляющих клиентов. |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-audit-delegation) | Назначение политики, которая отвечает за аудит для назначений делегирования. |
| [policy-delegate-management-groups](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-delegate-management-groups) | Отвечает за назначение политики для подтверждения того, что подписки в группе управления делегированы управляющему клиенту. Если это не так, создается соответствующее назначение.
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-keyvault-monitoring) | Назначение политики для диагностики ресурсов Azure Key Vault в делегированных подписках (с использованием эффекта deployIfNotExists). Дополнительные сведения см. в статье о [развертывании политики, которую можно исправить в рамках делегированной подписки](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-enforce-sub-monitoring) | Назначение нескольких политик для диагностики делегированной подписки, а также подключение всех виртуальных машин Windows и Linux к рабочей области Log Analytics, созданной с помощью политики. Дополнительные сведения см. в статье о [развертывании политики, которую можно исправить в рамках делегированной подписки](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/templates/policy-initiative) | Применение инициативы (нескольких связанных определений политик) к делегированной подписке. |

