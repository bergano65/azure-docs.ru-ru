---
title: Ведение журнала Azure Key Vault | Документация Майкрософт
description: Сведения о том, как отслеживать доступ к хранилищам ключей, включив функцию ведения журнала Azure Key Vault, которая сохраняет информацию в указанной учетной записи хранения Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 5423fc27ecc58bcd79b36a845e4b7569f342f712
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93286695"
---
# <a name="azure-key-vault-logging"></a>Ведение журнала Azure Key Vault

Создав одно или несколько хранилищ ключей вы, вероятно, захотите отслеживать, кто, как и когда осуществлял доступ к этим хранилищам. Это можно сделать с помощью функции ведения журнала Azure Key Vault, которая сохраняет информацию в указанной учетной записи хранения Azure. Пошаговые инструкции по настройке этой функции см. в статье [Включение ведения журнала Key Vault](howto-logging.md).

Регистрируемые в журналах сведения становятся доступны не позднее чем через 10 минут после выполнения операции с хранилищем ключей. В большинстве случаев это будет еще быстрее.  Способ управления журналами в своей учетной записи хранения вы выбираете сами.

* Используйте стандартные методы контроля доступа, предоставляемые Azure, для защиты журналов путем ограничения доступа к ним.
* Удаляйте журналы, которые больше не нужно хранить в учетной записи хранения.

