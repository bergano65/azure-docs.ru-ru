---
title: Ведение журнала Azure Key Vault | Документация Майкрософт
description: Это руководство поможет вам приступить к работе с журналами хранилища ключей Azure.
services: key-vault
documentationcenter: ''
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.assetid: 43f96a2b-3af8-4adc-9344-bc6041fface8
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: barclayn
ms.openlocfilehash: afec42551f124890dd2cc7b03cce48c359fc88c4
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194101"
---
# <a name="azure-key-vault-logging"></a>Ведение журнала Azure Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Создав одну или несколько хранилищ ключей, скорее всего нужно будет отслеживать как и когда хранилище ключей, к которому осуществляется доступ и кем. Это можно сделать, включив ведение журнала хранилища ключей Azure, которая сохраняет информацию в предоставленную учетную запись хранения Azure. Новый контейнер с именем **insights-logs-auditevent** автоматически создается для указанной учетной записи. Можно использовать эту учетную запись хранения для сбора журналов нескольких хранилищ ключей.

Доступны данные ведения журнала 10 минут (максимум) после выполнения операции хранилища ключей. В большинстве случаев это будет еще быстрее.  Способ управления журналами в своей учетной записи хранения вы выбираете сами.

* Используйте стандартные методы контроля доступа, предоставляемые Azure, для защиты журналов путем ограничения доступа к ним.
* Удаляйте журналы, которые больше не нужно хранить в учетной записи хранения.

Это руководство поможет вам приступить к работе с журналами хранилища ключей Azure. Будет создать учетную запись хранилища, включите ведение журнала и интерпретировать сведения журнала.  

> [!NOTE]
> В этом руководстве нет инструкций по созданию хранилищ ключей, ключей и секретов. Дополнительные сведения см. в статье [Что такое хранилище ключей Azure?](key-vault-overview.md) Инструкции кросс платформенного интерфейса командной строки Azure, см. в разделе [аналогичном руководстве](key-vault-manage-with-cli2.md).
>
> В этой статье инструкции Azure PowerShell для обновления данных диагностики. Можно также обновить ведение журналов диагностики с помощью Azure Monitor в **журналы диагностики** раздела на портале Azure. 
>

Общие сведения о хранилище ключей, см. в разделе [что такое хранилище ключей Azure?](key-vault-whatis.md). Сведения о котором хранилище ключей доступно, см. в разделе [странице с ценами](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Технические условия

Для работы с этим учебником требуется:

* Существующее хранилище ключей, которое вы используете.  
* Azure PowerShell, минимальная версия 1.0.0. Чтобы установить решение Azure PowerShell и связать его с подпиской Azure, см. статью [Установка и настройка Azure PowerShell](/powershell/azure/overview). Если вы уже установили Azure PowerShell и не знаете его версию, из консоли Azure PowerShell, введите `$PSVersionTable.PSVersion`.  
* Достаточный объем хранилища в Azure для журналов хранилищ ключей.

## <a id="connect"></a>Подключитесь к своей подписке хранилища ключей

Первым шагом в настройке ключей ведения журналов — точка Azure PowerShell для хранилища ключей, которое вы хотите войти.

Запустите сеанс Azure PowerShell и выполните вход в учетную запись Azure с помощью следующей команды:  

```PowerShell
Connect-AzAccount
```

Во всплывающем окне браузера введите имя пользователя и пароль учетной записи Azure. Azure PowerShell получает все подписки, связанные с этой учетной записи. По умолчанию PowerShell использует первый из них.

Возможно указать подписку, которая использовалась для создания хранилища ключей. Введите следующую команду, чтобы увидеть подписки для учетной записи:

```PowerShell
Get-AzSubscription
```

Затем укажите подписку, связанную с хранилищем ключей, с которого будут регистрироваться, введите следующую команду:

```PowerShell
Set-AzContext -SubscriptionId <subscription ID>
```

Выбрать подходящую подписку команды PowerShell является важным шагом, особенно в том случае, если у вас несколько подписок, связанных с учетной записью. Дополнительные сведения о настройке Azure PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).

## <a id="storage"></a>Создайте учетную запись хранения для журналов

Несмотря на то, что можно использовать существующую учетную запись хранения для журналов, мы создадим учетную запись хранения, которая будет использоваться для журналов хранилища ключей. Для удобства работы нам нужно будет указать позже, мы будем хранить данные в переменной с именем **sa**.

