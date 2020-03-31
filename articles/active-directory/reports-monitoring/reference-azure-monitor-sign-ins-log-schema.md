---
title: Схема входа в журнал в Azure Monitor Документы Майкрософт
description: Опишите знак Azure AD в схеме журнала для использования в Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5525f2f8ab4ef83ba9c3aeeff945bc9d875600d5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75748667"
---
# <a name="interpret-the-azure-ad-sign-in-logs-schema-in-azure-monitor"></a>Интерпретация схемы входа в журналы Azure AD в Azure Monitor

В этой статье описывается схема журналов входа Azure Active Directory (Azure AD) в Azure Monitor. Большая часть информации об операциях входа предоставляется в атрибуте *Properties* объекта `records`.


```json
{ 
    "time": "2019-03-12T16:02:15.5522137Z", 
    "resourceId": "/tenants/<TENANT ID>/providers/Microsoft.aadiam",
    "operationName": "Sign-in activity", 
    "operationVersion": "1.0", 
    "category": "SignInLogs", 
    "tenantId": "<TENANT ID>", 
    "resultType": "50140", 
    "resultSignature": "None", 
    "resultDescription": "This error occurred due to 'Keep me signed in' interrupt when the user was signing-in.", 
    "durationMs": 0, 
    "callerIpAddress": "<CALLER IP ADDRESS>", 
    "correlationId": "a75a10bd-c126-486b-9742-c03110d36262", 
    "identity": "Timothy Perkins", 
    "Level": 4, 
    "location": "US", 
    "properties": 
        {
            "id":"0231f922-93fa-4005-bb11-b344eca03c01",
            "createdDateTime":"2019-03-12T16:02:15.5522137+00:00",
            "userDisplayName":"Timothy Perkins",
            "userPrincipalName":"<USER PRINCIPAL NAME>",
            "userId":"<USER ID>",
            "appId":"<APPLICATION ID>",
            "appDisplayName":"Azure Portal",
            "ipAddress":"<IP ADDRESS>",
            "status":
            {
                "errorCode":50140,
                "failureReason":"This error occurred due to 'Keep me signed in' interrupt when the user was signing-in."
            },
            "clientAppUsed":"Browser",
            "deviceDetail":
            {
                "operatingSystem":"Windows 10",
                "browser":"Chrome 72.0.3626"
            },
            "location":
                {
                    "city":"Bellevue",
                    "state":"Washington",
                    "countryOrRegion":"US",
                    "geoCoordinates":
                    {
                        "latitude":45,
                        "longitude":122
                    }
                },
            "correlationId":"a75a10bd-c126-486b-9742-c03110d36262",
            "conditionalAccessStatus":"notApplied",
            "appliedConditionalAccessPolicies":
            [
                {
                    "id":"ae11ffaa-9879-44e0-972c-7538fd5c4d1a",
                    "displayName":"Hr app access policy",
                    "enforcedGrantControls":
                    [
                        "Mfa"
                    ],
                    "enforcedSessionControls":
                    [
                    ],
                    "result":"notApplied"
                },
                {
                    "id":"b915a70b-2eee-47b6-85b6-ff4f4a66256d",
                    "displayName":"MFA for all but global support access",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"830f27fa-67a8-461f-8791-635b7225caf1",
                    "displayName":"Header Based Application Control",
                    "enforcedGrantControls":["Mfa"],
                    "enforcedSessionControls":[],
                    "result":"notApplied"
                },
                {
                    "id":"8ed8d7f7-0a2e-437b-b512-9e47bed562e6",
                    "displayName":"MFA for everyones",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
                {
                    "id":"52924e0f-798b-4afd-8c42-49055c7d6395",
                    "displayName":"Device compliant",
                    "enforcedGrantControls":[],
                    "enforcedSessionControls":[],
                    "result":"notEnabled"
                },
             ],
            "isInteractive":true,
            "tokenIssuerType":"AzureAD",
            "authenticationProcessingDetails":[],
            "networkLocationDetails":[],
            "processingTimeInMilliseconds":0,
            "riskDetail":"hidden",
            "riskLevelAggregated":"hidden",
            "riskLevelDuringSignIn":"hidden",
            "riskState":"none",
            "riskEventTypes":[],
            "resourceDisplayName":"windows azure service management api",
            "resourceId":"797f4846-ba00-4fd7-ba43-dac1f8f63013",
            "authenticationMethodsUsed":[]
        }
}
```


