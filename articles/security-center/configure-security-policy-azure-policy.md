---
title: Создание и изменение политик безопасности политики Azure с помощью REST API | Документация Майкрософт
description: Сведения об управлении политиками политики Azure с помощью REST API.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 56c5ca8c4e1d70e002a338c753f9ab1f0b1aa411
ms.sourcegitcommit: 3f8017692169bd75483eefa96c225d45cd497f06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73522025"
---
# <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>Настройка политики безопасности в политике Azure с помощью REST API

Как часть интеграции платформенной функциональности с Политикой Azure Центр безопасности Azure позволяет создавать назначения политик с помощью REST API для Политики Azure. Приведенные ниже инструкции помогут создать назначения политик, а также настроить существующие назначения. 

Важные концепции в Политике Azure: 

- **Определение политики** — это правило. 

- **Инициатива** — это набор определений политик (правил). 

- **Назначение** — это приложение инициативы или политика для определенной области (Группа управления, подписка и т. д.). 

В Центре безопасности Azure есть встроенная инициатива, которая содержит все его политики безопасности. Чтобы оценить политики центра безопасности в ресурсах Azure, следует создать назначение в группе управления или подписке, которую необходимо оценить.

Встроенные инициативы имеют все политики Центра безопасности Azure, которые включены по умолчанию. Вы можете отключить определенные политики от встроенной инициативы. Например, чтобы применить все политики центра безопасности, кроме **брандмауэра веб-приложения**, измените значение параметра "воздействие политики" на " **отключено**". 

## <a name="api-examples"></a>Примеры с API

В следующих примерах замените указанные ниже переменные:

- **{Scope}** введите имя группы управления или подписки, к которой применяется политика.
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

## Справочник по именам политик <a name="policy-names"></a>

|Имя политики в Центре безопасности Azure|Имя политики, отображаемое в службе "Политика Azure" |Имя параметра действия политики|
|----|----|----|
|Шифрование SQL |Мониторинг баз данных SQL без шифрования в Центре безопасности Azure |sqlEncryptionMonitoringEffect| 
|Аудит SQL |Мониторинг баз данных SQL без аудита в Центре безопасности Azure |sqlAuditingMonitoringEffect|
|Обновление системы |Мониторинг отсутствия обновлений системы в Центре безопасности Azure |systemUpdatesMonitoringEffect|
|Шифрование хранилища |Аудит отсутствия шифрования больших двоичных объектов для учетных записей хранения |storageEncryptionMonitoringEffect|
|JIT-доступ к сети |Мониторинг возможных JIT-доступа к сети в центре безопасности Azure |jitNetworkAccessMonitoringEffect |
|Адаптивные элементы управления приложениями |Мониторинг возможных списков разрешенных приложений в Центре безопасности Azure |adaptiveApplicationControlsMonitoringEffect|
|Группы безопасности сети |Мониторинг нестрогого доступа к сети в Центре безопасности Azure |networkSecurityGroupsMonitoringEffect| 
|Конфигурации системы безопасности |Мониторинг уязвимостей ОС в Центре безопасности Azure |systemConfigurationsMonitoringEffect| 
|Защита конечных точек |Мониторинг отсутствия Endpoint Protection в Центре безопасности Azure |endpointProtectionMonitoringEffect |
|Шифрование дисков |Мониторинг незашифрованных дисков виртуальных машин в Центре безопасности Azure |diskEncryptionMonitoringEffect|
|Оценка уязвимостей |Мониторинг уязвимостей виртуальных машин в Центре безопасности Azure |vulnerabilityAssessmentMonitoringEffect|
|Брандмауэр веб-приложения |Мониторинг незащищенных веб-приложений в Центре безопасности Azure |webApplicationFirewallMonitoringEffect |
|Брандмауэр следующего поколения |Мониторинг незащищенных сетевых конечных точек в Центре безопасности Azure| |


## <a name="next-steps"></a>Дальнейшие действия

Другие связанные материалы см. в следующих статьях: 

- [Пользовательские политики безопасности](custom-security-policies.md)
- [Общие сведения о политике безопасности](tutorial-security-policy.md)