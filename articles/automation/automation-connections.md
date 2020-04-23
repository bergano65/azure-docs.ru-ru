---
title: Управление подключениями в службе автоматизации Azure
description: Ресурсы-контейнеры подключений в службе автоматизации Azure содержат данные, необходимые для подключения к внешней службе или приложению из модуля Runbook либо конфигурации DSC. В статье подробно рассматриваются подключения и работа с ними как в текстовых, так и в графических модулях.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: 39a41a60f4cabe995ebd458c4b906438d1e31bde
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82097121"
---
# <a name="manage-connections-in-azure-automation"></a>Управление подключениями в службе автоматизации Azure

Ресурс-контейнер подключения службы автоматизации содержит информацию, необходимую для подключения к внешней службе, приложению из модуля Runbook или конфигурации DSC. Это может включать данные, необходимые для аутентификации, такие как имя пользователя и пароль, а также информацию о подключении, например URL-адрес или порт. Вместо создания нескольких переменных значение подключения хранит в одном ресурсе все свойства для подключения к определенному приложению. Пользователь может изменять значения для подключения в одном месте, а передать имя подключения в модуль Runbook или конфигурацию DSC можно в одном параметре. Доступ к свойствам подключения можно получить в модуле Runbook или в конфигурации DSC с помощью `Get-AutomationConnection` действия.

При создании подключения необходимо указать тип подключения. Тип подключения — это шаблон, определяющий набор его свойств. Подключение определяет значения для каждого свойства, определенного в его типе. Типы подключения в службе автоматизации Azure добавляются в модули интеграции или создаются с помощью [API службы автоматизации](/previous-versions/azure/reference/mt163818(v=azure.100)), если модуль интеграции содержит тип подключения и импортируется в учетную запись службы автоматизации. В противном случае необходимо создать файл метаданных, чтобы указать тип соединения службы автоматизации. Дополнительные сведения об этом см. в разделе [модули интеграции](automation-integration-modules.md).

