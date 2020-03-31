---
title: Интеграция системы управления версиями в службе автоматизации Azure
description: В этой статье описывается интеграция системы управления версиями с GitHub в службе автоматизации Azure.
services: automation
ms.subservice: process-automation
ms.date: 12/10/2019
ms.topic: conceptual
ms.openlocfilehash: 166902978d1641458f18aeee6269c8d819e85233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132923"
---
# <a name="source-control-integration-in-azure-automation"></a>Интеграция системы управления версиями в службе автоматизации Azure

 Интеграция управления исходными исходами в Azure Automation поддерживает синхронизацию с однонаправленным управлением из репозитория управления исходным управлением. Управление исходным элементом позволяет обновлять свои runbooks в учетной записи Automation в актуальном состоянии со скриптами в репозитории управления исходным управлением GitHub или Azure Repos. Эта функция позволяет легко продвигать код, который был протестирован в среде разработки, к вашей учетной записи автоматизации производства.
 
 Интеграция управления исходным элементом позволяет легко сотрудничать с командой, отслеживать изменения и откатываться к более ранним версиям ваших runbooks. Например, управление исходным управлением позволяет синхронизировать различные ветви управления исходными данными с учетными записями автоматизации разработки, тестирования и производства. 

>[!NOTE]
>Эта статья была изменена и теперь содержит сведения о новом модуле Az для Azure PowerShell. Вы по-прежнему можете использовать модуль AzureRM, исправления ошибок для которого будут продолжать выпускаться как минимум до декабря 2020 г. Дополнительные сведения о совместимости модуля Az с AzureRM см. в статье [Introducing the new Azure PowerShell Az module](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0) (Знакомство с новым модулем Az для Azure PowerShell). Для инструкций по установке модуля Az на гибридном Runbook Worker [см.](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0) Для учетной записи Автоматизация вы можете обновить свои модули до последней версии, используя [как обновить модули Azure PowerShell в Azure Automation.](automation-update-azure-modules.md)

## <a name="source-control-types"></a>Типы управления исходом

Azure Automation поддерживает три типа управления исходными источниками:

* GitHub
* Лазурный репос (Git)
* Лазурный РЕПО (TFVC)

## <a name="prerequisites"></a>Предварительные требования

* Репозиторий управления исходным элементом (GitHub или Azure Repos)
* [Запуск как учетная запись](manage-runas-account.md)
* [Последние модули Azure](automation-update-azure-modules.md) в вашей учетной записи `Az.Accounts` `AzureRM.Profile`автоматизации, включая модуль (эквивалент модуля Az)

> [!NOTE]
> Задания синхронизации управления исходным управлением работают в учетной записи автоматизации пользователя и выставляются с той же скоростью, что и другие задания автоматизации.

## <a name="configuring-source-control"></a>Настройка управления исходом

В этом разделе рассказывается о том, как настроить элемент управления исходом для учетной записи Automation. Вы можете использовать либо портал Azure, либо PowerShell.

### <a name="configure-source-control-in-azure-portal"></a>Настройка управления исходом на портале Azure

Используйте эту процедуру для настройки управления исходным ресурсом с помощью портала Azure.

1. В учетной записи Автоматизация выберите **Управление исходом** и нажмите **Добавить**.

    ![Выбор системы управления версиями](./media/source-control-integration/select-source-control.png)

2. Выберите **тип управления исходом,** затем нажмите **Authenticate**. 

3. Окно браузера открывается и подсказывает вам войти в систему. Следите за запросами для завершения проверки подлинности.

