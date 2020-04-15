---
title: Ресурсы подключений в службе автоматизации Azure
description: Ресурсы-контейнеры подключений в службе автоматизации Azure содержат данные, необходимые для подключения к внешней службе или приложению из модуля Runbook либо конфигурации DSC. В статье подробно рассматриваются подключения и работа с ними как в текстовых, так и в графических модулях.
services: automation
ms.subservice: shared-capabilities
ms.date: 01/13/2020
ms.topic: conceptual
ms.openlocfilehash: 4840b135587ae776cfb80258ce513a48a79efa43
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383332"
---
# <a name="connection-assets-in-azure-automation"></a>Ресурсы подключений в службе автоматизации Azure

Ресурс-контейнер подключения службы автоматизации содержит информацию, необходимую для подключения к внешней службе, приложению из модуля Runbook или конфигурации DSC. Это может включать данные, необходимые для аутентификации, такие как имя пользователя и пароль, а также информацию о подключении, например URL-адрес или порт. Вместо создания нескольких переменных значение подключения хранит в одном ресурсе все свойства для подключения к определенному приложению. Пользователь может изменять значения для подключения в одном месте, а передать имя подключения в модуль Runbook или конфигурацию DSC можно в одном параметре. Свойства для подключения могут быть доступны в конфигурации Runbook `Get-AutomationConnection` или DSC с действием.

При создании подключения необходимо указать *тип подключения*. Тип подключения — это шаблон, определяющий набор его свойств. Подключение определяет значения для каждого свойства, определенного в его типе. Типы подключения в службе автоматизации Azure добавляются в модули интеграции или создаются с помощью [API службы автоматизации](/previous-versions/azure/reference/mt163818(v=azure.100)), если модуль интеграции содержит тип подключения и импортируется в учетную запись службы автоматизации. В противном случае необходимо создать файл метаданных для указания типа подключения автоматизации. Для получения дополнительной информации об этом, см [Интеграция Модули](automation-integration-modules.md).

>[!NOTE]
>Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в Azure Automation с помощью уникального ключа, который генерируется для каждой учетной записи Автоматизации. Этот ключ хранится в хранилище ключей, управляемом системой. Перед сохранением защищенного ресурса ключ загружается из Key Vault, а затем используется для шифрования ресурса. Этим процессом управляет служба автоматизации Azure.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="connection-types"></a>Типы подключений

В Azure Automation доступны три типа встроенных подключений:

* **Azure** — это подключение можно использовать для управления классическими ресурсами.
* **AzureClassicCertificate** — это подключение используется учетной записью **AzureClassicRunAs**.
* **AzureServicePrincipal** — это подключение используется учетной записью **AzureRunAs**.

В большинстве случаев не требуется создавать ресурс соединения, поскольку он создается при создании [учетной записи Run As.](manage-runas-account.md)

## <a name="windows-powershell-cmdlets"></a>Командлеты Windows PowerShell

Командлеты, представленные в следующей таблице, используются для создания подключений и управления ими с помощью Windows PowerShell в службе автоматизации Azure. Они погрузки в рамках [модуля Azure PowerShell](/powershell/azure/overview), который доступен для использования в автоматизации runbooks и DSC конфигураций.

