---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 0177f1ffbc21cf5508b86a9c0f3c625fead34faf
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170372"
---
|Имя |Описание |Действие |Версия |
|---|---|---|---|
|[Свойство ClusterProtectionLevel в кластерах Service Fabric должно иметь значение EncryptAndSign](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |Service Fabric поддерживает три уровня защиты (None (Нет), Sign (Подписывание) и EncryptAndSign (Шифрование и подписывание)) для взаимодействия между узлами с использованием основного сертификата кластера. Задайте уровень защиты, чтобы обеспечить шифрование и цифровое подписывание всех сообщений между узлами. |Audit, Disabled |1.0.0 |
|[Кластеры Service Fabric должны использовать только Azure Active Directory для проверки подлинности клиента](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |Аудит проверки подлинности клиента только через Azure Active Directory в Service Fabric |Audit, Disabled |1.0.0 |
