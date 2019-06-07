---
title: Ресурсы учетных данных в службе автоматизации Azure
description: Ресурсы-контейнеры учетных данных службы автоматизации Azure содержат учетные данные безопасности, которые могут использоваться для проверки подлинности при доступе к ресурсам с помощью модуля Runbook или конфигурации DSC. В этой статье описывается, как создать ресурсы-контейнеры учетных данных и использовать их в модуле Runbook или конфигурации DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 04/12/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: bb34c3f04302e6a2b5cc307b98bafe93e09fcf2f
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734707"
---
# <a name="credential-assets-in-azure-automation"></a>Ресурсы учетных данных в службе автоматизации Azure

Ресурс учетных данных автоматизации хранит объект, который содержит учетные данные безопасности, такие как имя пользователя и пароль. Модули Runbook и конфигурации DSC могут использовать командлеты, которые принимают объект PSCredential для проверки подлинности или извлекать имя пользователя и пароль объекта PSCredential, чтобы предоставить их каким-либо приложениям или службам, требующим проверку подлинности. Свойства для учетных данных безопасно хранятся в службе автоматизации Azure. К ним можно получить доступ в модуле Runbook или конфигурации DSC с помощью действия [Get-AutomationPSCredential](#activities).

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в службе автоматизации Azure с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Этот ключ хранится в Key Vault. Перед сохранением защищенного ресурса ключ загружается из Key Vault, а затем используется для шифрования ресурса.

## <a name="azure-classic-powershell-cmdlets"></a>Командлеты PowerShell для классического развертывания Azure

Командлеты, представленные в следующей таблице, используются для создания ресурсов учетных данных службы автоматизации и управления ими с помощью Windows PowerShell.  Они поставляются как часть [модуля Azure PowerShell](/powershell/azure/overview), которая доступна для использования в модулях Runbook службы автоматизации и конфигурации DSC.

| Командлеты | Описание |
|:--- |:--- |
| [Get-AzureAutomationCredential](/powershell/module/servicemanagement/azure/get-azureautomationcredential) |Извлекает сведения о ресурсе учетных данных. Сами учетные данные можно извлечь только с помощью действия **Get-AutomationPSCredential** . |
| [New-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Создает новые учетные данные службы автоматизации. |
| [Remove-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Удаляет учетные данные службы автоматизации. |
| [Set-AzureAutomationCredential](/powershell/module/servicemanagement/azure/new-azureautomationcredential) |Задает свойства для существующих учетных данных службы автоматизации. |

## <a name="azurerm-powershell-cmdlets"></a>Командлеты PowerShell для AzureRM

Для AzureRM командлеты, представленные в следующей таблице, используются для создания ресурсов учетных данных службы автоматизации и управления ими с помощью Windows PowerShell.  Они поставляются как часть [модуля AzureRM.Automation](/powershell/azure/overview), которая доступна для использования в модулях Runbook службы автоматизации и конфигурации DSC.

| Командлеты | Описание |
|:--- |:--- |
| [Get-AzureRMAutomationCredential](/powershell/module/azurerm.automation/get-azurermautomationcredential) |Извлекает сведения о ресурсе учетных данных. Это не возвращает объект PSCredential.  |
| [New-AzureRMAutomationCredential](/powershell/module/azurerm.automation/new-azurermautomationcredential) |Создает новые учетные данные службы автоматизации. |
| [Remove-AzureRmAutomationCredential](/powershell/module/azurerm.automation/remove-azurermautomationcredential) |Удаляет учетные данные службы автоматизации. |
| [Set-AzureRmAutomationCredential](/powershell/module/azurerm.automation/set-azurermautomationcredential) |Задает свойства для существующих учетных данных службы автоматизации. |

## <a name="activities"></a>Действия

Действия в следующей таблице используются для доступа к учетным данным в модуле Runbook и конфигурациях DSC.

| Действия | Описание |
|:--- |:--- |
| Get-AutomationPSCredential |Получает учетные данные для использования в модуле Runbook или в конфигурации DSC. Возвращает объект [System.Management.Automation.PSCredential](/dotnet/api/system.management.automation.pscredential) . |

> [!NOTE]
> Не следует использовать переменные в параметре –Name действия Get-AutomationPSCredential, так как это может усложнить обнаружение зависимостей между модулями Runbook или конфигурациями DSC и ресурсами-контейнерами учетных данных во время разработки.

## <a name="python2-functions"></a>Функции Python2

Функция, приведенная в следующей таблице, используется для доступа к учетным данным в модуле Runbook Python2.

| Функция | Описание |
|:---|:---|
| automationassets.get_automation_credential | Извлекает сведения о ресурсе учетных данных. |

> [!NOTE]
> Для доступа к функциям ресурсов, необходимо импортировать модуль automationassets в верхнюю часть модуля Runbook Python.

## <a name="creating-a-new-credential-asset"></a>Создание нового ресурса учетных данных

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Создание нового ресурса учетных данных на портале Azure

1. В учетной записи службы автоматизации в области **Общие ресурсы** выберите **Учетные данные**.
1. Щелкните **+ Add a credential** (+ Добавить учетные данные).
1. Заполните форму и нажмите кнопку **Создать** для сохранения новых учетных данных.

> [!NOTE]
> Учетные записи пользователей, которые используют многофакторную проверку подлинности, не поддерживаются для использования в службе автоматизации Azure.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Создание нового ресурса учетных данных с помощью Windows PowerShell

Команды, приведенные ниже в примере, демонстрируют создание новых учетных данных службы автоматизации. Сначала создается объект PSCredential с именем и паролем, а затем он используется для создания ресурса учетных данных. Кроме того, можно использовать командлет **Get-Credential** , чтобы отобразить запрос имени и пароля.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Использование учетных данных PowerShell

Для получения ресурса-контейнера учетных данных из модуля Runbook или конфигурации DSC используется действие **Get-AutomationPSCredential**. Оно возвращает [объект PSCredential](/dotnet/api/system.management.automation.pscredential), который можно использовать в действии или командлете, требующем параметр PSCredential. Можно также получить свойства объекта учетных данных, чтобы использовать их по отдельности. Объект имеет свойства для имени пользователя и надежного пароля. Также можно использовать метод **GetNetworkCredential** для возврата объекта [NetworkCredential](/dotnet/api/system.net.networkcredential), предоставляющего небезопасную версию пароля.

> [!NOTE]
> **Get-AzureRmAutomationCredential** не возвращает **PSCredential** , можно использовать для проверки подлинности. Он только сведения об учетных данных. Если необходимо использовать учетные данные в модуле runbook необходимо использовать **Get-AutomationPSCredential** извлекаемого **PSCredential** объекта.

### <a name="textual-runbook-sample"></a>Пример текстового Runbook

Команды в приведенном ниже примере демонстрируют использование учетных данных PowerShell в Runbook. В этом примере извлекаются учетные данные, и имя пользователя и пароль из них присваиваются переменным.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Можно также использовать учетные данные для проверки подлинности в Azure с помощью [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount). В большинстве случаев следует использовать [учетной записи запуска от имени](../manage-runas-account.md) и его извлечения [Get-AutomationConnection](../automation-connections.md).

```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzureRmAccount -Credential $myPsCred
```

### <a name="graphical-runbook-sample"></a>Пример графического Runbook

Можно добавить действие **Get-AutomationPSCredential** в графический Runbook, щелкнув правой кнопкой мыши учетные данные в области "Библиотека" графического редактора и выбрав пункт **Добавить на холст**.

![Добавление учетных данных на холст](../media/credentials/credential-add-canvas.png)

На следующем рисунке показан пример использования учетных данных в графическом Runbook.  В этом случае он используется для проверки подлинности для модуля runbook к ресурсам Azure, как описано в разделе [проверки подлинности модулей Runbook с помощью учетной записи пользователя Azure AD](../automation-create-aduser-account.md).  Первое действие получает учетные данные с доступом к подписке Azure.  Затем действие **Add-AzureAccount** использует эти учетные данные для аутентификации любых последующих действий.  Здесь используется [конвейерная связь](../automation-graphical-authoring-intro.md#links-and-workflow) , так как **Get-AutomationPSCredential** ожидает один объект.  

![Добавление учетных данных на холст](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Использование учетных данных PowerShell в DSC

Хотя конфигурации DSC в службе автоматизации Azure могут ссылаться на ресурсы учетных данных с помощью **Get-AutomationPSCredential**, ресурсы учетных данных также можно передать в с помощью параметров, если хотите, чтобы. Дополнительные сведения см. в статье [Компилирование конфигураций в Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Использование учетных данных в Python2

Ниже приведен пример доступа к учетным данным в модулях Runbook Python2.

```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о графической разработке см. в разделе [Связи и рабочий процесс](../automation-graphical-authoring-intro.md#links-and-workflow).
* Сведения о различных методах аутентификации в службе автоматизации см. в статье [Обеспечение безопасности в службе автоматизации Azure](../automation-security-overview.md).
* Чтобы начать работу с графическими модулями Runbook, см. инструкции в статье [Первый графический Runbook](../automation-first-runbook-graphical.md).
* Чтобы приступить к работе с модулями Runbook рабочих процессов PowerShell, обратитесь к статье [Мой первый модуль Runbook рабочего процесса PowerShell](../automation-first-runbook-textual.md)
* Сведения о том, как начать работу с модулями Runbook Python2, см. в статье [My first Python runbook](../automation-first-runbook-textual-python2.md) (Мой первый модуль Runbook Python). 
