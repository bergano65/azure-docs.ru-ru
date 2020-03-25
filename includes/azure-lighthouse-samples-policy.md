---
title: включить файл
description: включить файл
services: lighthouse
author: JnHs
ms.service: lighthouse
ms.topic: include
ms.date: 12/19/2019
ms.author: jenhayes
ms.custom: include file
ms.openlocfilehash: 55eee87ed1e07b61a7f8287e86ddc0a7a6aa4df9
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/25/2020
ms.locfileid: "75456778"
---
В этих примерах показано, как использовать Политику Azure с подписками, подключенными для делегированного управления ресурсами Azure.

| **Шаблон** | **Описание** |
|---------|---------|
| [policy-add-or-replace-tag](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-add-or-replace-tag) | Назначение политики, которая отвечает за добавление или удаление тега (с помощью действия изменения) в делегированной подписке. Дополнительные сведения см. в статье о [развертывании политики, которую можно исправить в рамках делегированной подписки](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-audit-delegation](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-audit-delegation) | Назначение политики, которая отвечает за аудит для назначений делегирования. |
| [policy-enforce-keyvault-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-keyvault-monitoring) | Назначение политики для диагностики ресурсов Azure Key Vault в делегированных подписках (с использованием эффекта deployIfNotExists). Дополнительные сведения см. в статье о [развертывании политики, которую можно исправить в рамках делегированной подписки](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-enforce-sub-monitoring](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-enforce-sub-monitoring) | Назначение нескольких политик для диагностики делегированной подписки, а также подключение всех виртуальных машин Windows и Linux к рабочей области Log Analytics, созданной с помощью политики. Дополнительные сведения см. в статье о [развертывании политики, которую можно исправить в рамках делегированной подписки](../articles/lighthouse/how-to/deploy-policy-remediation.md). |
| [policy-initiative](https://github.com/Azure/Azure-Lighthouse-samples/tree/master/Azure-Delegated-Resource-Management/templates/policy-initiative) | Применение инициативы (нескольких связанных определений политик) к делегированной подписке. |

