---
title: Интеграция системы управления версиями в службе автоматизации Azure
description: В этой статье описывается интеграция системы управления версиями с GitHub в службе автоматизации Azure.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: b0eed8fe9d548ee54698d187e192960bb3b44e44
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79368814"
---
# <a name="source-control-integration-in-azure-automation"></a>Интеграция системы управления версиями в службе автоматизации Azure

 Интеграция системы управления версиями в службе автоматизации Azure поддерживает однонаправленную синхронизацию из репозитория системы управления версиями. Система управления версиями позволяет синхронизировать модули Runbook в учетной записи службы автоматизации с помощью скриптов в репозитории GitHub или Azure Repos репозитория системы управления версиями. Эта функция упрощает продвижение кода, протестированного в среде разработки, с рабочей учетной записью автоматизации.
 
 С помощью интеграции системы управления версиями вы можете легко сотрудничать с командой, отслеживанием изменений и откатом к более ранним версиям модулей Runbook. Например, система управления версиями позволяет синхронизировать различные ветви в системе управления версиями с учетными записями автоматизации разработки, тестирования и рабочей среды. 

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Инструкции по установке AZ Module в гибридной рабочей роли Runbook см. в статье [Установка модуля Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Для учетной записи службы автоматизации можно обновить модули до последней версии, используя [обновление модулей Azure PowerShell в службе автоматизации Azure](automation-update-azure-modules.md).

## <a name="source-control-types"></a>Типы системы управления версиями

Служба автоматизации Azure поддерживает три типа системы управления версиями:

* GitHub
* Azure Repos (Git)
* Azure Repos (TFVC)

## <a name="prerequisites"></a>предварительные требования

* Репозиторий системы управления версиями (GitHub или Azure Repos)
* [Учетная запись запуска от имени](manage-runas-account.md)
* [Последние модули Azure](automation-update-azure-modules.md) в учетной записи службы автоматизации, включая модуль `Az.Accounts` (AZ Module, эквивалентный `AzureRM.Profile`)

> [!NOTE]
> Задания синхронизации системы управления версиями выполняются под учетной записью службы автоматизации пользователя и выставляются по той же ставке, что и другие задания службы автоматизации.

## <a name="configuring-source-control"></a>Настройка системы управления версиями

В этом разделе описывается настройка системы управления версиями для учетной записи службы автоматизации. Можно использовать либо портал Azure, либо PowerShell.

### <a name="configure-source-control----azure-portal"></a>Настройка системы управления версиями — портал Azure

Используйте эту процедуру для настройки системы управления версиями с помощью портал Azure.

1. В учетной записи службы автоматизации выберите **система управления версиями** и щелкните **+ Добавить**.

    ![Выбор системы управления версиями](./media/source-control-integration/select-source-control.png)

2. Выберите **тип системы управления версиями**, а затем щелкните **Проверка подлинности**. 

3. Откроется окно браузера с запросом на вход. Следуйте инструкциям на экране, чтобы завершить проверку подлинности.

4. На странице Сводка системы управления версиями используйте поля для заполнения свойств системы управления версиями, определенных ниже. По завершении щелкните **Сохранить**. 

    |Свойство  |Description  |
    |---------|---------|
    |Имя системы управления версиями     | Понятное имя для системы управления версиями. Это имя должно содержать только буквы и цифры.        |
    |Тип системы управления версиями     | Тип механизма управления версиями. Доступные параметры:</br> * GitHub</br>* Azure Repos (Git)</br> * Azure Repos (TFVC)        |
    |Хранилище     | Имя репозитория или проекта. Извлекаются первые 200 репозиториев. Чтобы найти репозиторий, введите его имя в поле и нажмите кнопку **Поиск на GitHub**.|
    |Ветвь     | Ветвь, из которой необходимо извлечь исходные файлы. Нацеливание на ветви недоступно для типа системы управления версиями TFVC.          |
    |Путь к папке     | Папка, содержащая модули Runbook для синхронизации, например **/рунбукс**. Синхронизируются только модули Runbook в указанной папке. Рекурсия не поддерживается.        |
    |Автоматическая синхронизация<sup>1</sup>     | Параметр, который включает или отключает автоматическую синхронизацию при выполнении фиксации в репозитории системы управления версиями.        |
    |Публикация модуля Runbook     | Значение ON, если модули Runbook автоматически публикуются после синхронизации из системы управления версиями, и в противном случае.           |
    |Description     | Текст, указывающий дополнительные сведения о системе управления версиями.        |

    <sup>1</sup> чтобы включить автоматическую синхронизацию при настройке интеграции системы управления версиями с Azure Repos, необходимо быть администратором проекта.

   ![Сводка по системе управления версиями](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Имя входа для хранилища системы управления версиями может отличаться от имени входа для портал Azure. При настройке системы управления версиями убедитесь, что вы вошли в систему с использованием правильной учетной записи для репозитория системы управления версиями. В случае сомнений откройте новую вкладку в браузере, выйдите из **VisualStudio.com** или **GitHub.com**и снова попробуйте подключиться к системе управления версиями.

### <a name="configure-source-control----powershell"></a>Настройка системы управления версиями с PowerShell

Вы также можете использовать PowerShell для настройки системы управления версиями в службе автоматизации Azure. Чтобы использовать командлеты PowerShell для этой операции, необходим личный маркер доступа (PAT). Используйте командлет [New-азаутоматионсаурцеконтрол](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) , чтобы создать подключение к системе управления версиями. Для этого командлета требуется безопасная строка для PAT. Сведения о создании защищенной строки см. в разделе [ConvertTo-SecureString](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6).

В следующих подразделах показано создание в PowerShell подключения к системе управления версиями для GitHub, Azure Repos (Git) и Azure Repos (TFVC).

#### <a name="create-source-control-connection-for-github"></a>Создание подключения к системе управления версиями для GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Создание подключения к системе управления версиями для Azure Repos (Git)

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Создание подключения к системе управления версиями для Azure Repos (TFVC)

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://<accountname>.visualstudio.com/<projectname>/_versionControl -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Разрешения личного маркера доступа (PAT)

Система управления версиями требует наличия некоторых минимальных разрешений для ПАТС. Следующие подразделы содержат минимальные разрешения, необходимые для GitHub и Azure Repos.

##### <a name="minimum-pat-permissions-for-github"></a>Минимальные разрешения PAT для GitHub

В следующей таблице определены минимальные разрешения PAT, необходимые для GitHub. Дополнительные сведения о создании PAT в GitHub см. в разделе [Создание личного маркера доступа для командной строки](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/).

|Область  |Description  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Доступ к фиксации состояния         |
|`repo_deployment`      | Доступ к состоянию развертывания         |
|`public_repo`     | Доступ к общим репозиториям         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Запись перехватчиков репозитория         |
|`read:repo_hook`|Чтение перехватчиков репозитория|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Минимальные разрешения PAT для Azure Repos

В следующем списке определяются минимальные разрешения PAT, необходимые для Azure Repos. Дополнительные сведения о создании PAT в Azure Repos см. в разделе [Аутентификация доступа с помощью персональных маркеров доступа](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate).

| Область  |  Тип доступа  |
|---------| ----------|
| Код      | Чтение  |
| Проект и команда | Чтение |
| Удостоверение | Чтение     |
| Профиль пользователя | Чтение     |
| Рабочие элементы | Чтение    |
| Подключения к службам | Чтение, запрос, управление<sup>1</sup>    |

<sup>1</sup> разрешение на подключения к службам требуется только в том случае, если включена Автосинхронизация.

## <a name="synchronizing"></a>Синхронизация

Выполните следующие действия для синхронизации с системой управления версиями. 

1. Выберите источник из таблицы на странице системы управления версиями. 

2. Нажмите кнопку **Начать синхронизацию**, чтобы запустить процесс. 

3. Просмотрите состояние текущего задания синхронизации или предыдущие, щелкнув вкладку **Синхронизация заданий** . 

4. В раскрывающемся меню **система управления версиями** выберите механизм управления версиями.

    ![Состояние синхронизации](./media/source-control-integration/sync-status.png)

5. Если щелкнуть какое-либо задание, можно просмотреть его выходные данные. Следующий пример иллюстрирует выходные данные задания синхронизации системы управления версиями.

    ```output
    ============================================================================

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

     =========================================================================

    ```

6. Дополнительное ведение журнала можно получить, выбрав **все журналы** на странице Сводка по заданию синхронизации системы управления версиями. Эти дополнительные записи журнала могут помочь в устранении неполадок, которые могут возникнуть при использовании системы управления версиями.

## <a name="disconnecting-source-control"></a>Отключение системы управления версиями

Чтобы отключиться от репозитория системы управления версиями, выполните следующие действия.

1. Откройте **систему управления версиями** в разделе **Параметры учетной записи** в учетной записи службы автоматизации.

2. Выберите механизм управления версиями для удаления. 

3. На странице Сводка системы управления версиями нажмите кнопку **Удалить**.

## <a name="handling-encoding-issues"></a>Обработка проблем кодирования

Если несколько пользователей редактируют модули Runbook в репозитории системы управления версиями с помощью различных редакторов, могут возникнуть проблемы с кодированием. Дополнительные сведения об этой ситуации см. в разделе [распространенные причины проблем кодирования](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues).

## <a name="updating-the-pat"></a>Обновление PAT

В настоящее время невозможно использовать портал Azure для обновления PAT в системе управления версиями. После истечения срока действия PAT или его отмены можно обновить систему управления версиями с помощью нового маркера доступа одним из следующих способов:

* Используйте [REST API](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Используйте командлет [Update-азаутоматионсаурцеконтрол](/powershell/module/az.automation/update-azautomationsourcecontrol) .

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о типах модулей Runbook и их преимуществах и ограничениях см. в статье [типы Runbook службы автоматизации Azure](automation-runbook-types.md).
