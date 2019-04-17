---
title: Интеграция системы управления версиями и службы автоматизации Azure
description: В этой статье описывается интеграция системы управления версиями с GitHub в службе автоматизации Azure.
services: automation
ms.service: automation
ms.subservice: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 04/15/2019
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 81602f1a30fb753d7a8fcfccace581cd8c7b2f0c
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59607098"
---
# <a name="source-control-integration-in-azure-automation"></a>Интеграция системы управления версиями в службе автоматизации Azure

Системы управления версиями позволяет поддерживать модули Runbook в автоматизации учетной записи актуальны со сценариями в репозиторий системы управления версиями GitHub или репозитории Azure. Система управления версиями позволяет работать вместе с коллегами, отслеживать изменения и выполнять откат к более ранним версиям модулей Runbook. Например,система управления версиями позволяет синхронизировать различные ветви с разработкой, тестированием или представлением учетных записей службы автоматизации. Это упрощает продвижение кода, который был протестирован в среде разработки, для представления учетной записи службы автоматизации. Интеграция системы управления версиями с помощью службы автоматизации поддерживает одно направление синхронизации на репозиторий системы управления версиями.

Служба автоматизации Azure поддерживает три типа системы управления версиями:

* GitHub
* Azure репозиториев (Git)
* Azure репозиториев (TFVC)

## <a name="pre-requisites"></a>Предварительные требования

* Репозиторий системы управления версиями (GitHub или репозитории Azure)
* Объект [учетной записи запуска от имени](manage-runas-account.md)
* Убедитесь в наличии [последние модули Azure](automation-update-azure-modules.md) в учетной записи службы автоматизации

> [!NOTE]
> Задания синхронизации системы управления версиями выполняются от имени учетной записи службы автоматизации соответствующих пользователей и оплачиваются по тому же тарифу, что и другие задания службы автоматизации.

## <a name="configure-source-control---azure-portal"></a>Настройка системы управления версиями — портал Azure

В учетной записи службы автоматизации выберите **системы управления версиями** и нажмите кнопку **+ добавить**

![Выбор системы управления версиями](./media/source-control-integration/select-source-control.png)

Выберите **Тип системы управления версиями**, нажмите кнопку **Проверить подлинность**. Окно браузера откроется и появится запрос на вход, чтобы завершить проверку подлинности следуйте инструкциям на экране.

На странице **Сводка системы управления версиями**, введите необходимые сведения и нажмите кнопку **Сохранить**. В следующей таблице показано описание доступных полей.

|Свойство  |ОПИСАНИЕ  |
|---------|---------|
|Имя системы управления версиями     | Понятное имя для системы управления версиями. *Это имя должно содержать только буквы и цифры.*        |
|Тип системы управления версиями     | Тип источника системы управления версиями. Доступные параметры:</br> GitHub</br>Azure репозиториев (Git)</br> Azure репозиториев (TFVC)        |
|Репозиторий     | Имя репозитория проекта. Возвращаются первые 200 репозиториев. Чтобы найти репозиторий, введите имя в поле и нажмите кнопку **поиска на сайте GitHub**.|
|Ветвь     | Ветвь, из которой следует извлечь исходные файлы. Нацеливание ветви недоступен для тип системы управления версиями TFVC.          |
|Путь к папке     | Папка, содержащая синхронизируемые модули Runbook. Пример: /Runbooks </br>*Синхронизируются только модули Runbook в указанной папке. Рекурсия не поддерживается.*        |
|Автоматическая синхронизация     | Включает или выключает автоматическую синхронизацию при выполнении фиксации в репозитории системы управления версиями         |
|Публикация модуля Runbook     | Если значение **на**после синхронизированные модули Runbook из системы управления версиями, их будет опубликовано автоматически.         |
|ОПИСАНИЕ     | Текстовое поле для указания дополнительных сведений        |

