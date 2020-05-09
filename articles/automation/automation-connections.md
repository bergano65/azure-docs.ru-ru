---
title: Управление подключениями в службе автоматизации Azure
description: Ресурсы-контейнеры подключений в службе автоматизации Azure содержат данные, необходимые для подключения к внешней службе или приложению из модуля Runbook либо конфигурации DSC. В статье подробно рассматриваются подключения и работа с ними как в текстовых, так и в графических модулях.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.custom: has-adal-ref
ms.openlocfilehash: b00dd226306ed639757666cc4f826b0d7a0e5711
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82651830"
---
# <a name="manage-connections-in-azure-automation"></a>Управление подключениями в службе автоматизации Azure

Ресурс для подключения службы автоматизации Azure содержит приведенные ниже сведения. Эти сведения необходимы для подключения к внешней службе или приложению из модуля Runbook или конфигурации DSC. 

* Сведения, необходимые для проверки подлинности, такие как имя пользователя и пароль
* Сведения о соединении, например URL-адрес или порт

Ресурс подключения сохраняет все свойства для подключения к определенному приложению, что делает ненужным создание нескольких переменных. Можно изменить значения подключения в одном месте, а также передать имя соединения с модулем Runbook или конфигурацией DSC в одном параметре. Модуль Runbook или Конфигурация обращается к свойствам соединения с помощью внутреннего `Get-AutomationConnection` командлета.

При создании подключения необходимо указать тип подключения. Тип подключения — это шаблон, определяющий набор его свойств. Вы можете добавить тип подключения в службу автоматизации Azure с помощью модуля интеграции с файлом метаданных. Кроме того, можно создать тип подключения с помощью [API службы автоматизации Azure](/previous-versions/azure/reference/mt163818(v=azure.100)) , если модуль интеграции включает тип подключения и импортирован в учетную запись службы автоматизации. 

