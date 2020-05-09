---
title: Управление учетными данными в службе автоматизации Azure
description: Ресурсы-контейнеры учетных данных службы автоматизации Azure содержат учетные данные безопасности, которые могут использоваться для проверки подлинности при доступе к ресурсам с помощью модуля Runbook или конфигурации DSC. В этой статье описывается, как создать ресурсы-контейнеры учетных данных и использовать их в модуле Runbook или конфигурации DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 16b92108bcb4e5185a1990b0ed8f1278bfe44921
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652835"
---
# <a name="manage-credentials-in-azure-automation"></a>Управление учетными данными в службе автоматизации Azure

Ресурс-контейнер учетных данных службы автоматизации содержит объект, содержащий учетные данные безопасности, такие как имя пользователя и пароль. Модули Runbook и конфигурации DSC используют командлеты, которые принимают объект [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) для проверки подлинности. Кроме того, они могут извлекать имя пользователя и пароль `PSCredential` объекта, чтобы предоставить некоторое приложение или службу, для которых требуется проверка подлинности. 

>[!NOTE]
>Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в службе автоматизации Azure с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Служба автоматизации Azure сохраняет ключ в управляемом системой Key Vault. Перед сохранением защищенного ресурса Автоматизация загружает ключ из Key Vault и затем использует его для шифрования ресурса. 

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке модуля Az в гибридной рабочей роли Runbook см. в статье об [установке модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Чтобы обновить модули в учетной записи службы автоматизации, см. руководство по [обновлению модулей Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md).

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="powershell-cmdlets-used-to-access-credentials"></a>Командлеты PowerShell, используемые для доступа к учетным данным

Командлеты, приведенные в следующей таблице, создают учетные данные службы автоматизации и управляют ими с помощью PowerShell. Они поставляются как часть [модулей AZ](modules.md#az-modules).

| Командлет | Описание |
|:--- |:--- |
| [Get-Азаутоматионкредентиал](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Извлекает объект [кредентиалинфо](https://docs.microsoft.com/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo?view=azurerm-ps) , содержащий метаданные об учетных данных. Командлет не извлекает сам `PSCredential` объект.  |
| [New-Азаутоматионкредентиал](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Создает новые учетные данные службы автоматизации. |
| [Remove-Азаутоматионкредентиал](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Удаляет учетные данные службы автоматизации. |
| [Set-Азаутоматионкредентиал](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Задает свойства для существующих учетных данных службы автоматизации. |

## <a name="other-cmdlets-used-to-access-credentials"></a>Другие командлеты, используемые для доступа к учетным данным

Командлеты, приведенные в следующей таблице, используются для доступа к учетным данным в модулях Runbook и конфигурациях DSC. 

| Командлет | Описание |
|:--- |:--- |
| `Get-AutomationPSCredential` |Возвращает `PSCredential` объект для использования в модуле Runbook или конфигурации DSC. Чаще всего следует использовать этот [внутренний командлет](modules.md#internal-cmdlets) вместо `Get-AzAutomationCredential` командлета, так как в последнем случае только сведения об учетных данных извлекаются. Эта информация обычно не полезна для передачи другому командлету. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Получает учетные данные с запросом имени пользователя и пароля. Этот командлет является частью модуля Microsoft. PowerShell. Security по умолчанию. См. раздел [модули по умолчанию](modules.md#default-modules).|
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Создает ресурс учетных данных. Этот командлет является частью модуля Azure по умолчанию. См. раздел [модули по умолчанию](modules.md#default-modules).|

Чтобы получить `PSCredential` объекты в коде, необходимо импортировать `Orchestrator.AssetManagement.Cmdlets` модуль. Дополнительные сведения см. [в статье Управление модулями в службе автоматизации Azure](modules.md).

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Не следует использовать переменные в `Name` параметре. `Get-AutomationPSCredential` Их использование может усложнить обнаружение зависимостей между модулями Runbook или конфигурациями DSC и ресурсами учетных данных во время разработки.

## <a name="python-2-functions-that-access-credentials"></a>Функции Python 2, обращающиеся к учетным данным

Функция, приведенная в следующей таблице, используется для доступа к учетным данным в модуле Runbook Python 2.

| Функция | Описание: |
|:---|:---|
| `automationassets.get_automation_credential` | Извлекает сведения о ресурсе учетных данных. |

> [!NOTE]
> Импортируйте `automationassets` модуль в верхней части модуля Runbook Python, чтобы получить доступ к функциям активов.

## <a name="create-a-new-credential-asset"></a>Создание нового ресурса учетных данных

Новый ресурс учетных данных можно создать с помощью портал Azure или с помощью Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Создание нового ресурса учетных данных с помощью портал Azure

1. В учетной записи службы автоматизации выберите **учетные данные** в разделе **Общие ресурсы**.
1. Щелкните **Добавить учетные данные**.
2. В области создать учетные данные введите соответствующее имя учетных данных, следуя стандартным стандартам именования. 
3. Введите свой идентификатор доступа в поле **имя пользователя** . 
4. В полях "пароль" введите секретный ключ доступа.

    ![Создать новые учетные данные](../media/credentials/credential-create.png)

5. Если флажок многофакторная проверка подлинности установлен, снимите его. 
6. Нажмите кнопку **создать** , чтобы сохранить новый ресурс учетных данных.

> [!NOTE]
> Служба автоматизации Azure не поддерживает учетные записи пользователей, которые используют многофакторную проверку подлинности.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Создание нового ресурса учетных данных с помощью Windows PowerShell

В следующем примере показано, как создать новый ресурс учетных данных службы автоматизации. Сначала `PSCredential` объект создается с именем и паролем, а затем используется для создания ресурса учетных данных. Вместо этого можно использовать `Get-Credential` командлет, чтобы предложить пользователю ввести имя и пароль.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="get-a-credential-asset"></a>Получение ресурса учетных данных

Модуль Runbook или Конфигурация DSC извлекает ресурс учетных данных с помощью `Get-AutomationPSCredential` внутреннего командлета. Этот командлет возвращает `PSCredential` объект, который можно использовать с командлетом, для которого требуются учетные данные. Можно также получить свойства объекта учетных данных, чтобы использовать их по отдельности. Объект имеет свойства для имени пользователя и защищенного пароля. 

> [!NOTE]
> `Get-AzAutomationCredential` Командлет не извлекает `PSCredential` объект, который можно использовать для проверки подлинности. Он предоставляет только сведения об учетных данных. Если необходимо использовать учетные данные в модуле Runbook, необходимо извлечь его как `PSCredential` объект с помощью. `Get-AutomationPSCredential`

Кроме того, можно использовать метод [жетнетворккредентиал](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) для получения объекта [NetworkCredential](/dotnet/api/system.net.networkcredential) , представляющего незащищенную версию пароля.

### <a name="textual-runbook-example"></a>Пример текстового Runbook

В следующем примере показано, как использовать учетные данные PowerShell в модуле Runbook. Он получает учетные данные и присваивает переменным имя пользователя и пароль.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Вы также можете использовать учетные данные для проверки подлинности в Azure с помощью [Connect-азаккаунт](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). В большинстве случаев следует использовать [учетную запись запуска от имени](../manage-runas-account.md) и получить подключение с помощью [Get-азаутоматионконнектион](../automation-connections.md).


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>Пример графического модуля Runbook

Вы можете добавить действие для внутреннего `Get-AutomationPSCredential` командлета в графический модуль Runbook, щелкнув правой кнопкой мыши учетные данные в области библиотека графического редактора и выбрав **Добавить на холст**.

![Добавление учетных данных на холст](../media/credentials/credential-add-canvas.png)

На следующем рисунке показан пример использования учетных данных в графическом Runbook. В этом случае учетные данные обеспечивают проверку подлинности для модуля Runbook в ресурсах Azure, как описано в статье [Использование Azure AD в службе автоматизации Azure для проверки подлинности в Azure](../automation-use-azure-ad.md). Первое действие получает учетные данные с доступом к подписке Azure. Затем действие подключения учетной записи использует эти учетные данные для проверки подлинности всех действий, которые поступают после него. Здесь используется [ссылка на конвейер](../automation-graphical-authoring-intro.md#links-and-workflow) , поскольку `Get-AutomationPSCredential` ожидается один объект.  

![Добавление учетных данных на холст](../media/credentials/get-credential.png)

## <a name="use-credentials-in-a-dsc-configuration"></a>Использование учетных данных в конфигурации DSC

Хотя конфигурации DSC в службе автоматизации Azure могут работать с ресурсами учетных данных с помощью `Get-AutomationPSCredential`, они также могут передавать активы учетных данных через параметры. Дополнительные сведения см. в статье [Компилирование конфигураций в Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="use-credentials-in-a-python-2-runbook"></a>Использование учетных данных в модуле Runbook Python 2

В следующем примере показан пример доступа к учетным данным в модулях Runbook Python 2.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о командлетах, используемых для доступа к учетным данным, см. [в статье Управление модулями в службе автоматизации Azure](modules.md).
* Общие сведения о модулях Runbook см. [в статье выполнение Runbook в службе автоматизации Azure](../automation-runbook-execution.md).
* Дополнительные сведения о конфигурациях DSC см. в разделе [Общие сведения о конфигурации состояния](../automation-dsc-overview.md).