|Командлет|Описание|
|---|---|
|[Get-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/get-azautomationconnection?view=azps-3.7.0)|Извлекает подключение. Включает хэш-таблицу со значениями полей соединения.|
|[Новое-AzAutomationConnection](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationconnection?view=azps-3.7.0)|Создает новое подключение.|
|[Удалить-AzAutomationConnection](https://docs.microsoft.com/powershell/module/Az.Automation/Remove-AzAutomationConnection?view=azps-3.7.0)|Удаляет существующее соединение.|
|[Set-AzAutomationConnectionFieldValue](https://docs.microsoft.com/powershell/module/Az.Automation/Set-AzAutomationConnectionFieldValue?view=azps-3.7.0)|Задает значение определенного поля для существующего подключения.|

## <a name="activities"></a>Действия

Действия в следующей таблице используются для доступа к подключениям в модуле Runbook или конфигурации DSC.

|Действия|Описание|
|---|---|
|`Get-AutomationConnection` | Получает подключение для использования. Возвращает хэш-таблицу со свойствами подключения.|

>[!NOTE]
>Избегайте использования переменных с параметром `Name` `Get-AutomationConnection`. Использование этого параметра может усложнить обнаружение зависимостей между конфигурациями runbooks или DSC и активами соединения во время проектирования.

## <a name="python-2-functions"></a>Функции Python 2

Функция в следующей таблице используется для доступа к соединениям в ступеньке Python 2.

| Компонент | Описание |
|:---|:---|
| `automationassets.get_automation_connection` | Извлекает подключение. Возвращает словарь со свойствами подключения. |

> [!NOTE]
> Для доступа `automationassets` к функциям актива необходимо импортировать модуль в верхней части вектора Python.

## <a name="creating-a-new-connection"></a>Создание нового соединения

### <a name="to-create-a-new-connection-with-the-azure-portal"></a>Создание нового подключения на портале Azure

1. С вашей учетной записи автоматизации щелкните часть **активов,** чтобы открыть лезвие **активов.**
2. Щелкните элемент **Подключения**, чтобы открыть колонку **Подключения**.
3. Щелкните **Добавить подключение** в верхней части колонки.
4. В раскрывающемся списке **Тип** выберите тип подключения, которое необходимо создать. Форма представит свойства для этого типа.
5. Заполните форму и нажмите кнопку **Создать** для сохранения нового подключения.

### <a name="to-create-a-new-connection-with-windows-powershell"></a>Создание нового подключения с помощью Windows PowerShell

Создайте новое соединение с Windows `New-AzAutomationConnection` PowerShell с помощью cmdlet. Этот cmdlet имеет `ConnectionFieldValues` названный параметр, который ожидает [хэштабели,](https://technet.microsoft.com/library/hh847780.aspx) определяющие значения для каждого из свойств, определяемых типом соединения.

В качестве альтернативы созданию учетной записи Run As с портала можно использовать следующие примеры для создания нового актива подключения.

```powershell
$ConnectionAssetName = "AzureRunAsConnection"
$ConnectionFieldValues = @{"ApplicationId" = $Application.ApplicationId; "TenantId" = $TenantID.TenantId; "CertificateThumbprint" = $Cert.Thumbprint; "SubscriptionId" = $SubscriptionId}
New-AzAutomationConnection -ResourceGroupName $ResourceGroup -AutomationAccountName $AutomationAccountName -Name $ConnectionAssetName -ConnectionTypeName AzureServicePrincipal -ConnectionFieldValues $ConnectionFieldValues
```

Вы можете использовать скрипт для создания актива подключения, потому что при создании учетной записи `AzureServicePrincipal` Автоматизации `AzureRunAsConnection` он автоматически включает в себя несколько глобальных модулей по умолчанию вместе с типом соединения для создания актива соединения. Важно помнить о том, что при попытке использовать другой метод аутентификации вы не сможете создать ресурс подключения для подключения к службе или приложению, так как другие типы подключения не определены в учетной записи службы автоматизации. Для получения дополнительной информации о том, как создать свой собственный тип соединения для вашего пользовательского или модуля из [галереи PowerShell](https://www.powershellgallery.com), см [Интеграция Модули](automation-integration-modules.md)

## <a name="using-a-connection-in-a-runbook-or-dsc-configuration"></a>Использование подключения в модуле Runbook или конфигурации DSC

Извлеките соединение в конфигурации runbook `Get-AutomationConnection` или DSC с помощью cmdlet. Вы не можете `Get-AzAutomationConnection` использовать действие. Это действие извлекает значения различных полей в связи и возвращает их в качестве [хэшбита.](https://go.microsoft.com/fwlink/?LinkID=324844) Этот хэш-таблица может быть использован анамбуком с соответствующими командами в конфигурации runbook или DSC.

### <a name="textual-runbook-sample"></a>Пример текстового Runbook

Ниже представлены примеры команд, которые используют упомянутую выше учетную запись запуска от имени для аутентификации при обращении из Runbook к ресурсам Azure Resource Manager. Здесь используются не учетные данные, а ресурс подключения, представляющий учетную запись запуска от имени, которая ссылается на субъект-службу на основе сертификатов.

```powershell
$Conn = Get-AutomationConnection -Name AzureRunAsConnection
Connect-AzAccount -ServicePrincipal -Tenant $Conn.TenantID -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
```

> [!NOTE]
> Для неграфических runbooks PowerShell, `Add-AzAccount` и `Add-AzureRMAccount` псевдонимы для [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount?view=azps-3.5.0). Вы можете использовать эти cmdlets или вы можете [обновить свои модули](automation-update-azure-modules.md) в вашей учетной записи автоматизации до последних версий. Возможно, потребуется обновить модули, даже если вы только что создали новую учетную запись Automation.

### <a name="graphical-runbook-samples"></a>Графические примеры для модуля Runbook

Вы добавляете `Get-AutomationConnection` действие в графический runbook, нажав правой нажав на соединение в панели библиотеки графического редактора и выбрав **Добавить на холст**.

![Добавление на холст](media/automation-connections/connection-add-canvas.png)

На следующем рисунке показан пример использования подключения в графическом Runbook. Это тот же пример, который представлен выше. Он выполняет аутентификацию с помощью учетной записи запуска от имени с текстовым Runbook. В этом `Constant value` примере используется `Get RunAs Connection` набор данных для действия, использующему объект соединения для проверки подлинности. Здесь используется [соединение конвейера,](automation-graphical-authoring-intro.md#links-and-workflow) поскольку набор `ServicePrincipalCertificate` параметров ожидает один объект.

![Установление подключений](media/automation-connections/automation-get-connection-object.png)

### <a name="python-2-runbook-sample"></a>Python 2 пример руны

В следующем примере показано, как аутентифицировать с помощью Run As соединения в runbook Python 2.

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

## <a name="next-steps"></a>Следующие шаги

- Изучите, как использовать [связи в графическом редакторе](automation-graphical-authoring-intro.md#links-and-workflow), которые позволяют направлять и контролировать поток логики в Runbook.
* Для справки PowerShell cmdlet [см.](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
)
- В статье [Модули интеграции службы автоматизации Azure](automation-integration-modules.md) описывается использование модулей PowerShell в службе автоматизации Azure и рекомендации по созданию модулей PowerShell в качестве модулей интеграции для службы автоматизации Azure.