Общие сведения о Key Vault см. в статье [Что такое хранилище ключей Azure?](overview.md) Сведения о регионах доступности Key Vault можно узнать на [странице цен](https://azure.microsoft.com/pricing/details/key-vault/). Сведения об использовании Azure Monitor для Key Vault см. [здесь](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="interpret-your-key-vault-logs"></a>Интерпретация журналов хранилища ключей

При включении ведения журнала для указанной вами учетной записи хранения автоматически создается контейнер с именем **insights-logs-auditevent**. Эту же учетную запись можно использовать для сбора журналов нескольких хранилищ ключей.

Отдельные BLOB-объекты хранятся как текст в формате JSON. Давайте рассмотрим пример записи журнала. 

```json
    {
        "records":
        [
            {
                "time": "2016-01-05T01:32:01.2691226Z",
                "resourceId": "/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSOGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT",
                "operationName": "VaultGet",
                "operationVersion": "2015-06-01",
                "category": "AuditEvent",
                "resultType": "Success",
                "resultSignature": "OK",
                "resultDescription": "",
                "durationMs": "78",
                "callerIpAddress": "104.40.82.76",
                "correlationId": "",
                "identity": {"claim":{"http://schemas.microsoft.com/identity/claims/objectidentifier":"d9da5048-2737-4770-bd64-XXXXXXXXXXXX","http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn":"live.com#username@outlook.com","appid":"1950a258-227b-4e31-a9cf-XXXXXXXXXXXX"}},
                "properties": {"clientInfo":"azure-resource-manager/2.0","requestUri":"https://control-prod-wus.vaultcore.azure.net/subscriptions/361da5d4-a47a-4c79-afdd-XXXXXXXXXXXX/resourcegroups/contosoresourcegroup/providers/Microsoft.KeyVault/vaults/contosokeyvault?api-version=2015-06-01","id":"https://contosokeyvault.vault.azure.net/","httpStatusCode":200}
            }
        ]
    }
```

В следующей таблице перечислены имена и описания полей.

| Имя поля | Описание |
| --- | --- |
| **time** |Дата и время (в формате UTC). |
| **resourceId** |Идентификатор ресурса Azure Resource Manager. Для журналов хранилища ключей это всегда идентификатор ресурса хранилища ключей. |
| **operationName** |Имя операции, как описано в следующей таблице. |
| **operationVersion** |Запрошенная клиентом версия REST API. |
| **category** |Тип результата. Для журналов Key Vault единственным доступным значением является `AuditEvent`. |
| **resultType** |Результат запроса к REST API. |
| **resultSignature** |Состояние HTTP. |
| **resultDescription** |Дополнительное описание результата, если доступно. |
| **durationMs** |Время обслуживания запроса REST API в миллисекундах. Сюда не входит задержка сети, поэтому время, зарегистрированное на стороне клиента, может не соответствовать этому значению. |
| **callerIpAddress** |IP-адрес клиента, выполнившего запрос. |
| **correlationId** |Необязательный GUID, который клиент может передавать для сопоставления журналов на стороне клиента с журналами на стороне службы (хранилища ключей). |
| **identity** |Удостоверение из маркера, предоставляемое в запросе к REST API. Обычно это "пользователь", "субъект-служба" или комбинация "пользователь + идентификатор приложения", как например при запросе из командлета Azure PowerShell. |
| **properties** |Эта информация зависит от типа операции ( **operationName** ). В большинстве случаев это поле содержит сведения о клиенте (передаваемая клиентом строка useragent), точный URI запроса REST API и код состояния HTTP. Кроме того, когда результат запроса содержит объект (например, **KeyCreate** или **VaultGet** ), это поле содержит еще и URI ключа (в значении `id`), URI хранилища или URI секрета. |

Значения поля **operationName** отображаются в формате *ObjectVerb*. Пример:

* Все операции с хранилищем ключей отображаются в формате `Vault<action>`, например `VaultGet` или `VaultCreate`.
* Все операции с ключами отображаются в формате `Key<action>`, например `KeySign` или `KeyList`.
* Все операции с секретами отображаются в формате `Secret<action>`, например `SecretGet` или `SecretListVersions`.

В следующей таблице перечислены значения **operationName** и соответствующие команды REST API.

### <a name="operation-names-table"></a>Таблица имен операций

| operationName | Команда REST API |
| --- | --- |
| **Аутентификация** |Аутентификация через конечную точку Azure Active Directory |
| **VaultGet** |[Получение сведений о хранилище ключей](/rest/api/keyvault/vaults) |
| **VaultPut** |[Создание или обновление хранилища ключей](/rest/api/keyvault/vaults) |
| **VaultDelete** |[Удаление хранилища ключей](/rest/api/keyvault/vaults) |
| **VaultPatch** |[Update a key vault (Создание или обновление хранилища ключей)](/rest/api/keyvault/vaults) |
| **VaultList** |[Список всех хранилищ ключей в группе ресурсов](/rest/api/keyvault/vaults) |
| **KeyCreate** |[Создание ключа](/rest/api/keyvault/createkey) |
| **KeyGet** |[Получение сведений о ключе](/rest/api/keyvault/getkey) |
| **KeyImport** |[Импорт ключа в хранилище](/rest/api/keyvault/vaults) |
| **KeyBackup** |[Резервное копирование ключа](/rest/api/keyvault/backupkey) |
| **KeyDelete** |[Удаление ключа](/rest/api/keyvault/deletekey) |
| **KeyRestore** |[Восстановление ключа](/rest/api/keyvault/restorekey) |
| **KeySign** |[Вход с помощью ключа](/rest/api/keyvault/sign) |
| **KeyVerify** |[Проверка с помощью ключа](/rest/api/keyvault/vaults) |
| **KeyWrap** |[Перенос ключа](/rest/api/keyvault/wrapkey) |
| **KeyUnwrap** |[Развертывание ключа](/rest/api/keyvault/unwrapkey) |
| **KeyEncrypt** |[Шифрование с помощью ключа](/rest/api/keyvault/encrypt) |
| **KeyDecrypt** |[Расшифровка с помощью ключа](/rest/api/keyvault/decrypt) |
| **KeyUpdate** |[Обновление ключа](/rest/api/keyvault/updatekey) |
| **KeyList** |[Перечисление ключей в хранилище](/rest/api/keyvault/vaults) |
| **KeyListVersions** |[Перечисление версий ключа](/rest/api/keyvault/getkeyversions) |
| **SecretSet** |[Создание секрета](/rest/api/keyvault/updatecertificate) |
| **SecretGet** |[Получение секрета](/rest/api/keyvault/getsecret) |
| **SecretUpdate** |[Обновление секрета](/rest/api/keyvault/updatesecret) |
| **SecretDelete** |[Удаление секрета](/rest/api/keyvault/deletesecret) |
| **SecretList** |[Список секретов в хранилище](/rest/api/keyvault/vaults) |
| **SecretListVersions** |[Список версий секрета](/rest/api/keyvault/getsecretversions) |
| **VaultAccessPolicyChangedEventGridNotification** | Опубликованное событие изменения политики доступа к хранилищу |
| **SecretNearExpiryEventGridNotification** |Опубликованное событие приближения истечения срока действия секрета |
| **SecretExpiredEventGridNotification** |Опубликованное событие истечения срока действия секрета |
| **KeyNearExpiryEventGridNotification** |Опубликованное событие приближения истечения срока действия ключа |
| **KeyExpiredEventGridNotification** |Опубликованное событие истечения срока действия ключа |
| **CertificateNearExpiryEventGridNotification** |Опубликованное событие приближения истечения срока действия сертификата |
| **CertificateExpiredEventGridNotification** |Опубликованное событие истечения срока действия сертификата |

## <a name="use-azure-monitor-logs"></a>Использование журналов Azure Monitor

Решение Key Vault в журналах Azure Monitor позволяет просматривать журналы `AuditEvent` для Key Vault. В журналах Azure Monitor запросы по журналам используются для анализа данных и получения необходимых сведений. 

Дополнительные сведения, включая инструкции по настройке, см. в разделе [Мониторинг службы Key Vault с помощью Azure Monitor для Key Vault (предварительная версия)](../../azure-monitor/insights/key-vault-insights-overview.md).

## <a name="next-steps"></a>Дальнейшие действия

- [Включение ведения журнала Key Vault](howto-logging.md)
- Руководство по использованию Azure Key Vault в веб-приложении .NET см. в [этой статье](tutorial-net-create-vault-azure-web-app.md).
- Справочные материалы по программированию см. в статье [Руководство разработчика хранилища ключей Azure](developers-guide.md).
- Полный список командлетов Azure PowerShell 1.0 для хранилища ключей Azure см. в статье [Azure Key Vault cmdlets](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault) (Командлеты хранилища ключей Azure).