4. На странице Сводки управления исходным управлением используйте поля для заполнения свойств управления исходом, определенных ниже. По завершении щелкните **Сохранить**. 

    |Свойство  |Описание  |
    |---------|---------|
    |Имя системы управления версиями     | Дружественное название для управления исходным источником. Это имя должно содержать только буквы и цифры.        |
    |Тип системы управления версиями     | Тип механизма управления исходом. Доступные параметры:</br> - GitHub</br>- Лазурный репос (Git)</br> - Лазурный РЕПО (TFVC)        |
    |Хранилище     | Название репозитория или проекта. Извлекаются первые 200 репозиторий. Чтобы найти репозиторий, введите имя в поле и нажмите **Поиск на GitHub**.|
    |Ветвь     | Ветвь, из которой можно вытащить исходные файлы. Таргетинг ветки недоступен для типа управления исходом TFVC.          |
    |Путь к папке     | Folder, содержащий руны для синхронизации, например, **/Runbooks**. Синхронизируются только руны в указанной папке. Рекурсия не поддерживается.        |
    |Автоматическая синхронизация<sup>1</sup>     | Установка, которая включает или выключает автоматическую синхронизацию при совершении коммита в репозитории управления исходным источником.        |
    |Публикация модуля Runbook     | Настройка On, если runbooks автоматически публикуется после синхронизации с исходным управлением, и Off в противном случае.           |
    |Описание     | Текст с указанием дополнительных деталей о элементе управления исходом.        |

    <sup>1</sup> Для включения автоматической синхронизации при настройке интеграции управления исходным ресурсом с Azure Repos необходимо быть администратором проекта.

   ![Сводка по системе управления версиями](./media/source-control-integration/source-control-summary.png)

> [!NOTE]
> Логин для репозитория управления исходным источником может отличаться от входа для портала Azure. Убедитесь, что при настройке элемента управления исходным источником вы вошли в систему с правильной учетной записью для репозитория управления исходным управлением. Если есть сомнения, откройте новую вкладку в браузере, выйдите из **dev.azure.com,** **visualstudio.com**или **github.com,** и попробуйте повторно подключиться к управлению исходным кодом.

### <a name="configure-source-control-in-powershell"></a>Настройка управления исходом в PowerShell

Вы также можете использовать PowerShell для настройки управления исходным элементом в Azure Automation. Для использования cmdlets PowerShell для этой операции необходим токен для личного доступа (PAT). Для создания исходного соединения используйте cmdlet [New-AzAutomationSourceControl.](https://docs.microsoft.com/powershell/module/az.automation/new-azautomationsourcecontrol?view=azps-3.5.0
) Это cmdlet требует надежной строки для PAT. Чтобы узнать, как создать безопасную строку, [см.](/powershell/module/microsoft.powershell.security/convertto-securestring?view=powershell-6)

Следующие подразделы иллюстрируют создание PowerShell соединения управления исходным элементом для GitHub, Azure Repos (Git) и Azure Repos (TFVC). 

#### <a name="create-source-control-connection-for-github"></a>Создание подключения к управлению исходом для GitHub

```powershell-interactive
New-AzAutomationSourceControl -Name SCGitHub -RepoUrl https://github.com/<accountname>/<reponame>.git -SourceType GitHub -FolderPath "/MyRunbooks" -Branch master -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName>
```

#### <a name="create-source-control-connection-for-azure-repos-git"></a>Создание соединения управления исходными исходами для Azure Repos (Git)

> [!NOTE]
> Azure Repos (Git) использует URL-адрес, который получает доступ **к dev.azure.com** вместо **visualstudio.com,** используемый в более ранних форматах. Старый формат `https://<accountname>.visualstudio.com/<projectname>/_git/<repositoryname>` URL устаревает, но все еще поддерживается. Предпочтение относясь новому формату.


```powershell-interactive
New-AzAutomationSourceControl -Name SCReposGit -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoGit -AccessToken <secureStringofPAT> -Branch master -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="create-source-control-connection-for-azure-repos-tfvc"></a>Создание соединения управления исходными источниками для Репоза Azure (TFVC)

> [!NOTE]
> Azure Repos (TFVC) использует URL-адрес, который получает доступ к **dev.azure.com** вместо **visualstudio.com,** используемый в более ранних форматах. Старый формат `https://<accountname>.visualstudio.com/<projectname>/_versionControl` URL устаревает, но все еще поддерживается. Предпочтение относясь новому формату.

```powershell-interactive
New-AzAutomationSourceControl -Name SCReposTFVC -RepoUrl https://dev.azure.com/<accountname>/<adoprojectname>/_git/<repositoryname> -SourceType VsoTfvc -AccessToken <secureStringofPAT> -ResourceGroupName <ResourceGroupName> -AutomationAccountName <AutomationAccountName> -FolderPath "/Runbooks"
```

