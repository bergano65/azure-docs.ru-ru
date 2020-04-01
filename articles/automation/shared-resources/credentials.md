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
ms.openlocfilehash: 680e68d17637d71c1a1e5e8cfa539ee90028ac4e
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478767"
---
# <a name="credential-assets-in-azure-automation"></a>Ресурсы учетных данных в службе автоматизации Azure

Актив учетных данных Automation содержит объект, содержащий учетные данные безопасности, такие как имя пользователя и пароль. В конфигурациях Runbooks и DSC используются cmdlets, которые принимают объект [PSCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential?view=pscore-6.2.0) для проверки подлинности. Кроме того, они могут извлечь имя `PSCredential` пользователя и пароль объекта, чтобы предоставить какое-либо приложение или службу, требующую проверки подлинности. Azure Automation надежно хранит свойства учетных данных и доступ к свойствам в конфигурации runbook или DSC с помощью активности [Get-AutomationPSCredential.](#activities-used-to-access-credentials)

> [!NOTE]
> Безопасные средства в службе автоматизации Azure включают учетные данные, сертификаты, подключения и зашифрованные переменные. Эти ресурсы шифруются и хранятся в Azure Automation с помощью уникального ключа, который генерируется для каждой учетной записи Автоматизации. Этот ключ хранится в Key Vault. Перед сохранением защищенного ресурса ключ загружается из Key Vault, а затем используется для шифрования ресурса.

[!INCLUDE [gdpr-dsr-and-stp-note.md](../../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="azure-powershell-az-cmdlets-used-for-credential-assets"></a>Azure PowerShell Az cmdlets, используемые для учетных данных

Для модуля Azure PowerShell Az cmdlets в следующей таблице используются для создания и управления учетными данными автоматизации с помощью Windows PowerShell. Они погрузки в рамках [модуля Az.Automation](/powershell/azure/new-azureps-module-az?view=azps-1.1.0), который доступен для использования в автоматизации runbooks и DSC конфигураций. Смотрите [поддержку модулей Az в Azure Automation.](https://docs.microsoft.com/azure/automation/az-modules)

| Командлет | Описание |
|:--- |:--- |
| [Get-AzAutomationCredential](/powershell/module/az.automation/get-azautomationcredential?view=azps-3.3.0) |Извлекает сведения о ресурсе учетных данных. Этот cmdlet не возвращает объект PSCredential.  |
| [Новый-AzAutomationCredential](/powershell/module/az.automation/new-azautomationcredential?view=azps-3.3.0) |Создает новые учетные данные службы автоматизации. |
| [Удалить-AzAutomationCredential](/powershell/module/az.automation/remove-azautomationcredential?view=azps-3.3.0) |Удаляет учетные данные службы автоматизации. |
| [Set-AzAutomationCredential](/powershell/module/az.automation/set-azautomationcredential?view=azps-3.3.0) |Задает свойства для существующих учетных данных службы автоматизации. |

## <a name="activities-used-to-access-credentials"></a>Действия, используемые для доступа к учетных данных

Действия в следующей таблице используются для доступа к учетным данным в конфигурациях runbooks и DSC.

| Действие | Описание |
|:--- |:--- |
| `Get-AutomationPSCredential` |Получает учетные данные для использования в модуле Runbook или в конфигурации DSC. Учетные данные в `PSCredential` виде объекта. |
| [Get-Credential](https://docs.microsoft.com/powershell/module/microsoft.powershell.security/get-credential?view=powershell-7) |Получает учетные данные с запросом на имя пользователя и пароль. |
| [New-AzureAutomationCredential](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azureautomationcredential?view=azuresmps-4.0.0) | Создает актив учетных данных. |

Для локальной разработки с помощью инструмента автоматизации Azure, `Get-AutomationPSCredential` cmdlet является частью сборки [AzureAutomationAuthoringToolkit.](https://www.powershellgallery.com/packages/AzureAutomationAuthoringToolkit/0.2.3.9) Для Работы Azure с контекстом автоматизации `Orchestrator.AssetManagement.Cmdlets`cmdlet находится в . Смотрите [модули управления в Azure Automation.](modules.md)

Чтобы получить `PSCredential` объекты в коде, можно установить [надстройку Microsoft Azure Automation ISE для PowerShell ISE.](https://github.com/azureautomation/azure-automation-ise-addon)

```azurepowershell
Install-Module AzureAutomationAuthoringToolkit -Scope CurrentUser -Force
```

Ваш скрипт также может импортировать необходимый модуль, где это необходимо, как в следующем примере: 

```azurepowershell
Import-Module Orchestrator.AssetManagement.Cmdlets -ErrorAction SilentlyContinue
```

> [!NOTE]
> Следует избегать использования переменных в параметре `Name` `Get-AutomationPSCredential`. Их использование может усложнить обнаружение зависимостей между конфигурациями runbooks или DSC и учетными данными во время проектирования.

## <a name="python2-functions-that-access-credentials"></a>Функции Python2, которые получают доступ к учетные данные

Функция, приведенная в следующей таблице, используется для доступа к учетным данным в модуле Runbook Python2.

| Функция | Описание |
|:---|:---|
| `automationassets.get_automation_credential` | Извлекает сведения о ресурсе учетных данных. |

> [!NOTE]
> Импортируйте `automationassets` модуль в верхней части runbook Python для доступа к функциям актива.

## <a name="creating-a-new-credential-asset"></a>Создание нового ресурса учетных данных

Создать новый учетный учет можно с помощью портала Azure или с помощью Windows PowerShell.

### <a name="create-a-new-credential-asset-with-the-azure-portal"></a>Создание нового актива учетных данных с помощью портала Azure

1. Из учетной записи автоматизации выберите **учетные данные** под **общими ресурсами.**
1. Щелкните **Добавить учетные данные**.
2. В панели New Credential введите соответствующее имя учетных данных в соответствии со стандартами именования. 
3. Введите идентификатор доступа в поле **имени пользователя.** 
4. Для обоих полей паролей введите свой секретный ключ доступа.
5. Если проверена многофакторная коробка аутентификации, отоверьте его. 
6. Нажмите **Создать,** чтобы сохранить новый актив учетных данных.

> [!NOTE]
> Azure Automation не поддерживает учетные записи пользователей, которые используют многофакторную аутентификацию.

### <a name="create-a-new-credential-asset-with-windows-powershell"></a>Создание нового актива учетных данных с помощью Windows PowerShell

Ниже приводится следующий пример, как создать новый актив учетных данных Automation. Объект `PSCredential` сначала создается с именем и паролем, а затем используется для создания ресурса учетных данных. Кроме того, вы `Get-Credential` можете использовать cmdlet, чтобы побудить пользователя ввести имя и пароль.


```powershell
$user = "MyDomain\MyUser"
$pw = ConvertTo-SecureString "PassWord!" -AsPlainText -Force
$cred = New-Object –TypeName System.Management.Automation.PSCredential –ArgumentList $user, $pw
New-AzureAutomationCredential -AutomationAccountName "MyAutomationAccount" -Name "MyCredential" -Value $cred
```

## <a name="using-a-powershell-credential"></a>Использование учетных данных PowerShell

Конфигурация runbook или DSC получает ресурс `Get-AutomationPSCredential` учетных данных с помощью действия. Это действие извлекает `PSCredential` объект, который можно использовать с действием или cmdlet, который требует учетных данных. Можно также получить свойства объекта учетных данных, чтобы использовать их по отдельности. Объект имеет свойства для имени пользователя и защищенного пароля. Кроме того, можно использовать метод [GetNetworkCredential](https://docs.microsoft.com/dotnet/api/system.management.automation.pscredential.getnetworkcredential?view=pscore-6.2.0) для получения объекта [NetworkCredential,](/dotnet/api/system.net.networkcredential) представляющего незащищенную версию пароля.

> [!NOTE]
> `Get-AzAutomationCredential`не получает `PSCredential` объект, который может быть использован для проверки подлинности. Он предоставляет только информацию о учетных данных. Если вам нужно использовать учетные данные в runbook, `PSCredential` вы `Get-AutomationPSCredential`должны получить его в качестве объекта с помощью.

### <a name="textual-runbook-example"></a>Пример текстового запуска

Ниже приводится следующий пример использования учетных данных PowerShell в справочнике. Он получает учетные данные и присваивает свое имя пользователя и пароль переменным.


```azurepowershell
$myCredential = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCredential.UserName
$securePassword = $myCredential.Password
$password = $myCredential.GetNetworkCredential().Password
```

Вы также можете использовать учетные данные для проверки подлинности в Azure с [помощью Connect-AzAccount.](/powershell/module/az.accounts/connect-azaccount?view=azps-3.3.0) В большинстве случаев следует использовать [учетную запись Run As](../manage-runas-account.md) и получить соединение с [Get-AzAutomationConnection.](../automation-connections.md)


```azurepowershell
$myCred = Get-AutomationPSCredential -Name 'MyCredential'
$userName = $myCred.UserName
$securePassword = $myCred.Password
$password = $myCred.GetNetworkCredential().Password

$myPsCred = New-Object System.Management.Automation.PSCredential ($userName,$password)

Connect-AzAccount -Credential $myPsCred
```

### <a name="graphical-runbook-example"></a>Графический пример runbook

Вы можете `Get-AutomationPSCredential` добавить действие в графический runbook, нажав правой нажав на учетные данные в библиотеке панели графического редактора и выбрав **Добавить на холст**.

![Добавление учетных данных на холст](../media/credentials/credential-add-canvas.png)

На следующем рисунке показан пример использования учетных данных в графическом Runbook. В этом случае учетные данные используются для обеспечения аутентификации для службы в службе безопасности ресурсов Azure, как описано в [Use Azure AD в Azure Automation для аутентификации azure.](../automation-use-azure-ad.md) Первое действие получает учетные данные с доступом к подписке Azure. Затем действие подключения к учетной записи использует этот учетный данный для обеспечения аутентификации любых действий, которые появляются после нее. Ссылка [конвейера](../automation-graphical-authoring-intro.md#links-and-workflow) используется `Get-AutomationPSCredential` здесь, так как ожидает один объект.  

![Добавление учетных данных на холст](../media/credentials/get-credential.png)

## <a name="using-credentials-in-a-dsc-configuration"></a>Использование учетных данных в конфигурации DSC

В то время как конфигурации DSC в `Get-AutomationPSCredential`Azure Automation могут работать с учетными данными с использованием, они также могут передавать учетные данные по параметрам. Дополнительные сведения см. в статье [Компилирование конфигураций в Azure Automation DSC](../automation-dsc-compile.md#credential-assets).

## <a name="using-credentials-in-python2"></a>Использование учетных данных в Python2

В следующем примере показан пример доступа к учетным данным в runbooks Python2.


```python
import automationassets
from automationassets import AutomationAssetNotFound

# get a credential
cred = automationassets.get_automation_credential("credtest")
print cred["username"]
print cred["password"]
```

## <a name="next-steps"></a>Следующие шаги

* Чтобы узнать больше о ссылках в графическом авторизации, см [Ссылки в графическом авторизации](../automation-graphical-authoring-intro.md#links-and-workflow).
* Чтобы понять различные методы аутентификации для автоматизации, [см.](../automation-security-overview.md)
* Чтобы начать работу с графических runbooks, [см.](../automation-first-runbook-graphical.md)
* Чтобы начать работу с runbooks рабочего процесса PowerShell, [см.](../automation-first-runbook-textual.md)
* Чтобы начать работу с Python2 runbooks, смотрите [мой первый Python2 runbook](../automation-first-runbook-textual-python2.md). 