Чтобы упростить управление мы также используем той же группе ресурсов, что содержит хранилище ключей. Из [руководства по началу](key-vault-get-started.md), эта группа ресурсов называется **ContosoResourceGroup**, и мы будем продолжать использовать расположение "Восточная Азия". Замените эти значения собственными, где это применимо:

```PowerShell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Если вы решили использовать существующую учетную запись хранения, он должен использовать ту же подписку как хранилище ключей. И он должен использовать модель развертывания Azure Resource Manager, а не классической модели развертывания.
>
>

## <a id="identify"></a>Определение хранилища ключей для журналов

В [руководства по началу](key-vault-get-started.md), то имя хранилища ключей, **ContosoKeyVault**. Мы продолжим использовать это имя и сохранить данные в переменной с именем **kv**:

```PowerShell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>Включение ведения журналов

Чтобы включить ведение журналов хранилища ключей, мы будем использовать **AzDiagnosticSetting набора** командлета, а также переменные, которые мы создали для новой учетной записи хранения и хранилище ключей. Мы также установим **-включена** флаг **$true** и зададим категорию **AuditEvent** (единственная категория для ведения журнала хранилища ключей):

```PowerShell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent
```

Выходные данные выглядят следующим образом:

    StorageAccountId   : /subscriptions/<subscription-GUID>/resourceGroups/ContosoResourceGroup/providers/Microsoft.Storage/storageAccounts/ContosoKeyVaultLogs
    ServiceBusRuleId   :
    StorageAccountName :
        Logs
        Enabled           : True
        Category          : AuditEvent
        RetentionPolicy
        Enabled : False
        Days    : 0

Этот результат подтверждает, что ведение журнала включено для хранилища ключей, и он сохранит данные вашей учетной записи хранения.

При необходимости можно задать политику хранения для журналов, таким образом, что старые журналы будут автоматически удалены. Например, задавать политику хранения, задав **- RetentionEnabled** флаг **$true**и задайте **- RetentionInDays** параметр **90**таким образом, чтобы журналы старше 90 дней будут автоматически удалены.

```PowerShell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Регистрируются следующие данные:

* Все прошедшие проверку подлинности запросы REST API, включая неудачные запросы, в результате разрешения на доступ, системных ошибок или неправильных запросов.
* Операции с ключом хранилища сам, включая создание, удаление и Настройка политик доступа хранилища ключей и обновления атрибутов хранилища ключей, например теги.
* Операции с ключами и секретами в хранилище ключей, включая:
  * Создание, изменение или удаление этих ключей и секретов.
  * Подписывание, проверка, шифрования, расшифровки, упаковки и распаковки ключей, получение секретов и список ключей и секретов (и их версий).
* непроверенные запросы, которые приводят к появлению ответа 401 Примерами являются запросы без токена носителя, которые неправильного формата или просроченные или имеют недопустимый маркер.  

## <a id="access"></a>Доступ к журналам

Журналы хранилища ключей хранятся в **insights-logs-auditevent** контейнер в учетной записи, которое вы указали. Чтобы просмотреть журналы, необходимо скачать большие двоичные объекты.

Сначала создайте переменную для имени контейнера. Эта переменная, в остальной части пошагового руководства будет использоваться.

```PowerShell
$container = 'insights-logs-auditevent'
```

Чтобы получить список всех больших двоичных объектов в этом контейнере, введите следующую команду:

```PowerShell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Результат будет выглядеть примерно так:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

- - -
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Как видно из этих выходных данных, имена больших двоичных объектов соответствуют соглашению об именовании: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Для значений даты и времени используется время в формате UTC.

Одну и ту же учетную запись хранения может использоваться для сбора журналов для нескольких ресурсов, для доступа к или загрузить только большие двоичные объекты, которые необходимы полезно полный идентификатор ресурса в имени большого двоичного объекта. Но сначала мы рассмотрим загрузку всех BLOB-объектов.

Создайте папку для скачивания больших двоичных объектов. Например: 

```PowerShell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Затем выведите список всех BLOB-объектов.  

```PowerShell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Передайте этот список с помощью **Get-AzStorageBlobContent** для загрузки больших двоичных объектов в конечную папку:

