---
title: Ведение журнала Azure Key Vault | Документация Майкрософт
description: Это руководство поможет вам приступить к работе с журналами хранилища ключей Azure.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 997651887c3c378e4791553d5ff05f585ad169ea
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71000668"
---
# <a name="azure-key-vault-logging"></a>Ведение журнала Azure Key Vault

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Создав одно или несколько хранилищ ключей вы, вероятно, захотите отслеживать, кто, как и когда осуществлял доступ к этим хранилищам. Это можно сделать с помощью функции ведения журнала Azure Key Vault, которая сохраняет информацию в указанной учетной записи хранения Azure. Для указанной вами учетной записи автоматически создается контейнер с именем **insights-logs-auditevent**. Эту же учетную запись можно использовать для сбора журналов нескольких хранилищ ключей.

Регистрируемые в журналах сведения становятся доступны не позднее чем через 10 минут после выполнения операции с хранилищем ключей. В большинстве случаев это будет еще быстрее.  Способ управления журналами в своей учетной записи хранения вы выбираете сами.

* Используйте стандартные методы контроля доступа, предоставляемые Azure, для защиты журналов путем ограничения доступа к ним.
* Удаляйте журналы, которые больше не нужно хранить в учетной записи хранения.

Это руководство поможет вам приступить к работе с журналами хранилища ключей Azure. Вы создадите учетную запись хранения, включите ведение журнала и проанализируете собранные данные журнала.  

> [!NOTE]
> В этом руководстве нет инструкций по созданию хранилищ ключей, ключей и секретов. Дополнительные сведения см. в статье [Что такое хранилище ключей Azure?](key-vault-overview.md) Инструкции по кроссплатформенному интерфейсу Azure CLI см. в [этом руководстве](key-vault-manage-with-cli2.md).
>
> В этой статье приведены инструкции по обновлению журнала ведения диагностики с помощью Azure PowerShell. Ведение таких журналов можно также настроить с помощью Azure Monitor в разделе **Журналы диагностики** на портале Azure. 
>

Общие сведения о Key Vault см. в статье [Что такое хранилище ключей Azure?](key-vault-overview.md) Сведения о регионах доступности Key Vault можно узнать на [странице цен](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим учебником требуется:

* Существующее хранилище ключей, которое вы используете.  
* Azure PowerShell, начиная с версии 1.0.0. Чтобы установить решение Azure PowerShell и связать его с подпиской Azure, см. статью [Установка и настройка Azure PowerShell](/powershell/azure/overview). Если средство Azure PowerShell у вас установлено, но вы не знаете его версию, введите `$PSVersionTable.PSVersion` в консоли Azure PowerShell.  
* Достаточный объем хранилища в Azure для журналов хранилищ ключей.

## <a id="connect"></a>Подключение к подписке хранилища ключей

Для настройки ведения журнала ключей первым делом подключите Azure PowerShell к нужному хранилищу ключей.

Запустите сеанс Azure PowerShell и выполните вход в учетную запись Azure с помощью следующей команды:  

```powershell
Connect-AzAccount
```

Во всплывающем окне браузера введите имя пользователя и пароль учетной записи Azure. Azure PowerShell получит все подписки, связанные с этой учетной записью. По умолчанию PowerShell использует первую из них.

Можете указать ту подписку, для которой вы создали хранилище ключей. Чтобы увидеть подписки для своей учетной записи, введите следующую команду:

```powershell
Get-AzSubscription
```

Затем укажите подписку, связанную с хранилищем ключей, данные которого будут регистрироваться. Для этого выполните следующую команду:

```powershell
Set-AzContext -SubscriptionId <subscription ID>
```

Подключение PowerShell к правильной подписке очень важно, особенно если с учетной записью связано несколько подписок. Дополнительные сведения о настройке Azure PowerShell см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).

## <a id="storage"></a>Создание учетной записи хранения для журналов

Хотя вы можете использовать и существующую учетную запись хранения для журналов, мы создадим учетную запись, которая будет использоваться для сбора данных хранилища ключей. Эти сведения нам нужно будет указать позже. Сейчас же для удобства работы мы сохраним их в переменной с именем **sa**.

Чтобы упростить управление, мы также будем использовать ту же группу ресурсов, которая содержит хранилище ключей. В [руководстве по началу работы](key-vault-get-started.md) эта группа ресурсов называется **ContosoResourceGroup**. Кроме того, мы будем продолжать использовать регион "Восточная Азия". Замените эти значения собственными, если нужно.

```powershell
 $sa = New-AzStorageAccount -ResourceGroupName ContosoResourceGroup -Name contosokeyvaultlogs -Type Standard_LRS -Location 'East Asia'
```

> [!NOTE]
> Если вы решили использовать существующую учетную запись хранения, она должна использовать ту же подписку, что и хранилище ключей. Также она должна использовать модель развертывания с помощью Azure Resource Manager, а не классическую модель развертывания.
>
>

## <a id="identify"></a>Определение хранилища ключей для журналов