>[!NOTE]
>Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в службе автоматизации Azure с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Служба автоматизации Azure сохраняет ключ в управляемом системой Key Vault. Перед сохранением защищенного ресурса Автоматизация загружает ключ из Key Vault и затем использует его для шифрования ресурса. 

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Чтобы обновить модули в учетной записи службы автоматизации, см. руководство по [обновлению модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="connection-types"></a>Типы подключений

Служба автоматизации Azure делает доступными следующие встроенные типы подключений:

* `Azure`— Представляет подключение, используемое для управления классическими ресурсами.
* `AzureServicePrincipal`— Представляет подключение, используемое учетной записью запуска от имени Azure.
* `AzureClassicCertificate`— Представляет подключение, используемое классической учетной записью запуска от имени Azure.

В большинстве случаев не нужно создавать ресурс подключения, так как он создается при создании [учетной записи запуска от имени](manage-runas-account.md).

## <a name="powershell-cmdlets-to-access-connections"></a>Командлеты PowerShell для доступа к подключениям

Командлеты, приведенные в следующей таблице, создают подключения службы автоматизации и управляют ими с помощью PowerShell. Они поставляются как часть [модулей AZ](shared-resources/modules.md#az-modules).

|Командлет|Описание|
|---|---|
|[Get-Азаутоматионконнектион](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|Извлекает сведения о соединении.|
|[New-Азаутоматионконнектион](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|Создает новое подключение.|
|[Remove-Азаутоматионконнектион](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|Удаляет существующее соединение.|
|[Set-Азаутоматионконнектионфиелдвалуе](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|Задает значение определенного поля для существующего подключения.|

## <a name="internal-cmdlets-to-access-connections"></a>Внутренние командлеты для доступа к подключениям

Внутренний командлет, приведенный в следующей таблице, используется для доступа к подключениям в модулях Runbook и конфигурациях DSC. Этот командлет поставляется с глобальным модулем `Orchestrator.AssetManagement.Cmdlets`. Дополнительные сведения см. в разделе [внутренние командлеты](shared-resources/modules.md#internal-cmdlets).

|Внутренний командлет|Описание|
|---|---|
|`Get-AutomationConnection` | Извлекает значения различных полей в соединении и возвращает их в виде [хэш-таблицы](https://go.microsoft.com/fwlink/?LinkID=324844). Затем эту хэш-таблицу можно использовать с соответствующими командами в модуле Runbook или конфигурации DSC.|

>[!NOTE]
>Избегайте использования переменных с `Name` параметром. `Get-AutomationConnection` Использование переменных в этом случае может усложнить обнаружение зависимостей между модулями Runbook или конфигурациями DSC и ресурсами подключения во время разработки.

## <a name="python-2-functions-to-access-connections"></a>Функции Python 2 для доступа к подключениям

Функция, приведенная в следующей таблице, используется для доступа к соединениям в модуле Runbook Python 2.

| Функция | Описание: |
|:---|:---|
| `automationassets.get_automation_connection` | Извлекает подключение. Возвращает словарь со свойствами подключения. |

> [!NOTE]
> Чтобы получить доступ к `automationassets` функциям активов, необходимо импортировать модуль в верхней части модуля Runbook Python.

## <a name="create-a-new-connection"></a>Создание подключения

### <a name="create-a-new-connection-with-the-azure-portal"></a>Создание нового соединения с портал Azure

Чтобы создать новое соединение в портал Azure, выполните следующие действия.

1. В учетной записи службы автоматизации щелкните **подключения** в разделе **Общие ресурсы**.
2. Щелкните **+ Добавить подключение** на странице подключения.
4. В поле **тип** в области новое подключение выберите тип создаваемого соединения. Доступны следующие варианты `Azure`: `AzureServicePrincipal`, и `AzureClassicCertificate`. 
5. В форме представлены свойства выбранного типа подключения. Заполните форму и нажмите кнопку **Создать** для сохранения нового подключения.

### <a name="create-a-new-connection-with-windows-powershell"></a>Создание нового подключения с помощью Windows PowerShell

Создайте новое подключение с помощью `New-AzAutomationConnection` командлета Windows PowerShell. Этот командлет имеет `ConnectionFieldValues` параметр, который ожидает, что хэш-таблица определяет значения для каждого свойства, определенного типом соединения.

Приведенные ниже примеры команд можно использовать в качестве альтернативы созданию учетной записи запуска от имени на портале для создания нового ресурса подключения.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

При создании учетной записи службы автоматизации по умолчанию включается несколько глобальных модулей, а также тип `AzureServicePrincipal` подключения для создания ресурса `AzureRunAsConnection` подключения. Если вы попытаетесь создать новый ресурс подключения для подключения к службе или приложению с другим методом проверки подлинности, операция завершится ошибкой, так как тип подключения еще не определен в учетной записи службы автоматизации. Дополнительные сведения о создании собственного типа подключения для пользовательского модуля см. в разделе [Добавление типа подключения](#add-a-connection-type).

## <a name="add-a-connection-type"></a>Добавление типа подключения

Если модуль Runbook или Конфигурация DSC подключается к внешней службе, необходимо определить тип соединения в [пользовательском модуле](shared-resources/modules.md#custom-modules) , который называется модулем интеграции. Этот модуль включает файл метаданных, указывающий свойства типа соединения, и имя ** &lt;ModuleName&gt;-Automation. JSON**, расположенное в папке Module сжатого файла **ZIP** . Этот файл содержит поля подключения, необходимые для подключения к системе или службе, которую представляет модуль. С помощью этого файла можно задать имена полей, типы данных, состояние шифрования и необязательное состояние для типа соединения. 

В следующем примере показан шаблон в формате **JSON** , который определяет свойства имени пользователя и пароля для пользовательского типа соединения с именем `MyModuleConnection`:

```json
{
   "ConnectionFields": [
   {
      "IsEncrypted":  false,
      "IsOptional":  true,
      "Name":  "Username",
      "TypeName":  "System.String"
   },
   {
      "IsEncrypted":  true,
      "IsOptional":  false,
      "Name":  "Password",
      "TypeName":  "System.String"
   }
   ],
   "ConnectionTypeName":  "MyModuleConnection",
   "IntegrationModuleName":  "MyModule"
}
```

## <a name="get-a-connection-in-a-runbook-or-dsc-configuration"></a>Получение подключения в модуле Runbook или конфигурации DSC

Получите подключение в модуле Runbook или конфигурации DSC с помощью внутреннего `Get-AutomationConnection` командлета. Этот командлет является предпочтительным по `Get-AzAutomationConnection` отношению к командлету, так как он получает значения соединения вместо сведений о соединении. 

### <a name="textual-runbook-example"></a>Пример текстового Runbook

В следующем примере показано, как использовать учетную запись запуска от имени для проверки подлинности с помощью ресурсов Azure Resource Manager в модуле Runbook. Он использует ресурс подключения, представляющий учетную запись запуска от имени, которая ссылается на субъект-службу, основанную на сертификате.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

### <a name="graphical-runbook-examples"></a>Примеры графических модулей Runbook

Вы можете добавить действие для внутреннего `Get-AutomationConnection` командлета в графический модуль Runbook. Щелкните правой кнопкой мыши соединение в области библиотека графического редактора и выберите команду **Добавить в холст**.

![Добавление на холст](media/automation-connections/connection-add-canvas.png)

На следующем рисунке показан пример использования объекта соединения в графическом модуле Runbook. В `Constant value` этом примере используется набор данных для `Get RunAs Connection` действия, который использует объект соединения для проверки подлинности. Здесь используется [ссылка на конвейер](automation-graphical-authoring-intro.md#links-and-workflow) , так как `ServicePrincipalCertificate` набор параметров ожидает один объект.

![Установление подключений](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-example"></a>Пример модуля Runbook Python 2

В следующем примере показано, как выполнить проверку подлинности с помощью подключения запуска от имени в модуле Runbook Python 2.

```python
""" Tutorial to show how to authenticate against Azure resource manager resources """
import azure.mgmt.resource
import automationassets

def get_automation_runas_credential(runas_connection):
    """ Returns credentials to authenticate against Azure resoruce manager """
    from OpenSSL import crypto
    from msrestazure import azure_active_directory
    import adal

    # Get the Azure Automation Run As service principal certificate
    cert = automationassets.get_automation_certificate("AzureRunAsCertificate")
    pks12_cert = crypto.load_pkcs12(cert)
    pem_pkey = crypto.dump_privatekey(
        crypto.FILETYPE_PEM, pks12_cert.get_privatekey())

    # Get Run As connection information for the Azure Automation service principal
    application_id = runas_connection["ApplicationId"]
    thumbprint = runas_connection["CertificateThumbprint"]
    tenant_id = runas_connection["TenantId"]

    # Authenticate with service principal certificate
    resource = "https://management.core.windows.net/"
    authority_url = ("https://login.microsoftonline.com/" + tenant_id)
    context = adal.AuthenticationContext(authority_url)
    return azure_active_directory.AdalAuthentication(
        lambda: context.acquire_token_with_client_certificate(
            resource,
            application_id,
            pem_pkey,
            thumbprint)
    )


# Authenticate to Azure using the Azure Automation Run As service principal
runas_connection = automationassets.get_automation_connection(
    "AzureRunAsConnection")
azure_credential = get_automation_runas_credential(runas_connection)
```

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о командлетах, используемых для доступа к подключениям, см. в статье [Управление модулями в службе автоматизации Azure](shared-resources/modules.md).
* Общие сведения о модулях Runbook см. [в статье выполнение Runbook в службе автоматизации Azure](automation-runbook-execution.md).
* Дополнительные сведения о конфигурациях DSC см. в разделе [Общие сведения о конфигурации состояния](automation-dsc-overview.md).