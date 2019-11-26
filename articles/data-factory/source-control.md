---
title: Source control in Azure Data Factory
description: Learn how to configure source control in Azure Data Factory
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
author: djpmsft
ms.author: daperlov
ms.reviewer: ''
manager: craigg
ms.openlocfilehash: 264c60c719ffdd94664ae3a85fc67894d14f394d
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484454"
---
# <a name="source-control-in-azure-data-factory"></a>Source control in Azure Data Factory

The Azure Data Factory user interface experience (UX) has two experiences available for visual authoring:

- Разработка с помощью службы фабрики данных
- Author with Azure Repos Git or GitHub integration

> [!NOTE]
> Only authoring directly with the Data Factory service is supported in the Azure Government Cloud.

## <a name="author-directly-with-the-data-factory-service"></a>Разработка с помощью службы фабрики данных

While authoring directly with the Data Factory service, the only way to save changes is via the **Publish All** button. Once clicked, all changes that you made are published directly to the Data Factory service. 

![Режим публикации](media/author-visually/data-factory-publish.png)

Authoring directly with the Data Factory service has the following limitations:

- Служба фабрики данных не содержит репозиторий для хранения сущностей JSON для изменений.
- Служба фабрики данных не оптимизирована для совместной работы или управления версиями.

> [!NOTE]
> Authoring directly with the Data Factory service is disabled in the Azure Data Factory UX when a Git repository is configured. Changes can be made directly to the service via PowerShell or an SDK.

## <a name="author-with-azure-repos-git-integration"></a>Разработка с помощью интеграции Git Azure Repos

Визуальная разработка с помощью интеграции Git Azure Repos поддерживает управление исходным кодом и совместную работу для создания конвейеров фабрики данных. Фабрику данных можно сопоставить с репозиторием организации Git Azure Repos для управления исходным кодом, совместной работы, управления версиями и т. д. В одной организации Git Azure Repos может быть несколько репозиториев, но репозиторий Git Azure Repos может быть связан только с одной фабрикой данных. Если у вас нет организации Azure Repos или репозитория, создайте их, следуя [этим инструкциям](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization-msa-or-work-student).

> [!NOTE]
> Вы можете хранить файлы данных и сценариев в репозитории Git Azure Repos. Но в службу хранилища Azure файлы необходимо отправлять вручную. Конвейер Фабрики данных автоматически не отправляет в службу хранилища Azure файлы данных и сценариев, которые хранятся в репозитории Git Azure Repos.

### <a name="configure-an-azure-repos-git-repository-with-azure-data-factory"></a>Настройка репозитория Git Azure Repos с помощью Фабрики данных Azure

Репозиторий Git Azure Repos можно настроить с помощью фабрики данных двумя способами.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Configuration method 1: Azure Data Factory home page

On the Azure Data Factory home page, select **Set up Code Repository**.

