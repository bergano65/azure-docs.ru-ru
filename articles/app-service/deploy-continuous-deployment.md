---
title: настройка непрерывного развертывания;
description: Узнайте, как включить CI/CD в службу приложений Azure от GitHub, BitBucket, Azure Repos или другого репо. Выберите конвейер сборки, который соответствует вашим потребностям.
ms.assetid: 6adb5c84-6cf3-424e-a336-c554f23b4000
ms.topic: article
ms.date: 03/20/2020
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 847de2c2c8916558d542473d9b7c80fd5552dbf7
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437237"
---
# <a name="continuous-deployment-to-azure-app-service"></a>Непрерывное развертывание в службе приложений Azure

[Служба приложений Azure](overview.md) позволяет непрерывно развертывать репозитории Репос GitHub, BitBucket и [Azure,](https://azure.microsoft.com/services/devops/repos/) втягивая последние обновления. В этой статье показано, как использовать портал Azure для непрерывного развертывания приложения через службу сборки Kudu или [Azure Pipelines.](https://azure.microsoft.com/services/devops/pipelines/) 

Для получения дополнительной информации об службах управления исходными данными [см. Создать репо (GitHub),] [Создать репо (BitBucket)]или [создать новое репо Git (Azure Repo).]

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

## <a name="authorize-azure-app-service"></a>Авторизация службы приложений Azure 

Чтобы использовать Azure Repos, убедитесь, что организация Azure DevOps связана с подпиской Azure. Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)

Для Bitbucket или GitHub разрешите службе приложений Azure подключиться к репозиторию. Вам нужно только один раз авторизовать сяразить со службой управления исходным сообщением. 