```PowerShell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

При выполнении второй команды разделитель **/** в именах больших двоичных объектов используется для создания полной структуры папки в конечной папке. Эта структура используется для загрузки и хранения больших двоичных объектов в виде файлов.

Для выборочной загрузки BLOB-объектов используйте подстановочные знаки. Например: 

* Если у вас есть несколько хранилищ ключей, но вы хотите загрузить журналы только для одного хранилища с именем CONTOSOKEYVAULT3.

  ```PowerShell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Если у вас есть несколько групп ресурсов, но вы хотите загрузить журналы только для одной из них, используйте `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```PowerShell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Если вы хотите загрузить все журналы за месяц января 2019, используйте `-Blob '*/year=2019/m=01/*'`:

  ```PowerShell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Теперь можно переходить к анализу содержимого журналов. Но прежде чем мы перейдем к этому, вы должны знать две дополнительные команды:

* Запрос состояния параметров диагностики для ресурса хранилища ключей: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Отключение ведения журнала для ресурса хранилища ключей: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>Интерпретация журналов хранилища ключей

Отдельные BLOB-объекты хранятся как текст в формате JSON. Давайте рассмотрим пример записи журнала. Выполните следующую команду:

```PowerShell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Возвращается запись следующего вида:

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

В следующей таблице перечислены имена полей и описания:

| Имя поля | ОПИСАНИЕ |
| --- | --- |
| **time** |Дата и время в формате UTC. |
| **resourceId** |Идентификатор ресурса Azure Resource Manager. Для журналов хранилища ключей это всегда идентификатор ресурса хранилища ключей. |
| **operationName** |Имя операции, как описано в следующей таблице. |
| **operationVersion** |Запрошенная клиентом версия REST API. |
| **category** |Тип результата. Для журналов хранилища ключей **AuditEvent** является единственным доступным значением. |
| **Тип resultType** |Результат запроса REST API. |
| **resultSignature** |Состояние HTTP. |
| **ResultDescription** |Дополнительное описание результата, если доступно. |
| **durationMs** |Время обслуживания запроса REST API в миллисекундах. Сюда не входит задержка сети, поэтому время, зарегистрированное на стороне клиента, может не соответствовать этому значению. |
| **calleripaddress.** |IP-адрес клиента, который выполнил запрос. |
| **CorrelationId** |Необязательный GUID, который клиент может передавать для сопоставления журналов на стороне клиента с журналами на стороне службы (хранилища ключей). |
| **identity** |Удостоверение из маркера, предоставляемое в запросе REST API. Обычно это «user», «субъект-службу» или комбинация «пользователь + идентификатор приложения,» как в случае запроса, полученный в результате командлета Azure PowerShell. |
| **properties** |Сведения, которые различаются в зависимости от операции (**Имя_операции**). В большинстве случаев это поле содержит сведения о клиенте (строке пользовательского агента, переданного клиентом), точный URI запроса REST API и код состояния HTTP. Кроме того, когда объект возвращается в результате запроса (например, **KeyCreate** или **VaultGet**), он также содержит ключ URI (как «id»), хранилище URI или URI секрета. |

**Имя_операции** значения полей находятся в *ObjectVerb* формат. Например: 

* Все операции с хранилищем ключей `Vault<action>` форматирования, такие как `VaultGet` и `VaultCreate`.
* Все операции с ключами `Key<action>` форматирования, такие как `KeySign` и `KeyList`.
* Все операции с секретами `Secret<action>` форматирования, такие как `SecretGet` и `SecretListVersions`.

В следующей таблице перечислены **Имя_операции** значения и соответствующие команды REST API:

