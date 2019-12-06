---
title: Интеграция системы управления версиями и службы автоматизации Azure
description: В этой статье описывается интеграция системы управления версиями с GitHub в службе автоматизации Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: mgoedtel
ms.author: magoedte
ms.date: 04/26/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fcb3885446e534a2a6a5153545fd39fb14a02776
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850165"
---
# <a name="source-control-integration-in-azure-automation"></a>Интеграция системы управления версиями в службе автоматизации Azure

Система управления версиями позволяет синхронизировать модули Runbook в учетной записи службы автоматизации с помощью сценариев в репозитории GitHub или Azure Repos репозитория системы управления версиями. Система управления версиями позволяет работать вместе с коллегами, отслеживать изменения и выполнять откат к более ранним версиям модулей Runbook. Например,система управления версиями позволяет синхронизировать различные ветви с разработкой, тестированием или представлением учетных записей службы автоматизации. Это упрощает продвижение кода, который был протестирован в среде разработки, для представления учетной записи службы автоматизации. Интеграция системы управления версиями с автоматизацией поддерживает синхронизацию с одним направлением из репозитория системы управления версиями.

Служба автоматизации Azure поддерживает три типа системы управления версиями:

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="pre-requisites"></a>Технические условия

* Репозиторий системы управления версиями (GitHub или Azure Repos)
* [Учетная запись запуска от имени](manage-runas-account.md)
* Убедитесь, что у вас есть [последние модули Azure](automation-update-azure-modules.md) в учетной записи службы автоматизации.

> [!NOTE]
> Задания синхронизации системы управления версиями выполняются от имени учетной записи службы автоматизации соответствующих пользователей и оплачиваются по тому же тарифу, что и другие задания службы автоматизации.

## <a name="configure-source-control---azure-portal"></a>Настройка системы управления версиями — портал Azure

В учетной записи службы автоматизации выберите **система управления версиями** и щелкните **+ Добавить** .

![Выбор системы управления версиями](./media/source-control-integration/select-source-control.png)

Выберите **Тип системы управления версиями**, нажмите кнопку **Проверить подлинность**. Откроется окно браузера с запросом на вход, следуйте инструкциям на экране, чтобы завершить проверку подлинности.

На странице **Сводка системы управления версиями**, введите необходимые сведения и нажмите кнопку **Сохранить**. В следующей таблице показано описание доступных полей.

|Свойство  |Описание  |
|---------|---------|
|Имя системы управления версиями     | Понятное имя для системы управления версиями. *Это имя должно содержать только буквы и цифры.*        |
|Тип системы управления версиями     | Тип источника системы управления версиями. Доступные параметры:</br> GitHub</br>Azure Repos (Git)</br> Azure Repos (TFVC)        |
|Репозиторий     | Имя репозитория проекта. Возвращаются первые 200 репозиториев. Чтобы найти репозиторий, введите его имя в поле и нажмите кнопку **Поиск на GitHub**.|
|Branch     | Ветвь, из которой следует извлечь исходные файлы. Нацеливание на ветви недоступно для типа системы управления версиями TFVC.          |
|Путь к папке     | Папка, содержащая модули Runbook для синхронизации. Пример:/Рунбукс </br>*Синхронизируются только модули Runbook в указанной папке. Рекурсия не поддерживается.*        |
|Автоматическая синхронизация<sup>1</sup>     | Включает или выключает автоматическую синхронизацию при выполнении фиксации в репозитории системы управления версиями         |
|Публикация модуля Runbook     | Если задано значение **On**, то после синхронизации модулей Runbook из системы управления версиями они будут автоматически опубликованы.         |
|Описание     | Текстовое поле для указания дополнительных сведений        |

<sup>1</sup> чтобы включить автоматическую синхронизацию при настройке интеграции системы управления версиями с Azure Repos, необходимо быть администратором проекта.

