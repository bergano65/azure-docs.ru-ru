---
title: Azure Key Vault решение в Azure Monitor | Документация Майкрософт
description: Для просмотра журналов Azure Key Vault можно использовать решение Azure Key Vault в Azure Monitor.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 5e25e6d6-dd20-4528-9820-6e2958a40dae
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: bwren
ms.openlocfilehash: b7d9ff760bac06602d8d770a358c8a2e22a72c81
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68849194"
---
# <a name="azure-key-vault-analytics-solution-in-azure-monitor"></a>Решение Azure Key Vault Analytics в Azure Monitor

![Символ Key Vault](media/azure-key-vault/key-vault-analytics-symbol.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Для просмотра журналов Azure Key Vault AuditEvent можно использовать решение Azure Key Vault в Azure Monitor.

Чтобы использовать решение, необходимо включить ведение журнала диагностики хранилища ключей Azure и направить диагностику в рабочую область Log Analytics. Необязательно записывать журналы в хранилище BLOB-объектов Azure.

> [!NOTE]
> В январе 2017 г. поддерживаемый способ отправки журналов из Key Vault в Log Analytics был изменен. Если в используемом вами решении Key Vault в названии отображается *(не рекомендуется)* , то выполните действия, описанные в разделе [Миграция из устаревшего решения Key Vault](#migrating-from-the-old-key-vault-solution).
>
>

## <a name="install-and-configure-the-solution"></a>Установка и настройка решения
Установите и настройте решение хранилища ключей Azure, выполнив следующие указания:

1. Используйте процесс, описанный в статье [добавление Azure Monitor решений из коллекция решений](../../azure-monitor/insights/solutions.md) , чтобы добавить Azure Key Vault решение в рабочую область log Analytics.
2. Включите ведение журнала диагностики для мониторинга ресурсов Key Vault, используя [портал](#enable-key-vault-diagnostics-in-the-portal) или [PowerShell](#enable-key-vault-diagnostics-using-powershell).

### <a name="enable-key-vault-diagnostics-in-the-portal"></a>Включение диагностики Key Vault на портале

1. На портале Azure перейдите к ресурсу Key Vault, который необходимо отслеживать.
2. Выберите *параметры диагностики* , чтобы открыть следующую страницу.

   ![изображение плитки "Хранилище ключей Azure"](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics01.png)
3. Щелкните *Включить диагностику*, чтобы открыть следующую страницу:

   ![изображение плитки "Хранилище ключей Azure"](media/azure-key-vault/log-analytics-keyvault-enable-diagnostics02.png)
4. Задайте имя для параметра диагностики.
5. Установите флажок *Send to Log Analytics* (Отправить в Log Analytics).
6. Выберите существующую рабочую область Log Analytics или создайте новую.
7. Чтобы включить журналы *AuditEvent*, установите соответствующий флажок в разделе "Журнал".
8. Нажмите кнопку *сохранить* , чтобы включить ведение журнала диагностики в log Analytics рабочей области.

### <a name="enable-key-vault-diagnostics-using-powershell"></a>Включение диагностики Key Vault с помощью PowerShell
Следующий сценарий PowerShell приведен в качестве примера того, как с помощью командлета `Set-AzDiagnosticSetting` включить ведения журналов диагностики для Key Vault:
```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'

Set-AzDiagnosticSetting -ResourceId $kv.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```



## <a name="review-azure-key-vault-data-collection-details"></a>Просмотр сведений о сборе данных хранилища ключей Azure
Решение хранилища ключей Azure собирает журналы диагностики напрямую из хранилища ключей.
Необязательно записывать журналы в хранилище BLOB-объектов Azure. Для сбора данных агенты не требуются.

В следующей таблице приведены методы сбора данных и другие сведения о сборе данных для хранилища ключей Azure.

| Платформа | Direct Agent | Агент Systems Center Operations Manager | Azure | Нужен ли Operations Manager? | Отправка данных агента Operations Manager через группу управления | Частота сбора |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  | При получении |

## <a name="use-azure-key-vault"></a>Использование хранилища ключей Azure
После [установки решения](https://azuremarketplace.microsoft.com/en-usrketplace/marketplace/apps/Microsoft.KeyVaultAnalyticsOMS?tab=Overview)просмотрите Key Vault данные, щелкнув плитку **Аналитика Key Vault** на странице Azure Monitor **Обзор** . Откройте ее в меню **Azure Monitor**, щелкнув **Больше** в разделе **Аналитика**. 

![изображение плитки "Хранилище ключей Azure"](media/azure-key-vault/log-analytics-keyvault-tile.png)

Щелкнув плитку **аналитика Key Vault** , можно просмотреть сводные данные журналов, а затем детализировать сведения для следующих категорий:

* количество всех операций хранилища ключей за определенный период;
* количество неудавшихся операций за определенный период;
* среднее время задержки для каждой операции;
* качество обслуживания для операций с количеством операций, выполнявшихся более 1000 мс, и списком этих операций.

![изображение панели мониторинга хранилища ключей Azure](media/azure-key-vault/log-analytics-keyvault01.png)

![изображение панели мониторинга хранилища ключей Azure](media/azure-key-vault/log-analytics-keyvault02.png)

### <a name="to-view-details-for-any-operation"></a>Просмотр сведений об операциях
1. На странице **Обзор** щелкните плитку **аналитика Key Vault** .
2. На панели мониторинга **Хранилище ключей Azure** просмотрите сводные данные в одной из колонок, а затем щелкните одну из них, чтобы просмотреть подробные сведения на странице поиска журналов.

    На любой из страниц поиска журналов можно просмотреть результаты по времени, подробные результаты и историю поиска журналов. Для сужения области результатов выполните фильтрацию по аспектам.

## <a name="azure-monitor-log-records"></a>Записи журнала Azure Monitor
Решение хранилища ключей Azure анализирует записи типа **KeyVaults**, полученные из [журналов AuditEvent](../../key-vault/key-vault-logging.md) системы диагностики Azure.  Свойства этих записей приведены в таблице ниже.  

| Свойство | Description |
|:--- |:--- |
| Тип |*AzureDiagnostics* |
| SourceSystem |*Таблицы Azure* |
| CallerIpAddress |IP-адрес клиента, отправившего запрос. |
| Категория | *AuditEvent* |
| CorrelationId |Необязательный GUID, который клиент может передавать для сопоставления журналов на стороне клиента с журналами на стороне службы (хранилища ключей). |
| DurationMs |Время обслуживания запроса REST API в миллисекундах. Это время не включает в себя задержку сети, поэтому время, зарегистрированное на стороне клиента, может не соответствовать этому значению. |
| HttpStatusCode_d |Код состояния HTTP, возвращаемый запросом (например, *200*) |
| id_s |Уникальный идентификатор запроса. |
| identity_claim_appid_g | Идентификатор GUID для идентификатора приложения |
| OperationName |Имя операции, как описано в статье [Ведение журнала хранилища ключей Azure](../../key-vault/key-vault-logging.md) |
| OperationVersion |Запрошенная клиентом версия REST API (например, *2015-06-01*) |
| requestUri_s |URI запроса |
| Resource |Имя хранилища ключей. |
| Группа ресурсов |Группа ресурсов хранилища ключей. |
| resourceId |Идентификатор ресурса диспетчера ресурсов Azure. Для журналов хранилища ключей это идентификатор ресурса хранилища ключей. |
| ResourceProvider |*MICROSOFT.KEYVAULT* |
| ResourceType | *VAULTS* |
| ResultSignature |Код состояния HTTP (например, *ОК*) |
| ResultType |Результат запроса REST API (например, *Успешно*) |
| SubscriptionId |Идентификатор подписки Azure, которая содержит хранилище ключей. |

## <a name="migrating-from-the-old-key-vault-solution"></a>Миграция из устаревшего решения Key Vault
В январе 2017 г. поддерживаемый способ отправки журналов из Key Vault в Log Analytics был изменен. Эти изменения обеспечивают следующие преимущества:
+ Журналы записываются непосредственно в рабочую область Log Analytics без необходимости использовать учетную запись хранения.
+ Меньше задержка между моментом создания журналов и их доступностью в Log Analytics.
+ Меньше этапов настройки.
+ Общий формат для всех типов системы диагностики Azure.

Чтобы использовать обновленное решение, выполните следующие действия:

1. [Настройка диагностики для отправки непосредственно в Log Analytics рабочую область из Key Vault](#enable-key-vault-diagnostics-in-the-portal)  
2. Включите решение Azure Key Vault с помощью процесса, описанного в статье [добавление Azure Monitor решений из коллекция решений](../../azure-monitor/insights/solutions.md)
3. Обновите все сохраненные запросы, панели мониторинга и оповещения, чтобы использовать новый тип данных.
   + Тип меняется с KeyVaults на AzureDiagnostics. Параметр ResourceType можно использовать для фильтрации по журналам Key Vault.
   + Вместо `KeyVaults` используйте `AzureDiagnostics | where ResourceType'=="VAULTS"`.
   + Поля (в именах полей учитывается регистр).
   + Для любого поля, имя которого содержит суффикс \_s, \_d или \_g, переведите первый знак в нижний регистр.
   + Для любого поля, имя которого содержит суффикс \_o, данные разбиваются на отдельные поля на основе имен вложенных полей. Например, имя участника-пользователя вызывающего объекта сохраняется в поле `identity_claim_http_schemas_xmlsoap_org_ws_2005_05_identity_claims_upn_s`.
   + Поле CallerIpAddress меняется на CallerIPAddress.
   + Поле RemoteIPCountry больше не используется.
4. Удалите устаревшее решение *Key Vault Analytics (не рекомендуется)* . Если используется PowerShell, то выполните следующую команду: `Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "KeyVault" -Enabled $false`

Данные, собранные до этого изменения, не отображаются в новом решении. Эти данные по-прежнему можно запрашивать с помощью старых имен типов и полей.

## <a name="troubleshooting"></a>Устранение неполадок
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>Следующие шаги
* Используйте [запросы журналов в Azure Monitor](../../azure-monitor/log-query/log-query-overview.md) для просмотра подробных Azure Key Vault данных.
