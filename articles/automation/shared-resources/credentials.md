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
ms.openlocfilehash: 4226a625918be378b14e14c55fe4dd4ca5c398d5
ms.sourcegitcommit: 1ed0230c48656d0e5c72a502bfb4f53b8a774ef1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82136691"
---
# <a name="manage-credentials-in-azure-automation"></a>Управление учетными данными в службе автоматизации Azure

Ресурс-контейнер учетных данных службы автоматизации содержит объект, содержащий учетные данные безопасности, такие как имя пользователя и пароль. Модули Runbook и конфигурации DSC используют командлеты, которые принимают объект [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) для проверки подлинности. Кроме того, они могут извлекать имя пользователя и пароль `PSCredential` объекта, чтобы предоставить некоторое приложение или службу, для которых требуется проверка подлинности. 

> [!NOTE]
> Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в службе автоматизации Azure с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Этот ключ хранится в Key Vault. Перед сохранением защищенного ресурса ключ загружается из Key Vault, а затем используется для шифрования ресурса.

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке AZ Module в гибридной рабочей роли Runbook см. в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Для учетной записи службы автоматизации можно обновить модули до последней версии, используя [обновление модулей Azure PowerShell в службе автоматизации Azure](../automation-update-azure-modules.md).

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>Azure PowerShell AZ командлеты, используемые для учетных данных

В рамках модуля Azure PowerShell AZ командлеты, приведенные в следующей таблице, используются для создания ресурсов учетных данных службы автоматизации и управления ими с помощью Windows PowerShell. Они поставляются в [модуль AZ. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), доступный для использования в модулях Runbook службы автоматизации и конфигурациях DSC. См. статью [Поддержка модуля az в службе автоматизации Azure](https://docs.microsoft.com/azure/automation/az-modules).

| Командлет | Описание |
|:--- |:--- |
| [Get-Азаутоматионкредентиал](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Извлекает объект [кредентиалинфо](https://docs.microsoft.com/dotnet/api/microsoft.azure.commands.automation.model.credentialinfo?view=azurerm-ps) , содержащий метаданные об учетных данных. Командлет не извлекает сам `PSCredential` объект.  |
| [New-Азаутоматионкредентиал](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Создает новые учетные данные службы автоматизации. |
| [Remove-Азаутоматионкредентиал](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Удаляет учетные данные службы автоматизации. |
| [Set-Азаутоматионкредентиал](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Задает свойства для существующих учетных данных службы автоматизации. |

## <a name="activities-used-to-access-credentials"></a>Действия, используемые для доступа к учетным данным

Действия, приведенные в следующей таблице, используются для доступа к учетным данным в графических модулях Runbook и конфигурациях DSC. Примеры использования действий см. [в разделе графическая разработка в службе автоматизации Azure](../automation-graphical-authoring-intro.md#activities).

| Действие | Описание |
|:--- |:--- |
| `Get-AutomationPSCredential` |Возвращает `PSCredential` объект для использования в модуле Runbook или конфигурации DSC. Чаще всего это действие следует использовать вместо `Get-AzAutomationCredential` командлета, так как в последнем случае только сведения об учетных данных извлекаются. Эта информация обычно не полезна для передачи другому командлету. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Получает учетные данные с запросом имени пользователя и пароля. |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Создает ресурс учетных данных. |

Чтобы получить `PSCredential` объекты в коде, необходимо импортировать `Orchestrator.AssetManagement.Cmdlets` модуль. Дополнительные сведения см. [в статье Управление модулями в службе автоматизации Azure](modules.md).

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Не следует использовать переменные в `Name` параметре. `Get-AutomationPSCredential` Их использование может усложнить обнаружение зависимостей между модулями Runbook или конфигурациями DSC и ресурсами учетных данных во время разработки.

## <a name="python-2-functions-that-access-credentials"></a>Функции Python 2, обращающиеся к учетным данным

Функция, приведенная в следующей таблице, используется для доступа к учетным данным в модуле Runbook Python 2.

| Компонент | Описание |
|:---|:---|
| `automationassets.get_automation_credential` | Извлекает сведения о ресурсе учетных данных. |

> [!NOTE]
> Импортируйте `automationassets` модуль в верхней части модуля Runbook Python, чтобы получить доступ к функциям активов.

## <a name="creating-a-new-credential-asset"></a>Создание нового ресурса учетных данных

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

## <a name="using-a-powershell-credential"></a>Использование учетных данных PowerShell

Модуль Runbook или Конфигурация DSC получает ресурс учетных данных с `Get-AutomationPSCredential` действием. Это действие извлекает `PSCredential` объект, который можно использовать с действием или командлетом, для которого требуются учетные данные. Можно также получить свойства объекта учетных данных, чтобы использовать их по отдельности. Объект имеет свойства для имени пользователя и защищенного пароля. Кроме того, можно использовать метод [жетнетворккредентиал](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) для получения объекта [NetworkCredential](/dotnet/api/system.net.networkcredential) , представляющего незащищенную версию пароля.

> [!NOTE]
> `Get-AzAutomationCredential`не извлекает `PSCredential` объект, который можно использовать для проверки подлинности. Он предоставляет только сведения об учетных данных. Если необходимо использовать учетные данные в модуле Runbook, необходимо извлечь его как `PSCredential` объект с помощью. `Get-AutomationPSCredential`

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

Вы можете добавить `Get-AutomationPSCredential` действие в графический модуль Runbook, щелкнув правой кнопкой мыши учетные данные в области библиотека графического редактора и выбрав **Добавить на холст**.

![Добавление учетных данных на холст](../media/credentials/credential-add-canvas.png)

На следующем рисунке показан пример использования учетных данных в графическом Runbook. В этом случае учетные данные обеспечивают проверку подлинности для модуля Runbook в ресурсах Azure, как описано в статье [Использование Azure AD в службе автоматизации Azure для проверки подлинности в Azure](../automation-use-azure-ad.md). Первое действие получает учетные данные с доступом к подписке Azure. Затем действие подключения учетной записи использует эти учетные данные для проверки подлинности всех действий, которые поступают после него. Здесь используется [ссылка на конвейер](../automation-graphical-authoring-intro.md#links-and-workflow) , поскольку `Get-AutomationPSCredential` ожидается один объект.  

![Добавление учетных данных на холст](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>Использование учетных данных в конфигурации DSC

Хотя конфигурации DSC в службе автоматизации Azure могут работать с ресурсами учетных данных с помощью `Get-AutomationPSCredential`, они также могут передавать активы учетных данных через параметры. Дополнительные сведения см. в статье [Компилирование конфигураций в Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python-2"></a>Использование учетных данных в Python 2

В следующем примере показан пример доступа к учетным данным в модулях Runbook Python 2.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о создании ссылок в графической разработке см. в разделе [ссылки в графической разработке](../automation-graphical-authoring-intro.md#links-and-workflow).
* Сведения о различных методах проверки подлинности для автоматизации см. в статье [Безопасность службы автоматизации Azure](../automation-security-overview.md).
* Чтобы приступить к работе с графическими модулями Runbook, см. раздел [Мой первый графический модуль Runbook](../automation-first-runbook-graphical.md).
* Чтобы приступить к работе с модулями Runbook рабочих процессов PowerShell, см. [Мой первый Runbook рабочего процесса PowerShell](../automation-first-runbook-textual.md).
* Чтобы приступить к работе с модулями Runbook Python 2, ознакомьтесь с [моим первым модулем Runbook Python 2](../automation-first-runbook-textual-python2.md). 