![Сводка по системе управления версиями](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Имя входа для хранилища системы управления версиями может отличаться от имени входа для портал Azure. При настройке системы управления версиями убедитесь, что вы вошли в систему с использованием правильной учетной записи для репозитория системы управления версиями. Если есть сомнения, откройте новую вкладку в браузере, выйдите из visualstudio.com или github.com и повторите попытку подключения системы управления версиями.

## <a name="configure-source-control---powershell"></a>Настройка системы управления версиями с PowerShell

Вы также можете использовать PowerShell для настройки системы управления версиями в службе автоматизации Azure. Чтобы настроить систему управления версиями с помощью командлетов PowerShell, требуется персональный маркер доступа (PAT). Для создания подключения к системе управления версиями используется команда [New-азурермаутоматионсаурцеконтрол](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) . Командлету требуется защищенная строка личного маркера доступа. Дополнительные сведения о создании защищенной строки см. в разделе [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

### <a name="azure-repos-git"></a>Azure Repos (Git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure Repos (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Разрешения личного маркера доступа

Система управления версиями требует минимальные разрешения для личных маркеров доступа. В следующих таблицах содержатся минимальные разрешения, необходимые для GitHub и Azure Repos.

#### <a name="github"></a>GitHub

Дополнительные сведения о создании личного маркера доступа в GitHub см. в статье [Создание личного маркера доступа для командной строки](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Область действия  |Описание  |
|---------|---------|
|**Репозиторий**     |         |
|repo:status     | Доступ к фиксации состояния         |
|repo_deployment      | Доступ к состоянию развертывания         |
|public_repo     | Доступ к общим репозиториям         |
|**admin:repo_hook**     |         |
|запись:repo_hook     | Запись перехватчиков репозитория         |
|чтение:repo_hook|Чтение перехватчиков репозитория|

#### <a name="azure-repos"></a>Azure Repos

Дополнительные сведения о создании личного маркера доступа в Azure Repos см. в статье [Аутентификация доступа с помощью персональных маркеров доступа](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

|Область действия  |
|---------|
|Код (чтение)     |
|Проект и команда (чтение)|
|Удостоверение (чтение)      |
|Профиль пользователя (чтение)     |
|Рабочие элементы (чтение)    |
|Подключения служб (чтение, запрос и управление)<sup>1</sup>    |

<sup>1</sup> разрешение на подключения к службам требуется только в том случае, если включена Автосинхронизация.

## <a name="syncing"></a>Синхронизация

Выберите источник из таблицы на странице **системы управления версиями** . Нажмите кнопку **Начать синхронизацию**, чтобы запустить процесс.

Вы можете просмотреть состояние текущего задания синхронизации или предыдущие, щелкнув вкладку **Синхронизация заданий** . В раскрывающемся списке **система управления версиями** выберите систему управления версиями.

![Состояние синхронизации](./media/source-control-integration/sync-status.png)

Если щелкнуть какое-либо задание, можно просмотреть его выходные данные. Следующий пример иллюстрирует выходные данные задания синхронизации системы управления версиями.

```output
========================================================================================================

Azure Automation Source Control.
Supported runbooks to sync: PowerShell Workflow, PowerShell Scripts, DSC Configurations, Graphical, and Python 2.

Setting AzureRmEnvironment.

Getting AzureRunAsConnection.

Logging in to Azure...

Source control information for syncing:

[Url = https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl] [FolderPath = /Runbooks]

Verifying url: https://ContosoExample.visualstudio.com/ContosoFinanceTFVCExample/_versionControl

Connecting to VSTS...


Source Control Sync Summary:


2 files synced:
 - ExampleRunbook1.ps1
 - ExampleRunbook2.ps1



========================================================================================================
```

Дополнительное ведение журнала можно получить, выбрав **все журналы** на странице **Сводка по заданию синхронизации системы управления версиями** . Эти дополнительные записи журнала могут помочь в устранении неполадок, которые могут возникнуть при использовании системы управления версиями.

## <a name="disconnecting-source-control"></a>Отключение системы управления версиями

Чтобы отключиться от репозитория системы управления версиями, откройте **систему управления** версиями в разделе **Параметры учетной записи** в учетной записи службы автоматизации.

Выберите систему управления версиями, которую требуется удалить. На странице **Сводка системы управления версиями** щелкните **Удалить**.

## <a name="encoding"></a>Сервис кодирования

Если несколько пользователей редактируют модули Runbook в репозитории системы управления версиями с разными редакторами, существует вероятность возникновения проблем кодирования. Такая ситуация может привести к неправильным символам в модуле Runbook. Дополнительные сведения см. в разделе [распространенные причины проблем кодирования](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues) .

## <a name="updating-the-access-token"></a>Обновление маркера доступа

В настоящее время невозможно обновить маркер доступа в системе управления версиями с портала. После истечения срока действия личного маркера доступа можно обновить систему управления версиями с помощью нового маркера доступа следующим образом:

* Через [API-интерфейс RESTful](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* С помощью командлета [Update-азаутоматионсаурцеконтрол](/powershell/module/az.automation/update-azautomationsourcecontrol) .

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о типах модулей Runbook, их преимуществах и ограничениях см. в статье [Типы модулей Runbook в службе автоматизации Azure](automation-runbook-types.md).