>[!NOTE]
>Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в службе автоматизации Azure с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Этот ключ хранится в хранилище ключей, управляемом системой. Перед сохранением защищенного ресурса ключ загружается из Key Vault, а затем используется для шифрования ресурса. Этим процессом управляет служба автоматизации Azure.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке AZ Module в гибридной рабочей роли Runbook см. в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Для учетной записи службы автоматизации можно обновить модули до последней версии, используя [обновление модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="connection-types"></a>Типы подключений

В службе автоматизации Azure доступны три типа встроенных подключений:

* **Azure** — это подключение можно использовать для управления классическими ресурсами.
* **AzureClassicCertificate** — это подключение используется учетной записью **AzureClassicRunAs**.
* **AzureServicePrincipal** — это подключение используется учетной записью **AzureRunAs**.

В большинстве случаев не нужно создавать ресурс подключения, так как он создается при создании [учетной записи запуска от имени](manage-runas-account.md).

## <a name="windows-powershell-cmdlets"></a>Командлеты Windows PowerShell

Командлеты, представленные в следующей таблице, используются для создания подключений и управления ими с помощью Windows PowerShell в службе автоматизации Azure. Они поставляются в составе [модуля Azure PowerShell](/powershell/azure/overview), который можно использовать в модулях Runbook службы автоматизации и конфигурациях DSC.

|Командлет|Описание|
|---|---|
|[Get-Азаутоматионконнектион](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|Извлекает подключение. Содержит хэш-таблицу со значениями полей соединения.|
|[New-Азаутоматионконнектион](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|Создает новое подключение.|
|[Remove-Азаутоматионконнектион](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|Удаляет существующее соединение.|
|[Set-Азаутоматионконнектионфиелдвалуе](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|Задает значение определенного поля для существующего подключения.|

## <a name="activities"></a>Действия

Действия в следующей таблице используются для доступа к подключениям в модуле Runbook или конфигурации DSC.

|Действия|Описание|
|---|---|
|`Get-AutomationConnection` | Получает подключение для использования. Возвращает хэш-таблицу со свойствами подключения.|

>[!NOTE]
>Избегайте использования переменных с `Name` параметром. `Get-AutomationConnection` Использование этого параметра может усложнить обнаружение зависимостей между модулями Runbook или конфигурациями DSC, а также ресурсами подключения во время разработки.

## <a name="python-2-functions"></a>Функции Python 2

Функция, приведенная в следующей таблице, используется для доступа к соединениям в модуле Runbook Python 2.

| Компонент | Описание |
|:---|:---|
| `automationassets.get_automation_connection` | Извлекает подключение. Возвращает словарь со свойствами подключения. |

> [!NOTE]
> Чтобы получить доступ к `automationassets` функциям активов, необходимо импортировать модуль в верхней части модуля Runbook Python.

## <a name="creating-a-new-connection"></a>Создание нового подключения

### <a name="create-a-new-connection-with-the-azure-portal"></a>Создание нового соединения с портал Azure

1. В учетной записи службы автоматизации щелкните часть **активы** , чтобы открыть колонку **ресурсы** .
2. Щелкните элемент **Подключения**, чтобы открыть колонку **Подключения**.
3. Щелкните **Добавить подключение** в верхней части колонки.
4. В раскрывающемся списке **Тип** выберите тип подключения, которое необходимо создать. Форма представит свойства для этого типа.
5. Заполните форму и нажмите кнопку **Создать** для сохранения нового подключения.

### <a name="create-a-new-connection-with-windows-powershell"></a>Создание нового подключения с помощью Windows PowerShell

Создайте новое подключение с помощью `New-AzAutomationConnection` командлета Windows PowerShell. Этот командлет имеет параметр с именем `ConnectionFieldValues` , который ожидает [хэш-таблицу](https://technet.microsoft.com/library/hh847780.aspx) , определяющую значения для каждого свойства, определенного типом соединения.

Приведенные ниже примеры команд можно использовать в качестве альтернативы созданию учетной записи запуска от имени на портале для создания нового ресурса подключения.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

При создании учетной записи службы автоматизации по умолчанию включается несколько глобальных модулей, а также тип `AzureServicePrincipal` подключения для создания ресурса `AzureRunAsConnection` подключения. Если вы попытаетесь создать новый ресурс подключения для подключения к службе или приложению с другим методом проверки подлинности, операция завершится ошибкой, так как тип подключения еще не определен в учетной записи службы автоматизации. Дополнительные сведения о создании собственного типа подключения для пользовательского модуля [коллекция PowerShell](https://www.powershellgallery.com) см. в разделе [модули интеграции](automation-integration-modules.md).

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Использование подключения в модуле Runbook или конфигурации DSC

Получите подключение в модуле Runbook или конфигурации DSC с помощью `Get-AutomationConnection` командлета. Вы не можете использовать `Get-AzAutomationConnection` действие. Это действие получает значения различных полей в соединении и возвращает их в виде [хэш-таблицы](https://go.microsoft.com/fwlink/?LinkID=324844). Затем эту хэш-таблицу можно использовать с соответствующими командами в модуле Runbook или конфигурации DSC.

### <a name="textual-runbook-sample"></a>Пример текстового Runbook

Ниже представлены примеры команд, которые используют упомянутую выше учетную запись запуска от имени для аутентификации при обращении из Runbook к ресурсам Azure Resource Manager. Здесь используются не учетные данные, а ресурс подключения, представляющий учетную запись запуска от имени, которая ссылается на субъект-службу на основе сертификатов.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!NOTE]
> Для неграфических модулей Runbook PowerShell `Add-AzAccount` и `Add-AzureRMAccount` являются псевдонимами для [Connect-азаккаунт](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Вы можете использовать эти командлеты или [обновить модули](automation-update-azure-modules.md) в учетной записи службы автоматизации до последних версий. Вам может потребоваться обновить модули, даже если вы только что создали новую учетную запись службы автоматизации.

### <a name="graphical-runbook-samples"></a>Графические примеры для модуля Runbook

Чтобы добавить `Get-AutomationConnection` действие в графический модуль Runbook, щелкните правой кнопкой мыши подключение в области библиотека графического редактора и выберите команду **Добавить в холст**.

![Добавление на холст](media/automation-connections/connection-add-canvas.png)

На следующем рисунке показан пример использования подключения в графическом Runbook. Это тот же пример, как показано выше, для проверки подлинности с помощью учетной записи запуска от имени с текстовым модулем Runbook. В `Constant value` этом примере используется набор данных для `Get RunAs Connection` действия, использующего объект соединения для проверки подлинности. Здесь используется [ссылка на конвейер](automation-graphical-authoring-intro.md#links-and-workflow) , так как `ServicePrincipalCertificate` набор параметров ожидает один объект.

![Установление подключений](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-sample"></a>Пример модуля Runbook Python 2

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

- Изучите, как использовать [связи в графическом редакторе](automation-graphical-authoring-intro.md#links-and-workflow), которые позволяют направлять и контролировать поток логики в Runbook.
* Справочник по командлетам PowerShell см. в разделе [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- В статье [Модули интеграции службы автоматизации Azure](automation-integration-modules.md) описывается использование модулей PowerShell в службе автоматизации Azure и рекомендации по созданию модулей PowerShell в качестве модулей интеграции для службы автоматизации Azure.