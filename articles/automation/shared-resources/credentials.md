---
title: Ресурсы учетных данных в службе автоматизации Azure
description: Ресурсы-контейнеры учетных данных службы автоматизации Azure содержат учетные данные безопасности, которые могут использоваться для проверки подлинности при доступе к ресурсам с помощью модуля Runbook или конфигурации DSC. В этой статье описывается, как создать ресурсы-контейнеры учетных данных и использовать их в модуле Runbook или конфигурации DSC.
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 01/31/2020
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 767c1fddbc3d1f46d4341a70c990c2b57ad40e54
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373532"
---
# <a name="credential-assets-in-azure-automation"></a>Ресурсы учетных данных в службе автоматизации Azure

Ресурс-контейнер учетных данных службы автоматизации содержит объект, который содержит учетные данные безопасности, такие как имя пользователя и пароль. Модули Runbook и конфигурации DSC могут использовать командлеты, которые принимают объект PSCredential для проверки подлинности или извлекать имя пользователя и пароль объекта PSCredential, чтобы предоставить их каким-либо приложениям или службам, требующим проверку подлинности. Свойства для учетных данных безопасно хранятся в службе автоматизации Azure. К ним можно получить доступ в модуле Runbook или конфигурации DSC с помощью действия [Get-AutomationPSCredential](#activities).

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

> [!NOTE]
> Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в службе автоматизации Azure с помощью уникального ключа, который создается для каждой учетной записи службы автоматизации. Этот ключ хранится в Key Vault. Перед сохранением защищенного ресурса ключ загружается из Key Vault, а затем используется для шифрования ресурса.

## <a name="azure-powershell-az-cmdlets"></a>Azure PowerShell AZ командлеты

Для Azure PowerShell AZ командлеты, приведенные в следующей таблице, используются для создания ресурсов учетных данных службы автоматизации и управления ими с помощью Windows PowerShell. Они поставляются как часть [модуля азуреаз. Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), который доступен для использования в модулях Runbook службы автоматизации и конфигурациях DSC.

| Командлеты | Описание |
|:--- |:--- |
| [Get-Азаутоматионкредентиал](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Извлекает сведения о ресурсе учетных данных. Это не возвращает объект PSCredential.  |
| [New-Азаутоматионкредентиал](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Создает новые учетные данные службы автоматизации. |
| [Remove-Азаутоматионкредентиал](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Удаляет учетные данные службы автоматизации. |
| [Set-Азаутоматионкредентиал](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Задает свойства для существующих учетных данных службы автоматизации. |

## <a name="activities"></a>Действия

Действия в следующей таблице используются для доступа к учетным данным в модуле Runbook и конфигурациях DSC.

| Действия | Описание |
|:--- |:--- |
| Get-AutomationPSCredential |Получает учетные данные для использования в модуле Runbook или в конфигурации DSC. Возвращает объект [System.Management.Automation.PSCredential](/dotnet/api/system.management.automation.pscredential) . |

> [!NOTE]
> Не следует использовать переменные в параметре –Name действия Get-AutomationPSCredential, так как это может усложнить обнаружение зависимостей между модулями Runbook или конфигурациями DSC и ресурсами-контейнерами учетных данных во время разработки.

## <a name="python2-functions"></a>Функции Python2

Функция, приведенная в следующей таблице, используется для доступа к учетным данным в модуле Runbook Python2.

| Компонент | Описание |
|:---|:---|
| automationassets.get_automation_credential | Извлекает сведения о ресурсе учетных данных. |

> [!NOTE]
> Для доступа к функциям ресурсов, необходимо импортировать модуль automationassets в верхнюю часть модуля Runbook Python.

## <a name="creating-a-new-credential-asset"></a>Создание нового ресурса учетных данных

### <a name="to-create-a-new-credential-asset-with-the-azure-portal"></a>Создание нового ресурса учетных данных на портале Azure

1. В учетной записи службы автоматизации в области **Общие ресурсы** выберите **Учетные данные**.
1. Щелкните **Добавить учетные данные**.
1. Заполните форму и выберите **создать** , чтобы сохранить новые учетные данные.

> [!NOTE]
> Учетные записи пользователей, которые используют многофакторную проверку подлинности, не поддерживаются для использования в службе автоматизации Azure.

### <a name="to-create-a-new-credential-asset-with-windows-powershell"></a>Создание нового ресурса учетных данных с помощью Windows PowerShell

Команды, приведенные ниже в примере, демонстрируют создание новых учетных данных службы автоматизации. Сначала создается объект PSCredential с именем и паролем, а затем он используется для создания ресурса учетных данных. Кроме того, можно использовать командлет **Get-Credential**, чтобы получить запрос на ввод имени и пароля.

```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Использование учетных данных PowerShell

Для получения ресурса-контейнера учетных данных из модуля Runbook или конфигурации DSC используется действие **Get-AutomationPSCredential**. Этот метод возвращает [объект PSCredential](/dotnet/api/system.management.automation.pscredential), который можно использовать с действием или командлетом, требующим параметр PSCredential. Можно также получить свойства объекта учетных данных, чтобы использовать их по отдельности. Объект имеет свойство для имени пользователя и защищенного пароля, или же вы можете использовать метод **GetNetworkCredential** для возврата объекта [NetworkCredential](/dotnet/api/system.net.networkcredential), предоставляющего незащищенную версию пароля.

> [!NOTE]
> **Get-азаутоматионкредентиал** не возвращает значение **PSCredential** , которое можно использовать для проверки подлинности. Он предоставляет только сведения об учетных данных. Если необходимо использовать учетные данные в модуле Runbook, то для получения объекта **PSCredential** необходимо использовать **Get-AutomationPSCredential** .

### <a name="textual-runbook-sample"></a>Пример текстового Runbook

Команды в приведенном ниже примере демонстрируют использование учетных данных PowerShell в Runbook. В этом примере извлекаются учетные данные, и имя пользователя и пароль из них присваиваются переменным.

```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Вы также можете использовать учетные данные для проверки подлинности в Azure с помощью [Connect-азаккаунт](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0). В большинстве случаев следует использовать [учетную запись запуска от имени](../manage-runas-account.md) и получать ее с помощью [Get-азаутоматионконнектион](../automation-connections.md).

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

На следующем рисунке показан пример использования учетных данных в графическом Runbook. В этом случае он используется для проверки подлинности модуля Runbook в ресурсах Azure, как описано в статье Проверка подлинности [модулей Runbook с помощью учетной записи пользователя Azure AD](../automation-create-aduser-account.md). Первое действие получает учетные данные с доступом к подписке Azure. Затем действие **Connect-AzureRmAccount** использует эти учетные данные для проверки подлинности всех действий, которые поступают после него. Здесь используется [конвейерная связь](../automation-graphical-authoring-intro.md#links-and-workflow) , так как **Get-AutomationPSCredential** ожидает один объект.  

![Добавление учетных данных на холст](../media/credentials/get-credential.png)

## <a name="using-a-powershell-credential-in-dsc"></a>Использование учетных данных PowerShell в DSC

Хотя конфигурации DSC в службе автоматизации Azure могут ссылаться на ресурсы учетных данных с помощью командлета **Get-AutomationPSCredential**, ресурсы учетных данных также могут передаваться через параметры, если это необходимо. Дополнительные сведения см. в статье [Компилирование конфигураций в Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

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

## <a name="next-steps"></a>Следующие шаги

* Дополнительные сведения о графической разработке см. в разделе [Связи и рабочий процесс](../automation-graphical-authoring-intro.md#links-and-workflow).
* Сведения о различных методах аутентификации в службе автоматизации см. в статье [Обеспечение безопасности в службе автоматизации Azure](../automation-security-overview.md).
* Чтобы начать работу с графическими модулями Runbook, см. инструкции в статье [Первый графический Runbook](../automation-first-runbook-graphical.md).
* Чтобы приступить к работе с модулями Runbook рабочих процессов PowerShell, обратитесь к статье [Мой первый модуль Runbook рабочего процесса PowerShell](../automation-first-runbook-textual.md)
* Сведения о том, как начать работу с модулями Runbook Python2, см. в статье [My first Python runbook](../automation-first-runbook-textual-python2.md) (Мой первый модуль Runbook Python). 