#### <a name="personal-access-token-pat-permissions"></a>Разрешения токенов личного доступа (PAT)

Управление исходом требует некоторых минимальных разрешений для PAT. Следующие подразделы содержат минимальные разрешения, необходимые для GitHub и Azure Repos.

##### <a name="minimum-pat-permissions-for-github"></a>Минимальные разрешения PAT для GitHub

В следующей таблице определяются минимальные разрешения PAT, необходимые для GitHub. Для получения дополнительной информации о создании PAT в GitHub [см.](https://help.github.com/articles/creating-a-personal-access-token-for-the-command-line/)

|Область  |Описание  |
|---------|---------|
|**`repo`**     |         |
|`repo:status`     | Доступ к фиксации состояния         |
|`repo_deployment`      | Доступ к состоянию развертывания         |
|`public_repo`     | Доступ к общим репозиториям         |
|**`admin:repo_hook`**     |         |
|`write:repo_hook`     | Запись перехватчиков репозитория         |
|`read:repo_hook`|Чтение перехватчиков репозитория|

##### <a name="minimum-pat-permissions-for-azure-repos"></a>Минимальные разрешения PAT для Azure Repos

Следующий список определяет минимальные разрешения PAT, необходимые для репо Azure. Для получения дополнительной информации о создании PAT [Authenticate access with personal access tokens](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate)в Azure Repos см.

| Область  |  Тип доступа  |
|---------| ----------|
| `Code`      | Чтение  |
| `Project and team` | Чтение |
| `Identity` | Чтение     |
| `User profile` | Чтение     |
| `Work items` | Чтение    |
| `Service connections` | Читать, закавыкать, управлять<sup>1</sup>    |

<sup>1</sup> `Service connections` Разрешение требуется только в том случае, если вы включили автосину.

## <a name="synchronizing"></a>Синхронизация

Выполните следующие действия, чтобы синхронизировать с исходным управлением. 

1. Выберите источник из таблицы на странице управления Исходом. 

2. Нажмите кнопку **Начать синхронизацию**, чтобы запустить процесс. 

3. Просмотр текущего задания синхронизации или предыдущих, нажав на вкладку **задания Sync.** 

4. В меню выпадения **исходного элемента** выберите механизм управления исходным ресурсом.

    ![Состояние синхронизации](./media/source-control-integration/sync-status.png)

5. Если щелкнуть какое-либо задание, можно просмотреть его выходные данные. Следующий пример иллюстрирует выходные данные задания синхронизации системы управления версиями.

    ```output
    ===================================================================

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

    ==================================================================

    ```

6. Дополнительная регистрация доступна, выбрав **все журналы** на странице Резюме работы управления исходным управлением. Эти дополнительные записи журнала могут помочь вам устранить проблемы, которые могут возникнуть при использовании управления исходным источником.

## <a name="disconnecting-source-control"></a>Отключение системы управления версиями

Отключение от репозитория управления исходным источником:

1. Управление открытым **исходным кодом** под **настройками учетной записи** в учетной записи автоматизации.

2. Выберите механизм управления исходом для удаления. 

3. На странице Сводка системы управления версиями щелкните **Удалить**.

## <a name="handling-encoding-issues"></a>Обработка проблем кодирования

Если несколько человек редактируют runbooks в репозитории управления исходным кодом с помощью различных редакторов, могут возникнуть проблемы с кодированием. Чтобы узнать больше об этой ситуации, [см.](/powershell/scripting/components/vscode/understanding-file-encoding#common-causes-of-encoding-issues)

## <a name="updating-the-pat"></a>Обновление PAT

В настоящее время вы не можете использовать портал Azure для обновления PAT в исходном элементе. После истечения срока действия PAT или отзыва ею можно обновить элемент управления исходом с помощью нового токена доступа одним из следующих способов:

* Используйте [REST API](https://docs.microsoft.com/rest/api/automation/sourcecontrol/update).
* Используйте [обновление-AzAutomationSourceControl](/powershell/module/az.automation/update-azautomationsourcecontrol) cmdlet.

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о типах runbook [Azure Automation runbook types](automation-runbook-types.md)и их преимуществах и ограничениях, см.
