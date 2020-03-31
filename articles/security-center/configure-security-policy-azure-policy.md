---
title: Создание и отсвазание политик и средств политики Azure с помощью API REST
description: Узнайте об управлении политикой Azure через API REST.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: c218b5dc8ca3bfa0358a9b6a0d4867696762a8d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77430947"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Настройка политики безопасности в политике Azure с помощью API REST

Как часть интеграции платформенной функциональности с Политикой Azure Центр безопасности Azure позволяет создавать назначения политик с помощью REST API для Политики Azure. Приведенные ниже инструкции помогут создать назначения политик, а также настроить существующие назначения. 

Важные концепции в Политике Azure: 

- **Определение политики** — это правило 

- **Инициатива** представляет собой набор определений политики (правил) 

- **Назначение** представляет собой применение инициативы или политики к определенной сфере (группа управления, подписка и т.д.) 

В Центре безопасности Azure есть встроенная инициатива, которая содержит все его политики безопасности. Чтобы оценить политики Центра безопасности на ресурсах Azure, необходимо создать назначение в группе управления или подписку, оцениваемую.

Встроенные инициативы имеют все политики Центра безопасности Azure, которые включены по умолчанию. Вы можете отключить определенные политики от встроенной инициативы. Например, чтобы применить все политики Центра безопасности, кроме **брандмауэра веб-приложений,** измените значение параметра эффекта политики на **отключенный.** 

## <a name="api-examples"></a>Примеры с API

В следующих примерах замените указанные ниже переменные:

- **«область»** введите имя группы управления или подписку, на которую вы применяете политику.
- **{policyAssignmentName}**  — вставьте [имя назначения соответствующей политики](#policy-names);
- **{name}**  — введите свое имя или имя администратора, который утвердил изменение политики.

В этом примере показано, как присвоить встроенную инициативу Центра безопасности Azure подписке или группе управления.
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

В этом примере показано, как присвоить подписке встроенную инициативу Центра безопасности Azure со следующими отключенными политиками: 

- обновления системы (systemUpdatesMonitoringEffect); 

- конфигурации безопасности (systemConfigurationsMonitoringEffect); 

- Endpoint Protection (endpointProtectionMonitoringEffect). 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
В этом примере показано, как удалять назначения:
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

## <a name="policy-names-reference"></a>Справочник по именам политик <a name="policy-names"></a>

|Имя политики в Центре безопасности Azure|Имя политики, отображаемое в службе "Политика Azure" |Имя параметра действия политики|
|----|----|----|
|Шифрование SQL |Мониторинг баз данных SQL без шифрования в Центре безопасности Azure |sqlEncryptionMonitoringEffect| 
|Аудит SQL |Мониторинг баз данных SQL без аудита в Центре безопасности Azure |sqlAuditingMonitoringEffect|
|Обновление системы |Мониторинг отсутствия обновлений системы в Центре безопасности Azure |systemUpdatesMonitoringEffect|
|Шифрование хранилища |Аудит отсутствия шифрования больших двоичных объектов для учетных записей хранения |storageEncryptionMonitoringEffect|
|JIT-доступ к сети |Мониторинг возможного доступа к сети (JIT) в Центре безопасности Azure |jitNetworkAccessMonitoringEffect |
|Адаптивные элементы управления приложениями |Мониторинг возможных списков разрешенных приложений в Центре безопасности Azure |adaptiveApplicationControlsMonitoringEffect|
|Группы безопасности сети |Мониторинг нестрогого доступа к сети в Центре безопасности Azure |networkSecurityGroupsMonitoringEffect| 
|Конфигурации системы безопасности |Мониторинг уязвимостей ОС в Центре безопасности Azure |systemConfigurationsMonitoringEffect| 
|Защита конечных точек |Мониторинг отсутствия Endpoint Protection в Центре безопасности Azure |endpointProtectionMonitoringEffect |
|Шифрование дисков |Мониторинг незашифрованных дисков виртуальных машин в Центре безопасности Azure |diskEncryptionMonitoringEffect|
|Оценка уязвимостей |Мониторинг уязвимостей виртуальных машин в Центре безопасности Azure |vulnerabilityAssessmentMonitoringEffect|
|Брандмауэр веб-приложения |Мониторинг незащищенных веб-приложений в Центре безопасности Azure |webApplicationFirewallMonitoringEffect |
|Брандмауэр следующего поколения |Мониторинг незащищенных сетевых конечных точек в Центре безопасности Azure| |


## <a name="next-steps"></a>Дальнейшие действия

Для других связанных с ними материалов см. 

- [Пользовательские политики безопасности](custom-security-policies.md)
- [Обзор политики безопасности](tutorial-security-policy.md)