В [руководстве по началу работы](key-vault-get-started.md) использовалось имя **ContosoKeyVault** для хранилища ключей. Мы сохраним это же имя и сохраним его в переменной с именем **kv**:

```powershell
$kv = Get-AzKeyVault -VaultName 'ContosoKeyVault'
```

## <a id="enable"></a>Включение ведения журналов

Мы включим ведение журналов хранилища ключей с помощью командлета **Set-AzDiagnosticSetting** и переменных, которые мы создали для новой учетной записи хранения и хранилища ключей. Мы также установим для флага **-Enabled** значение **$true** и зададим категорию **AuditEvent** (единственная категория для ведения журнала Key Vault).

```powershell
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

Эти выходные данные подтверждают, что для хранилища ключей теперь включено ведение журнала, и данные сохраняются в указанной учетной записи хранения.

При желании вы можете задать для журналов политику хранения, например для автоматического удаления старых журналов. Например, для политики хранения можно присвоить флагу **-RetentionEnabled** значение **$true**, а параметру **-RetentionInDays** — значение **90**, чтобы автоматически удалять журналы, которые хранятся более 90 дней.

```powershell
Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $true -Category AuditEvent -RetentionEnabled $true -RetentionInDays 90
```

Регистрируются следующие данные:

* все прошедшие проверку подлинности запросы REST API, включая запросы, ставшие неудачными из-за определенных разрешений на доступ, системных ошибок или неправильных запросов;
* операции с хранилищем ключей, включая создание, удаление и настройку политик доступа к нему, а также обновление таких его атрибутов, как теги;
* операции с ключами и секретами в хранилище ключей, в том числе:
  * создание, изменение или удаление ключей или секретов;
  * подписывание, проверка, шифрование, расшифровка, упаковка и распаковка ключей, получение секретов и вывод списка ключей и секретов (и их версий);
* непроверенные запросы, которые приводят к появлению ответа 401 (например, запросы без токена носителя, с недействительным токеном, а также запросы неправильного формата или просроченные запросы).  

## <a id="access"></a>Доступ к журналам

Журналы Key Vault сохраняются в контейнере **insights-logs-auditevent** в указанной вами учетной записи хранения. Чтобы просмотреть эти журналы, скачайте большие двоичные объекты.

Сначала создайте переменную для имени контейнера. Эта переменная будет использоваться далее в этом пошаговом руководстве.

```powershell
$container = 'insights-logs-auditevent'
```

Чтобы получить список всех больших двоичных объектов (BLOB-объектов) в этом контейнере, введите следующее:

```powershell
Get-AzStorageBlob -Container $container -Context $sa.Context
```

Результат будет выглядеть примерно так:

```
Container Uri: https://contosokeyvaultlogs.blob.core.windows.net/insights-logs-auditevent

Name

---
resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=05/h=01/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=02/m=00/PT1H.json

resourceId=/SUBSCRIPTIONS/361DA5D4-A47A-4C79-AFDD-XXXXXXXXXXXX/RESOURCEGROUPS/CONTOSORESOURCEGROUP/PROVIDERS/MICROSOFT.KEYVAULT/VAULTS/CONTOSOKEYVAULT/y=2016/m=01/d=04/h=18/m=00/PT1H.json
```

Как видно из этих выходных данных, имена больших двоичных объектов соответствуют соглашению об именовании: `resourceId=<ARM resource ID>/y=<year>/m=<month>/d=<day of month>/h=<hour>/m=<minute>/filename.json`

Для значений даты и времени используется время в формате UTC.

Поскольку одну учетную запись можно использовать для сбора журналов нескольких ресурсов, для просмотра и скачивания нужных BLOB-объектов желательно указывать полный идентификатор ресурса в имени BLOB-объекта. Но сначала мы рассмотрим загрузку всех BLOB-объектов.

Создайте папку для загрузки BLOB-объектов. Например:

```powershell 
New-Item -Path 'C:\Users\username\ContosoKeyVaultLogs' -ItemType Directory -Force
```

Затем выведите список всех BLOB-объектов.  

```powershell
$blobs = Get-AzStorageBlob -Container $container -Context $sa.Context
```

Передайте этот список в команду **Get-AzStorageBlobContent**, чтобы скачать большие двоичные объекты в папку назначения.

```powershell
$blobs | Get-AzStorageBlobContent -Destination C:\Users\username\ContosoKeyVaultLogs'
```

При выполнении второй команды разделитель **/** в именах больших двоичных объектов используется для создания полной структуры папки в конечной папке. Эта структура будет использоваться для скачивания и хранения больших двоичных объектов в виде файлов.

Для выборочной загрузки BLOB-объектов используйте подстановочные знаки. Например:

* Если у вас есть несколько хранилищ ключей, но вы хотите загрузить журналы только для одного хранилища с именем CONTOSOKEYVAULT3.

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/VAULTS/CONTOSOKEYVAULT3
  ```

* Если у вас есть несколько групп ресурсов, но вы хотите загрузить журналы только для одной из них, используйте `-Blob '*/RESOURCEGROUPS/<resource group name>/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/RESOURCEGROUPS/CONTOSORESOURCEGROUP3/*'
  ```