![Сводка по системе управления версиями](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Убедитесь, что вы выполняете вход с использованием правильной учетной записи при настройке системы управления версиями. Если есть сомнения, откройте новую вкладку в браузере, выйдите из visualstudio.com или github.com и повторите попытку подключения системы управления версиями.

## <a name="configure-source-control---powershell"></a>Настройка системы управления версиями — PowerShell

Можно также использовать PowerShell для настройки системы управления версиями в службе автоматизации Azure. Чтобы настроить систему управления версиями с помощью командлетов PowerShell, требуется личный маркер доступа (PAT). Использовании [New AzureRmAutomationSourceControl](/powershell/module/AzureRM.Automation/New-AzureRmAutomationSourceControl) для создания соединениями системы управления версиями. Этот командлет требует защищенной строки из личного маркера доступа, чтобы узнать, как создать защищенную строку, см. в разделе [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

### <a name="azure-repos-git"></a>Azure репозиториев (Git)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="azure-repos-tfvc"></a>Azure репозиториев (TFVC)

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

### <a name="github"></a>GitHub

```powershell-interactive
New-AzureRmAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

### <a name="personal-access-token-permissions"></a>Разрешения личного маркера доступа

Система управления версиями требует минимальные разрешения для личных маркеров доступа. В следующих таблицах представлены минимальные разрешения, необходимые для GitHub и репозитории Azure.

#### <a name="github"></a>GitHub

Дополнительные сведения о создании личного маркера доступа в GitHub [Создание личного маркера доступа для командной строки](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Область  |ОПИСАНИЕ  |
|---------|---------|
|**Репозиторий**     |         |
|repo:status     | Доступ к фиксации состояния         |
|repo_deployment      | Доступ к состоянию развертывания         |
|public_repo     | Доступ к общим репозиториям         |
|**admin:repo_hook**     |         |
|запись:repo_hook     | Запись перехватчиков репозитория         |
|чтение:repo_hook|Чтение перехватчиков репозитория|

#### <a name="azure-repos"></a>Azure Repos

Дополнительные сведения о создании личного маркера доступа в репозитории Azure [проверки подлинности при доступе с личными маркерами доступа](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

|Область  |
|---------|
|Код (чтение)     |
|Проект и команда (чтение)|
|Удостоверение (чтение)      |
|Профиль пользователя (чтение)     |
|Рабочие элементы (чтение)    |
|Подключения служб (чтение, запрос и управление)<sup>1</sup>    |

<sup>1</sup> разрешение подключения к службе только в том случае требуется, если включена автоматическая синхронизация.

## <a name="syncing"></a>Синхронизация

Выберите источник из таблицы на **система управления версиями** страницы. Нажмите кнопку **Начать синхронизацию**, чтобы запустить процесс.

Можно просмотреть состояние текущего задания синхронизации или предыдущих заданий, щелкнув вкладку **Задания синхронизации**. В раскрывающемся списке **Система управления версиями** выберите систему управления версиями.

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

Более подробное ведение журнала доступно, выбрав **все журналы** на **Сводка по заданию синхронизации элемента управления источника** страницы. Эти дополнительные записи журнала, которые помогут устранить проблемы, которые могут возникнуть при использовании системы управления версиями.

## <a name="disconnecting-source-control"></a>Отключение системы управления версиями

Чтобы отключиться от репозиторий системы управления версиями, откройте **система управления версиями** под **параметры учетной записи** в учетной записи службы автоматизации.

Выберите систему управления версиями, которую требуется удалить. На странице **Сводка системы управления версиями** щелкните **Удалить**.

## <a name="encoding"></a>Кодирование

Если несколько человек изменение модулей Runbook в репозиторий системы управления версиями с разными редакторами, есть шанс на выполнение в кодировку проблемы. Это может привести к недопустимых символов в модуле runbook. Дополнительные сведения об этом см. в разделе [наиболее распространенных причин проблемы кодирования](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о типах модулей Runbook, их преимуществах и ограничениях см. в статье [Типы модулей Runbook в службе автоматизации Azure](automation-runbook-types.md).