## <a name="field-descriptions"></a>Описания полей

| Имя поля | Описание |
|------------|-------------|
| Time | Дата и время в формате UTC. |
| ResourceId | Это значение не сопоставлено, и его можно игнорировать.  |
| OperationName | Для операций входа это поле всегда имеет значение *Sign-in activity*. |
| OperationVersion | Запрошенная клиентом версия REST API. |
| Категория | Для операций входа это поле всегда имеет значение *SignIn*. | 
| TenantId | Связанный с журналами уникальный идентификатор (GUID) клиента. |
| ResultType | Результатом операции входа может быть *Success* или *Failure*. | 
| ResultSignature | Содержит код ошибки, которая возникла во время входа. |
| ResultDescription | Содержит описание ошибки, которая возникла во время входа. |
| рискDetail | рискDetail | Обеспечивает "причину" определенного состояния рискованного пользователя, ввода или обнаружения риска. Возможные `none`значения: , `adminGeneratedTemporaryPassword` `userPerformedSecuredPasswordChange`, `userPerformedSecuredPasswordReset` `adminConfirmedSigninSafe`, `aiConfirmedSigninSafe` `userPassedMFADrivenByRiskBasedPolicy`, `adminDismissedAllRiskForUser` `adminConfirmedSigninCompromised`, `unknownFutureValue`, . Значение `none` означает, что никаких действий не было выполнено на пользователя или входе в систему до сих пор. <br>**Примечание:** Подробная информация об этом свойстве требует лицензии Azure AD Premium P2. Другие лицензии `hidden`возвращают значение. |
| riskEventTypes | riskEventTypes | Типы обнаружения рисков, связанные с ввозами. `unlikelyTravel`Возможные значения: , `anonymizedIPAddress` `maliciousIPAddress`, `unfamiliarFeatures` `malwareInfectedIPAddress`, `suspiciousIPAddress` `leakedCredentials`, `investigationsThreatIntelligence` `generic`, `unknownFutureValue`, , и . |
| рискУгированный | рискУровень | Агрегированный уровень риска. `none`Возможные значения: , `low` `medium`, `high` `hidden`, `unknownFutureValue`и . Значение `hidden` означает, что пользователь или входе не был включен для защиты идентификационных данных Azure AD. **Примечание:** Подробная информация об этом свойстве доступна только для клиентов Azure AD Premium P2. Все остальные клиенты `hidden`будут возвращены. |
| riskLevelDuringSignin | рискУровень | Уровень риска при входе в систему. `none`Возможные значения: , `low` `medium`, `high` `hidden`, `unknownFutureValue`и . Значение `hidden` означает, что пользователь или входе не был включен для защиты идентификационных данных Azure AD. **Примечание:** Подробная информация об этом свойстве доступна только для клиентов Azure AD Premium P2. Все остальные клиенты `hidden`будут возвращены. |
| рискгосударство | рискгосударство | Сообщает о состоянии рискованного пользователя, входе или обнаружении риска. Возможные значения: `none`, `confirmedSafe` `remediated`, `dismissed` `atRisk`, `confirmedCompromised` `unknownFutureValue`, . |
| DurationMs |  Это значение не сопоставлено, и его можно игнорировать. |
| CallerIpAddress | IP-адрес отправившего запрос клиента. | 
| CorrelationId | Необязательный код GUID, который передал клиент. Это значение может помочь найти связь между операциями на стороне клиента и операциями на стороне сервера. Кроме того, оно может пригодиться при отслеживании журналов, в которые записываются данные о многих службах. |
| идентификации | Удостоверение из маркера, предоставленное при выполнении запроса. Это может быть учетная запись пользователя, системная учетная запись или субъект-служба. |
| Level | Предоставляет тип сообщения. Для операций аудита это поле всегда имеет значение *Informational*. |
| Расположение | Содержит сведения о местонахождении, в котором выполнялась операция входа. |
| Свойства | Перечисляет все свойства, связанные со вводами. Для получения дополнительной [Microsoft Graph API Reference](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)информации см. Для удобства чтения в этой схеме используются имена атрибутов, отображаемые в ресурсе входа.

## <a name="next-steps"></a>Дальнейшие действия

* [Интерпретация схемы журналов аудита в Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Дополнительные сведения о журналах диагностики Azure](../../azure-monitor/platform/platform-logs-overview.md).