* Если вы хотите скачать все журналы за январь 2019 г., используйте `-Blob '*/year=2019/m=01/*'`:

  ```powershell
  Get-AzStorageBlob -Container $container -Context $sa.Context -Blob '*/year=2016/m=01/*'
  ```

Теперь можно переходить к анализу содержимого журналов. Но прежде чем мы перейдем к этому, вам нужно изучить еще две команды:

* Запрос состояния параметров диагностики для ресурса хранилища ключей: `Get-AzDiagnosticSetting -ResourceId $kv.ResourceId`
* Отключение ведения журнала для ресурса хранилища ключей: `Set-AzDiagnosticSetting -ResourceId $kv.ResourceId -StorageAccountId $sa.Id -Enabled $false -Category AuditEvent`

## <a id="interpret"></a>Интерпретация журналов хранилища ключей

Отдельные BLOB-объекты хранятся как текст в формате JSON. Давайте рассмотрим пример записи журнала. Выполните следующую команду:

```powershell
Get-AzKeyVault -VaultName 'contosokeyvault'`
```

Это действие возвращает запись журнала такого вида:

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

| Имя поля | ОПИСАНИЕ |
| --- | --- |
| **time** |Дата и время (в формате UTC). |
| **resourceId** |Идентификатор ресурса Azure Resource Manager. Для журналов хранилища ключей это всегда идентификатор ресурса хранилища ключей. |
| **operationName** |Имя операции, как описано в следующей таблице. |
| **operationVersion** |Запрошенная клиентом версия REST API. |
| **category** |Тип результата. Для журналов Key Vault единственным доступным значением является **AuditEvent**. |
| **resultType** |Результат запроса к REST API. |
| **resultSignature** |Состояние HTTP. |
| **resultDescription** |Дополнительное описание результата, если доступно. |
| **durationMs** |Время обслуживания запроса REST API в миллисекундах. Сюда не входит задержка сети, поэтому время, зарегистрированное на стороне клиента, может не соответствовать этому значению. |
| **callerIpAddress** |IP-адрес клиента, выполнившего запрос. |
| **correlationId** |Необязательный GUID, который клиент может передавать для сопоставления журналов на стороне клиента с журналами на стороне службы (хранилища ключей). |
| **identity** |Удостоверение из маркера, предоставляемое в запросе к REST API. Обычно это "пользователь", "субъект-служба" или комбинация "пользователь + идентификатор приложения", как например при запросе из командлета Azure PowerShell. |
| **properties** |Эта информация зависит от типа операции (**operationName**). В большинстве случаев это поле содержит сведения о клиенте (передаваемая клиентом строка useragent), точный URI запроса REST API и код состояния HTTP. Кроме того, когда результат запроса содержит объект (например, **KeyCreate** или **VaultGet**), это поле содержит еще и URI ключа (в параметре id), URI хранилища или URI секрета. |

Значения поля **operationName** отображаются в формате *ObjectVerb*. Например:

* Все операции с хранилищем ключей отображаются в формате `Vault<action>`, например `VaultGet` или `VaultCreate`.
* Все операции с ключами отображаются в формате `Key<action>`, например `KeySign` или `KeyList`.
* Все операции с секретами отображаются в формате `Secret<action>`, например `SecretGet` или `SecretListVersions`.

В следующей таблице перечислены значения **operationName** и соответствующие команды REST API.

| operationName | Команда REST API |
| --- | --- |
| **Аутентификация** |Аутентификация через конечную точку Azure Active Directory |
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

## <a id="loganalytics"></a>Использование журналов Azure Monitor

Решение Key Vault в журналах Azure Monitor позволяет просматривать журналы **AuditEvent** для Key Vault. В журналах Azure Monitor запросы по журналам используются для анализа данных и получения необходимых сведений. 

Дополнительные сведения, включая инструкции по настройке, см. в статье [Решение Azure Key Vault в журналах Azure Monitor](../azure-monitor/insights/azure-key-vault.md). В этой статье также содержатся инструкции на случай переноса из старого решения Key Vault, которое предлагалось в предварительной версии журналов Azure Monitor, где сначала требовалось направить журналы в учетную запись хранения Azure и настроить чтение из этой учетной записи в журналах Azure Monitor.

## <a id="next"></a>Дальнейшие действия

Руководство по использованию Azure Key Vault в веб-приложении .NET см. в [этой статье](tutorial-net-create-vault-azure-web-app.md).

Справочные материалы по программированию см. в статье [Руководство разработчика хранилища ключей Azure](key-vault-developers-guide.md).

Полный список командлетов Azure PowerShell 1.0 для хранилища ключей Azure см. в статье [Azure Key Vault cmdlets](/powershell/module/az.keyvault/?view=azps-1.2.0#key_vault) (Командлеты хранилища ключей Azure).

Руководство по смене ключей и аудиту журналов с помощью Azure Key Vault см. в статье [о настройке полной смены ключей и аудита в Key Vault](key-vault-key-rotation-log-monitoring.md).