1. На [портале Azure](https://portal.azure.com)и выберите **службы приложений.**

   ![Поиск служб приложений.](media/app-service-continuous-deployment/search-for-app-services.png)

1. Выберите службу приложений, которая требуется развернуть.

   ![Выберите приложение.](media/app-service-continuous-deployment/select-your-app.png)
   
1. На странице приложения выберите **Центр развертывания** в левом меню.
   
1. На странице **Центра развертывания** выберите **GitHub** или **Bitbucket,** а затем выберите **Authorize.** 
   
   ![Выберите службу управления исходом, а затем выберите Авторизацию.](media/app-service-continuous-deployment/github-choose-source.png)
   
1. При необходимости войдите в службу и следуйте запросам авторизации. 

## <a name="enable-continuous-deployment"></a>Включение непрерывного развертывания 

После авторизации службы управления исходными исходами настройте приложение для непрерывного развертывания через встроенный сервер сборки [приложений Kudu](#option-1-kudu-app-service) или через [Azure Pipelines.](#option-2-azure-pipelines) 

### <a name="option-1-kudu-app-service"></a>Вариант 1: Служба приложения Куду

Для непрерывного развертывания с GitHub, Bitbucket или Azure Repos можно использовать встроенный сервер Kudu App Service. 

1. На [портале Azure](https://portal.azure.com)ищите **службы приложений,** а затем выберите службу приложений, которая требуется развернуть. 
   
1. На странице приложения выберите **Центр развертывания** в левом меню.
   
1. Выберите авторизованного поставщика управления исходным движением на странице **Центра развертывания** и выберите **Продолжить**. Для GitHub или Bitbucket вы также можете выбрать **учетную запись Change** для изменения авторизованной учетной записи. 
   
   > [!NOTE]
   > Чтобы использовать Azure Repos, убедитесь, что организация Службы Azure DevOps связана с подпиской Azure. Для получения дополнительной информации [см.](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)
   
1. Для GitHub или Azure Repos на странице **поставщика сборки** выберите **службу сборки службы** **приложений,** а затем выберите Продолжить . Bitbucket всегда использует службу сборки службы приложений.
   
   ![Выберите службу сборки службы приложений, а затем выберите Продолжить.](media/app-service-continuous-deployment/choose-kudu.png)
   
1. На странице **Настройка:**
   
   - Для GitHub отбросьте и выберите **Организацию,** **Репозиторий**и **ветвь,** которые необходимо развертывать непрерывно.
     
     > [!NOTE]
     > Если вы не видите репозиториев, возможно, потребуется авторизация службы приложений Azure в GitHub. Просмотрите репозиторий GitHub и перейдите в приложения **настройки** > **Applications** > **авторизованных приложений OAuth Apps.** Выберите **службу приложений Azure,** а затем выберите **Grant**. Для репозиториев организации вы должны быть владельцем организации для предоставления разрешений.
     
   - Для Bitbucket выберите **команду**Bitbucket, **репозиторий**и **ветвь,** которые необходимо развертывать непрерывно.
     
   - Для репо Azure выберите **организацию Azure DevOps,** **проект,** **репозиторий**и **ветвь,** которые необходимо развертывать непрерывно.
     
     > [!NOTE]
     > Если организация Azure DevOps не указана в списке, убедитесь, что она связана с подпиской Azure. Для получения дополнительной [Set up an Azure DevOps Services account so it can deploy to a web app](https://docs.microsoft.com/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps?view=azure-devops)информации см.
     
1. Выберите **Продолжить**.
   
   ![Заполните репозиторийную информацию, а затем выберите Продолжить.](media/app-service-continuous-deployment/configure-kudu.png)
   
1. После настройки поставщика сборки просмотрите настройки на странице **Резюме,** а затем выберите **finish.**
   
1. Новые коммиты в выбранном репозитории и ветке теперь постоянно развертываются в приложении Службы приложения. Вы можете отслеживать коммиты и развертывания на странице **Центра развертывания.**
   
   ![Отслеживание коммитов и развертывания в Центре развертывания](media/app-service-continuous-deployment/github-finished.png)

### <a name="option-2-azure-pipelines"></a>Вариант 2: Трубопроводы Azure 

Если у вашей учетной записи есть необходимые разрешения, можно настроить конвейеры Azure для непрерывного развертывания из GitHub или Azure Repos. Для получения дополнительной информации о развертывании через конвейеры Azure можно [насайте веб-приложения для служб ы доступа Azure App Services.](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps)

#### <a name="prerequisites"></a>Предварительные требования

Для создания непрерывной доставки с помощью azure Pipelines ваша организация Azure DevOps должна иметь следующие разрешения: 

- Учетная запись Azure должна иметь разрешения на запись в Active Directory Azure и создание службы. 
  
- Ваша учетная запись Azure должна иметь роль **владельца** в подписке Azure.

- Вы должны быть администратором в проекте Azure DevOps, который вы хотите использовать.

#### <a name="github--azure-pipelines"></a>GitHub - Трубопроводы Azure

1. На [портале Azure](https://portal.azure.com)ищите **службы приложений,** а затем выберите службу приложений, которая требуется развернуть. 
   
1. На странице приложения выберите **Центр развертывания** в левом меню.

1. Выберите **GitHub** в качестве поставщика управления исходом на странице **Центра развертывания** и выберите **Продолжить**. Для **GitHub**вы можете выбрать **учетную запись Change** для изменения авторизованной учетной записи.

    ![система управления версиями](media/app-service-continuous-deployment/deployment-center-src-control.png)
   
1. На странице **поставщика сборки** выберите **прокладки azure (Preview),** а затем выберите **Продолжить**.

    ![построить поставщика](media/app-service-continuous-deployment/select-build-provider.png)
   
1. На странице **Настройка** в разделе **Код** выберите **Организацию,** **Репозиторий**и **ветвь,** которые необходимо развертывать непрерывно, и выберите **Продолжить.**
     
     > [!NOTE]
     > Если вы не видите репозиториев, возможно, потребуется авторизация службы приложений Azure в GitHub. Просмотрите репозиторий GitHub и перейдите в приложения **настройки** > **Applications** > **авторизованных приложений OAuth Apps.** Выберите **службу приложений Azure,** а затем выберите **Grant**. Для репозиториев организации вы должны быть владельцем организации для предоставления разрешений.
       
    В разделе **Сборка** укажите организацию Azure DevOps, проект, языковую платформу, которую Azure Pipelines должны использовать для выполнения задач сборки, а затем выберите **Продолжить.**

   ![построить поставщика](media/app-service-continuous-deployment/build-configure.png)

1. После настройки поставщика сборки просмотрите настройки на странице **Резюме,** а затем выберите **finish.**

   ![построить поставщика](media/app-service-continuous-deployment/summary.png)
   
1. Новые коммиты в выбранном репозитории и ветке теперь постоянно развертываются в службе приложений. Вы можете отслеживать коммиты и развертывания на странице **Центра развертывания.**
   
   ![Отслеживание коммитов и развертывания в Центре развертывания](media/app-service-continuous-deployment/github-finished.png)

#### <a name="azure-repos--azure-pipelines"></a>Репо Azure - Трубопроводы Azure

1. На [портале Azure](https://portal.azure.com)ищите **службы приложений,** а затем выберите службу приложений, которая требуется развернуть. 
   
1. На странице приложения выберите **Центр развертывания** в левом меню.

1. Выберите **Azure Repos** в качестве поставщика управления исходными исходами на странице **Центра развертывания** и выберите **Продолжить**.

    ![система управления версиями](media/app-service-continuous-deployment/deployment-center-src-control.png)

1. На странице **поставщика сборки** выберите **прокладки azure (Preview),** а затем выберите **Продолжить**.

    ![система управления версиями](media/app-service-continuous-deployment/azure-pipelines.png)

1. На странице **Настройка** в разделе **Код** выберите **Организацию,** **Репозиторий**и **ветвь,** которые необходимо развертывать непрерывно, и выберите **Продолжить.**

   > [!NOTE]
   > Если существующая организация Azure DevOps не указана в списке, возможно, потребуется связать ее с подпиской Azure. Для получения дополнительной информации [см.](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)

   В разделе **Сборка** укажите организацию Azure DevOps, проект, языковую платформу, которую Azure Pipelines должны использовать для выполнения задач сборки, а затем выберите **Продолжить.**

   ![построить поставщика](media/app-service-continuous-deployment/build-configure.png)

1. После настройки поставщика сборки просмотрите настройки на странице **Резюме,** а затем выберите **finish.**  
     
   ![построить поставщика](media/app-service-continuous-deployment/summary-azure-pipelines.png)

1. Новые коммиты в выбранном репозитории и ветке теперь постоянно развертываются в службе приложений. Вы можете отслеживать коммиты и развертывания на странице **Центра развертывания.**

## <a name="disable-continuous-deployment"></a>Отключение непрерывного развертывания

Чтобы отключить непрерывное развертывание, выберите **отключение** в верхней части страницы **Центра развертывания** приложения.

![Отключение непрерывного развертывания](media/app-service-continuous-deployment/disable.png)

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="use-unsupported-repos"></a>Использование неподдерживаемых РЕПО

Для приложений Windows можно вручную настроить непрерывное развертывание из облачного хранилища Git или Mercurial, который портал не поддерживает непосредственно, например [GitLab.](https://gitlab.com/) Вы делаете это, выбирая внешний ящик на странице **Центра развертывания.** Для получения дополнительной информации [см.](https://github.com/projectkudu/kudu/wiki/Continuous-deployment#setting-up-continuous-deployment-using-manual-steps)

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Исследуйте общие проблемы с непрерывным развертыванием](https://github.com/projectkudu/kudu/wiki/Investigating-continuous-deployment)
* [Используйте Azure PowerShell](/powershell/azureps-cmdlets-docs)
* [Документация по Git](https://git-scm.com/documentation)
* [Project Kudu](https://github.com/projectkudu/kudu/wiki)

[Create a repo (GitHub)]: https://help.github.com/articles/create-a-repo (Создание репозитория GitHub)
[Create a repo (BitBucket)]: https://confluence.atlassian.com/get-started-with-bitbucket/create-a-repository-861178559.html (Создание репозитория BitBucket)
[Создание нового репо Git (Azure Repos)]: /azure/devops/repos/git/creatingrepo