![Настройка репозитория кода Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Метод 2. Пользовательский интерфейс "Работа над холстом"
In the Azure Data Factory UX authoring canvas, select the **Data Factory** drop-down menu, and then select **Set up Code Repository**.

![Настройка параметров репозитория кода для создания в пользовательском интерфейсе](media/author-visually/configure-repo-2.png)

Both methods open the repository settings configuration pane.

![Настройка параметров репозитория кода](media/author-visually/repo-settings.png)

The configuration pane shows the following Azure Repos code repository settings:

| Параметр | Описание | Value |
|:--- |:--- |:--- |
| **Тип репозитория** | Тип репозитория кода Azure Repos.<br/> | Azure DevOps Git or GitHub |
| **Azure Active Directory** | Имя вашего клиента Azure AD. | `<your tenant name>` |
| **Организация Azure Repos** | Название вашей организации Azure Repos. Название организации Azure Repos можно найти по адресу `https://{organization name}.visualstudio.com`. [Войдите в свою организацию Azure Repos](https://www.visualstudio.com/team-services/git/) и получите доступ к профилю Visual Studio, чтобы просмотреть свои репозитории и проекты. | `<your organization name>` |
| **Имя проекта** | Имя проекта Azure Repos. Имя проекта Azure Repos можно найти по адресу `https://{organization name}.visualstudio.com/{project name}`. | `<your Azure Repos project name>` |
| **Имя репозитория** | Имя репозитория кода Azure Repos. Проекты Azure Repos содержат репозитории Git, которые управляют исходным кодом по мере роста проекта. Создайте репозиторий либо используйте репозиторий, который уже имеется в проекте. | `<your Azure Repos code repository name>` |
| **Ветвь совместной работы** | Ветвь вашей совместной работы в Azure Repos, которая используется для публикации. By default, it’s `master`. Измените этот параметр, если нужно опубликовать ресурсы из другой ветви. | `<your collaboration branch name>` |
| **Корневая папка** | Корневая папка в ветви совместной работы Azure Repos. | `<your root folder name>` |
| **Import existing Data Factory resources to repository** (Импорт существующих ресурсов фабрики данных в репозиторий) | Указывает, следует ли импортировать имеющиеся ресурсы фабрики данных из пользовательского интерфейса **Работа над холстом** в репозиторий Git Azure Repos. Установите флажок, чтобы импортировать ресурсы фабрики данных в связанный репозиторий Git в формате JSON. Это действие экспортирует каждый ресурс по отдельности (то есть, связанные службы и наборы данных будут экспортироваться в отдельных файлах JSON). Если этот флажок не установлен, имеющиеся ресурсы не импортируются. | Установлен (по умолчанию) |
| **Branch to import resource into** (Ветвь для импорта ресурсов) | Указывает, в какую ветвь импортируются ресурсы фабрики данных (конвейеры, наборы данных, связанные службы и т.д.). Вы можете выбрать для импорта ресурсов один из следующих вариантов: 1) "Совместная работа"; 2) "Создать"; 3) "Использовать имеющуюся". |  |

> [!NOTE]
> If you are using Microsoft Edge and do not see any values in your Azure DevOps Account dropdown, add https://*.visualstudio.com to the trusted sites list.

### <a name="use-a-different-azure-active-directory-tenant"></a>Использование другого клиента Azure Active Directory

Репозиторий Git Azure Repos можно создать в другом клиенте Azure Active Directory. Чтобы указать другой клиент Azure AD, у вас должны быть права администратора для подписки Azure, которую вы используете.

### <a name="use-your-personal-microsoft-account"></a>Использование личной учетной записи Майкрософт

Чтобы использовать личную учетную запись Майкрософт для интеграции с Git, можно связать личный репозиторий Azure с Active Directory вашей организации.

1. Добавление личной учетной записи Майкрософт в Active Directory вашей организации в качестве гостя. Дополнительные сведения см. в статье [Добавление пользователей службы совместной работы B2B на портале Azure](../active-directory/b2b/add-users-administrator.md).

2. Войдите на портал Azure с помощью личной учетной записи Майкрософт. Перейдите в Active Directory вашей организации.

3. Перейдите к разделу Azure DevOps, где находится личный репозиторий. Выберите репозиторий и подключитесь к Active Directory.

После этих шагов настройки личный репозиторий доступен при настройке интеграции Git в пользовательском интерфейсе Фабрики данных.

Дополнительные сведения о подключении репозитория Azure Active Directory вашей организации см. в статье о [подключении организации Azure DevOps к Azure Active Directory](/azure/devops/organizations/accounts/connect-organization-to-azure-ad).

## <a name="author-with-github-integration"></a>Разработка с помощью интеграции GitHub

