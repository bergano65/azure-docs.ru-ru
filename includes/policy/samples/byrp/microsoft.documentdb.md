---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a44755805d28d4bc1edee6057af9c0c678cfccf9
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836480"
---
|Имя |Описание |Действие |Версия |GitHub |
|---|---|---|---|---|
|[Разрешенные расположения Azure Cosmos DB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0473574d-2d43-4217-aefe-941fcdf7e684) |Эта политика позволяет ограничить расположения, которые ваша организация может указать при развертывании ресурсов Azure Cosmos DB. Используется для соблюдения географических требований. |[parameters('policyEffect')] |1.0.0 |[Ссылка](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/Cosmos_Locations_Deny.json) |
|[Служба Cosmos DB должна использовать конечную точку службы виртуальной сети](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe0a2b1a3-f7f9-4569-807f-2a9edebdf4d9) |Эта политика выполняет аудит всех Cosmos DB, не настроенных на использование конечной точки службы виртуальной сети. |Audit, Disabled |1.0.0 |[Ссылка](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Network/VirtualNetworkServiceEndpoint_CosmosDB_Audit.json) |
|[Развертывание Расширенной защиты от угроз для учетных записей Cosmos DB](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fb5f04e03-92a3-4b09-9410-2cc5e5047656) |Эта политика включает Расширенную защиту от угроз для учетных записей Cosmos DB. |DeployIfNotExists, Disabled |1.0.0 |[Ссылка](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cosmos%20DB/CosmosDbAdvancedThreatProtection_Deploy.json) |