| operationName | Команды REST API |
| --- | --- |
| **Проверка подлинности** |Проверку подлинности с помощью конечной точки Azure Active Directory |
| **VaultGet** |[Получение сведений о хранилище ключей](https://msdn.microsoft.com/library/azure/mt620026.aspx) |
| **VaultPut** |[Создание или обновление хранилища ключей](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultDelete** |[Удаление хранилища ключей](https://msdn.microsoft.com/library/azure/mt620022.aspx) |
| **VaultPatch** |[Update a key vault (Создание или обновление хранилища ключей)](https://msdn.microsoft.com/library/azure/mt620025.aspx) |
| **VaultList** |[Список всех хранилищ ключей в группе ресурсов](https://msdn.microsoft.com/library/azure/mt620027.aspx) |
| **KeyCreate** |[Создание ключа](https://msdn.microsoft.com/library/azure/dn903634.aspx) |
| **KeyGet** |[Получение сведений о ключе](https://msdn.microsoft.com/library/azure/dn878080.aspx) |
| **KeyImport** |[Импорт ключа в хранилище](https://msdn.microsoft.com/library/azure/dn903626.aspx) |
| **KeyBackup** |[Резервное копирование ключа](https://msdn.microsoft.com/library/azure/dn878058.aspx) |
| **KeyDelete** |[Удаление ключа](https://msdn.microsoft.com/library/azure/dn903611.aspx) |
| **KeyRestore** |[Восстановление ключа](https://msdn.microsoft.com/library/azure/dn878106.aspx) |
| **KeySign** |[Вход с помощью ключа](https://msdn.microsoft.com/library/azure/dn878096.aspx) |
| **KeyVerify** |[Проверка с помощью ключа](https://msdn.microsoft.com/library/azure/dn878082.aspx) |
| **KeyWrap** |[Перенос ключа](https://msdn.microsoft.com/library/azure/dn878066.aspx) |
| **KeyUnwrap** |[Развертывание ключа](https://msdn.microsoft.com/library/azure/dn878079.aspx) |
| **KeyEncrypt** |[Шифрование с помощью ключа](https://msdn.microsoft.com/library/azure/dn878060.aspx) |
| **KeyDecrypt** |[Расшифровка с помощью ключа](https://msdn.microsoft.com/library/azure/dn878097.aspx) |
| **KeyUpdate** |[Обновление ключа](https://msdn.microsoft.com/library/azure/dn903616.aspx) |
| **KeyList** |[Перечисление ключей в хранилище](https://msdn.microsoft.com/library/azure/dn903629.aspx) |
| **KeyListVersions** |[Перечисление версий ключа](https://msdn.microsoft.com/library/azure/dn986822.aspx) |
| **SecretSet** |[Создание секрета](https://msdn.microsoft.com/library/azure/dn903618.aspx) |
| **SecretGet** |[Получение секрета](https://msdn.microsoft.com/library/azure/dn903633.aspx) |
| **SecretUpdate** |[Обновление секрета](https://msdn.microsoft.com/library/azure/dn986818.aspx) |
| **SecretDelete** |[Удаление секрета](https://msdn.microsoft.com/library/azure/dn903613.aspx) |
| **SecretList** |[Список секретов в хранилище](https://msdn.microsoft.com/library/azure/dn903614.aspx) |
| **SecretListVersions** |[Список версий секрета](https://msdn.microsoft.com/library/azure/dn986824.aspx) |

## <a id="loganalytics"></a>Использовать журналы Azure Monitor

Решение хранилища ключей можно использовать в журналах Azure Monitor для просмотра Key Vault **AuditEvent** журналы. В журналах Azure Monitor используйте журнал запросов для анализа данных и получить необходимую информацию. 

Дополнительные сведения, включая как выполнить такую настройку, см. в разделе [решение хранилища ключей Azure в журналах Azure Monitor](../azure-monitor/insights/azure-key-vault.md). В этой статье также содержит инструкции, если нужно выполнить миграцию из старого решения Key Vault, стал доступен во время журналы Azure Monitor, предварительного просмотра, где сначала требовалось направить журналы в учетную запись хранилища Azure и журналов мониторинга Azure для считывания из него.

## <a id="next"></a>Дальнейшие действия

В этом руководстве используется Azure Key Vault в веб-приложения .NET, см. в разделе [использование хранилища ключей Azure из веб-приложения](tutorial-net-create-vault-azure-web-app.md).

Справочные материалы по программированию см. в статье [Руководство разработчика хранилища ключей Azure](key-vault-developers-guide.md).

Список командлетов Azure PowerShell 1.0 для хранилища ключей Azure, см. в разделе [командлетов хранилища ключей Azure](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault).

Руководство по смене ключей и аудиту журналов с помощью Azure Key Vault, см. в разделе [Настройка хранилища ключей с начала до конца смены ключей и аудита](key-vault-key-rotation-log-monitoring.md).