Визуальная разработка с помощью интеграции GitHub поддерживает управление исходным кодом и совместную работу для создания конвейеров фабрики данных. Фабрику данных можно связать с репозиторием учетной записи GitHub для управления исходным кодом, совместной работы и управления версиями. В одной учетной записи GitHub может быть несколько репозиториев, но репозиторий GitHub может быть связан только с одной фабрикой данных. Если у вас нет учетной записи GitHub или репозитория, создайте их, следуя  [этим инструкциям](https://github.com/join) .

Интеграция GitHub с Фабрикой данных поддерживает общедоступный репозиторий GitHub ([https://github.com](https://github.com)) и GitHub Enterprise. Вы можете использовать общедоступные и частные репозитории GitHub с Фабрикой данных, если у вас есть разрешения на чтение и запись в репозитории GitHub.

To configure a GitHub repo, you must have administrator permissions for the Azure subscription that you're using.

Уделите 9 минут вашего времени, чтобы просмотреть следующее видео с кратким обзором и демонстрацией этой функции:

> [!VIDEO https://channel9.msdn.com/shows/azure-friday/Azure-Data-Factory-visual-tools-now-integrated-with-GitHub/player]

### <a name="configure-a-github-repository-with-azure-data-factory"></a>Configure a GitHub repository with Azure Data Factory

Репозиторий GitHub можно настроить с помощью фабрики данных двумя способами.

#### <a name="configuration-method-1-azure-data-factory-home-page"></a>Configuration method 1: Azure Data Factory home page

On the Azure Data Factory home page, select **Set up Code Repository**.

![Настройка репозитория кода Azure Repos](media/author-visually/configure-repo.png)

#### <a name="configuration-method-2-ux-authoring-canvas"></a>Метод 2. Пользовательский интерфейс "Работа над холстом"

In the Azure Data Factory UX authoring canvas, select the **Data Factory** drop-down menu, and then select **Set up Code Repository**.

![Настройка параметров репозитория кода для создания в пользовательском интерфейсе](media/author-visually/configure-repo-2.png)

Both methods open the repository settings configuration pane.

![Параметры репозитория GitHub](media/author-visually/github-integration-image2.png)

The configuration pane shows the following GitHub repository settings:

| **Параметр** | **Описание**  | **Значение**  |
|:--- |:--- |:--- |
| **Тип репозитория** | Тип репозитория кода Azure Repos. | GitHub |
| **Use GitHub Enterprise** (Использовать GitHub Enterprise) | Флажок для выбора GitHub Enterprise. | unselected (default) |
| **GitHub Enterprise URL** (URL-адрес GitHub Enterprise) | Корневой URL-адрес GitHub Enterprise. Например, https://github.mydomain.com. Required only if **Use GitHub Enterprise** is selected | `<your GitHub enterprise url>` |                                                           
| **Учетная запись GitHub** | Имя учетной записи GitHub. This name can be found from https:\//github.com/{account name}/{repository name}. При переходе на эту страницу вам предложат ввести учетные данные OAuth GitHub для вашей учетной записи GitHub. | `<your GitHub account name>` |
| **Repository Name**  | Имя репозитория кода GitHub. Учетные записи GitHub содержат репозитории Git для управления исходным кодом. Создайте репозиторий либо используйте уже имеющийся в учетной записи. | `<your repository name>` |
| **Ветвь совместной работы** | Ветвь вашей совместной работы в GitHub, которая используется для публикации. By default, its master. Измените этот параметр, если нужно опубликовать ресурсы из другой ветви. | `<your collaboration branch>` |
| **Корневая папка** | Корневая папка в ветви совместной работы GitHub. |`<your root folder name>` |
| **Import existing Data Factory resources to repository** (Импорт существующих ресурсов фабрики данных в репозиторий) | Specifies whether to import existing data factory resources from the UX authoring canvas into a GitHub repository. Установите флажок, чтобы импортировать ресурсы фабрики данных в связанный репозиторий Git в формате JSON. Это действие экспортирует каждый ресурс по отдельности (то есть, связанные службы и наборы данных будут экспортироваться в отдельных файлах JSON). Если этот флажок не установлен, имеющиеся ресурсы не импортируются. | Установлен (по умолчанию) |
| **Branch to import resource into** (Ветвь для импорта ресурсов) | Указывает, в какую ветвь импортируются ресурсы фабрики данных (конвейеры, наборы данных, связанные службы и т.д.). Вы можете выбрать для импорта ресурсов один из следующих вариантов: 1) "Совместная работа"; 2) "Создать"; 3) "Использовать имеющуюся". |  |

### <a name="known-github-limitations"></a>Known GitHub limitations

- В репозитории GitHub можно хранить файлы данных и сценариев. Но в службу хранилища Azure файлы необходимо отправлять вручную. Конвейер Фабрики данных автоматически не отправляет в службу хранилища Azure файлы данных и сценариев, которые хранятся в репозитории GitHub.

- GitHub Enterprise версии ниже 2.14.0 не работает в браузере Microsoft Edge.

- GitHub integration with the Data Factory visual authoring tools only works in the generally available version of Data Factory.

## <a name="switch-to-a-different-git-repo"></a>Выберите другой репозиторий Git

To switch to a different Git repo, click the **Git Repo Settings** icon in the upper right corner of the Data Factory overview page. Если значок не отображается, очистите кэш локального браузера. Чтобы разорвать связь с текущим репозиторием, выберите значок.

![Git icon](media/author-visually/remove-repo.png)

Once the Repository Settings pane appears, select **Remove Git**. Enter your data factory name and click **confirm** to remove the Git repository associated with your data factory.

![Удаление связи с текущим репозиторием Git](media/author-visually/remove-repo2.png)

After you remove the association with the current repo, you can configure your Git settings to use a different repo and then import existing Data Factory resources to the new repo. 

## <a name="version-control"></a>Управление версиями

Системы управления версиями, также называемые системами _контроля версий_, позволяют разработчикам взаимодействовать с кодом и отслеживать изменения, внесенные в базу кода. Система управления версиями является важным инструментом для проектов с несколькими разработчиками.

### <a name="creating-feature-branches"></a>Creating feature branches

Каждый репозиторий Git Azure Repos, который связан с фабрикой данных, имеет ветвь совместной работы. (`master` — ветвь совместной работы по умолчанию.) Users can also create feature branches by clicking **+ New Branch** in the branch dropdown. Once the new branch pane appears, enter the name of your feature branch.

![Create a new branch](media/author-visually/new-branch.png)

When you are ready to merge the changes from your feature branch to your collaboration branch, click on the branch dropdown and select **Create pull request**. Вы перейдете в Git Azure Repos, где можно инициировать запросы на вытягивание, выполнять проверки кода и объединять изменения в ветви совместной работы. (Значение по умолчанию — `master`.) Публикация в службе фабрики данных разрешена только из ветви совместной работы. 

![Создание нового запроса на вытягивание](media/author-visually/create-pull-request.png)

### <a name="configure-publishing-settings"></a>Настройка параметров публикации

Чтобы настроить ветвь публикации (ветвь, в которой сохраняются шаблоны Resource Manager), добавьте файл `publish_config.json` в корневую папку в ветви совместной работы. Фабрика данных считывает этот файл, ищет поле `publishBranch` и создает новую ветвь (если она отсутствует) с указанным значением. Затем она сохраняет все шаблоны Resource Manager в указанном расположении. Пример.

```json
{
    "publishBranch": "factory/adf_publish"
}
```

Если вы указываете новую ветвь публикации, Фабрика данных не удаляет предыдущую ветвь. If you want to remove the previous publish branch, delete it manually.

> [!NOTE]
> Фабрика данных считывает файл `publish_config.json` только при загрузке фабрики. Если вы уже загрузили фабрику на портал, обновите браузер, чтобы изменения вступили в силу.

### <a name="publish-code-changes"></a>Публикация изменений кода

After you have merged changes to the collaboration branch (`master` is the default), click **Publish** to manually publish your code changes in the master branch to the Data Factory service.

![Публикация изменений в службе фабрики данных](media/author-visually/publish-changes.png)

A side pane will open where you confirm that the publish branch and pending changes are correct. Once you verify your changes, click **OK** to confirm the publish.

![Подтверждение использования правильной ветви публикации](media/author-visually/configure-publish-branch.png)

> [!IMPORTANT]
> Главная ветвь может не соответствовать тому, что развернуто в службе фабрики данных. Главную ветвь *нужно* опубликовать вручную в службе фабрики данных.

## <a name="advantages-of-git-integration"></a>Преимущества интеграции Git

-   **Система управления версиями**. Так как рабочие нагрузки фабрики данных становятся критически важными, вам может понадобиться интегрировать фабрику с Git, чтобы использовать несколько преимуществ системы управления версиями, таких как:
    -   возможность отслеживания и аудита изменений;
    -   возможность отменить изменения, внесение которых привело к появлению ошибок.
-   **Частичное сохранение**. Внося множество изменений в фабрику, вы обнаружите, что в обычном динамическом режиме не следует сохранять изменения как черновик, так как это может привести к нежелаемой потере внесенных изменений в случае сбоя компьютера. При использовании интеграции Git можно продолжить постепенное сохранение изменений и публиковать их в фабрике только при необходимости. Git выступает в качестве промежуточного места для результатов работы, пока вы не выполните все необходимые проверки.
-   **Совместная работа и управление**. Если несколько участников команды выполняют развертывание в одной фабрике, вы можете разрешить им совместно работать друг с другом с помощью процесса проверки кода. Вы также можете настроить фабрику таким образом, чтобы не каждый участник мог выполнять развертывание в нее. Вы можете разрешить вносить изменения через Git всем участникам команды, но только определенным из них присвоить разрешение на публикацию изменений в фабрике.
-   **Отображение изменений**. В режиме Git можно просмотреть все изменения, которые будут опубликованы в фабрике. Будут показаны все ресурсы или сущности, которые были изменены, добавлены или удалены с момента последней публикации в фабрике. Исходя из данных о различиях, можно либо продолжить процесс публикации, либо вернуться и проверить изменения и продолжить публикацию позже.
-   **Более эффективные методы CI/CD**. Если вы используете режим Git, можно настроить конвейер выпуска, чтобы он активировался автоматически после внесения каждого изменения в фабрике разработки. В фабрике можно настроить свойства, доступные в качестве параметров в шаблоне Resource Manager. Может быть полезно сохранить в качестве параметров только требуемый набор параметров, а все остальные параметры жестко закодировать.
-   **Повышенная производительность**. An average factory loads ten times faster in Git mode than in regular LIVE mode, because the resources are downloaded via Git.

## <a name="best-practices-for-git-integration"></a>Рекомендации по интеграции Git

### <a name="permissions"></a>Разрешения

Typically you don’t want every team member to have permissions to update the factory. The following permissions settings are recommended:

*   Всем участникам команды следует предоставить разрешения на чтение фабрики данных.
*   Only a select set of people should be allowed to publish to the factory. To do so, they must have the **Data Factory contributor** role on the factory. For more information on permissions, see [Roles and permissions for Azure Data Factory](concepts-roles-permissions.md).
   
It's recommended to not allow direct check-ins to the collaboration branch. This restriction can help prevent bugs as every check-in will go through a pull request review process described in [Creating feature branches](source-control.md#creating-feature-branches).

### <a name="using-passwords-from-azure-key-vault"></a>Using passwords from Azure Key Vault

its recommended to use Azure Key Vault to store any connection strings or passwords for Data Factory Linked Services. For security reasons, we don’t store any such secret information in Git, so any changes to Linked Services are published immediately to the Azure Data Factory service.

Using Key Vault also makes continuous integration and deployment easier as you will not have to provide these secrets during Resource Manager template deployment.

## <a name="troubleshooting-git-integration"></a>Troubleshooting Git integration

### <a name="stale-publish-branch"></a>Stale publish branch

If the publish branch is out of sync with the master branch and contains out-of-date resources despite a recent publish, try following these steps:

1. Remove your current Git repository
1. Reconfigure Git with the same settings, but make sure **Import existing Data Factory resources to repository** is selected and choose **New branch**
1. Delete all resources from your collaboration branch
1. Create a pull request to merge the changes to the collaboration branch 

## <a name="provide-feedback"></a>Отправить отзыв
Выберите **Обратная связь**, чтобы оставить комментарии о возможностях или сообщить корпорации Майкрософт о проблемах со средством:

![Обратная связь](media/author-visually/provide-feedback.png)

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о мониторинге и управлении конвейерами см. в статье [Отслеживание фабрики данных Azure с помощью программных средств](monitor-programmatically.md).
* To implement continuous integration and deployment, see [Continuous integration and delivery (CI/CD) in Azure Data Factory](continuous-integration-deployment.md).
