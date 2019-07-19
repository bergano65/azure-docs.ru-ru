---
title: Развертывание из локального репозитория Git — Служба приложений Azure
description: Узнайте, как включить локальное развертывание репозитория Git в службе приложений Azure.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2019
ms.author: cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 4b2934c8b93ffb247661886cb2791c0719996aeb
ms.sourcegitcommit: a8b638322d494739f7463db4f0ea465496c689c6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/17/2019
ms.locfileid: "68297185"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Развертывание локального репозитория Git в службе приложений Azure

В этом пошаговом руководство показано, как развернуть приложение в [службе приложений Azure](overview.md) из репозитория Git на локальном компьютере.

## <a name="prerequisites"></a>предварительные требования

Выполните следующие шаги для изучения данного руководства.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Установка Git](https://www.git-scm.com/downloads).
  
- Создать локальный репозиторий Git с кодом, который вы хотите развернуть. Чтобы скачать пример репозитория, выполните следующую команду в локальном окне терминала:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Развертывание с помощью сервера сборки KUDU

Самый простой способ включить локальное развертывание Git для приложения с сервером сборки службы приложений KUDU — использовать Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Настойка пользователя развертывания

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Получение URL-адреса развертывания

Чтобы получить URL-адрес для включения локального развертывания Git для существующего приложения, выполните [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) в Cloud Shell. Замените \<имя приложения > и \<имя группы > именем приложения и его группой ресурсов Azure.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

Чтобы создать приложение с поддержкой Git, выполните команду [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) в Cloud Shell `--deployment-local-git` с параметром. Замените \<имя приложения >, \<имя группы > и \<имя плана > именами для нового приложения Git, группы ресурсов Azure и плана службы приложений Azure.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Любая команда возвращает URL-адрес следующего `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`вида:. Используйте этот URL-адрес для развертывания приложения на следующем шаге.

Вместо использования этого URL-адреса уровня учетной записи можно также включить локальный Git с помощью учетных данных на уровне приложения. Служба приложений Azure автоматически создает эти учетные данные для каждого приложения. 

Получите учетные данные приложения, выполнив следующую команду в Cloud Shell. Замените \<имя приложения > и \<имя группы > именем своего приложения и именем группы ресурсов Azure.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Используйте URL-адрес, который возвращает, чтобы развернуть приложение на следующем шаге.

### <a name="deploy-the-web-app"></a>Развертывание веб-приложения

1. Откройте локальное окно терминала в локальном репозитории Git и добавьте Azure Remote. В следующей команде замените \<URL-адрес > на пользовательский URL-адрес развертывания или URL-адрес конкретного приложения, полученный на предыдущем шаге.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Отправка в Azure Remote с помощью `git push azure master`. 
   
1. В окне **диспетчера учетных данных Git** введите [пароль пользователя для развертывания](#configure-a-deployment-user), а не пароль для входа в Azure.
   
1. Просмотрите выходные данные. Вы можете увидеть автоматизацию среды выполнения, например MSBuild для ASP.NET, `npm install` для Node. js и `pip install` для Python. 
   
1. Перейдите к приложению в портал Azure, чтобы убедиться, что содержимое развернуто.

## <a name="deploy-with-azure-pipelines-builds"></a>Развертывание с помощью сборок Azure Pipelines

Если у вашей учетной записи есть необходимые разрешения, можно настроить Azure Pipelines (Предварительная версия), чтобы включить локальное развертывание Git для приложения. 

- Учетная запись Azure должна иметь разрешения на запись в Azure Active Directory и создание службы. 
  
- Ваша учетная запись Azure должна иметь роль **владельца** в подписке Azure.

- Вы должны быть администратором в проекте Azure DevOps, который вы хотите использовать.

Чтобы включить локальное развертывание Git для приложения с Azure Pipelines (Предварительная версия):

1. Перейдите на страницу приложения службы приложений Azure в [портал Azure](https://portal.azure.com)и выберите **центр развертывания** в меню слева.
   
1. На странице **центр развертывания** выберите **локальный Git**, а затем нажмите кнопку **продолжить**. 
   
   ![Выберите локальный Git и нажмите кнопку продолжить.](media/app-service-deploy-local-git/portal-enable.png)
   
1. На странице **поставщик сборки** выберите **Azure pipelines (Предварительная версия)** и нажмите кнопку **продолжить**. 
   
   ![Выберите Azure Pipelines (Предварительная версия) и нажмите кнопку продолжить.](media/app-service-deploy-local-git/pipeline-builds.png)

1. На странице **Настройка** Настройте новую организацию Azure DevOps или укажите существующую организацию, а затем нажмите кнопку **продолжить**.
   
   > [!NOTE]
   > Если существующая организация Azure DevOps отсутствует в списке, может потребоваться связать ее с подпиской Azure. Дополнительные сведения см. в разделе [Определение конвейера освобождения компакт-диска](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. В зависимости от ценовой категории плана службы приложений может отобразиться страница **развертывание на промежуточном** [уровне](https://azure.microsoft.com/pricing/details/app-service/plans/). Выберите, следует ли [включить слоты развертывания](deploy-staging-slots.md), а затем нажмите кнопку **продолжить**.
   
1. На странице **Сводка** проверьте параметры и нажмите кнопку **Готово**.
   
1. Когда конвейер Azure будет готов, скопируйте URL-адрес репозитория Git со страницы **центра развертывания** , чтобы использовать его на следующем шаге. 
   
   ![Скопируйте URL-адрес репозитория Git](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. В локальном окне терминала добавьте удаленное хранилище Azure в локальный репозиторий Git. В команде замените \<URL-адрес > URL-адресом репозитория Git, полученным на предыдущем шаге.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Отправка в Azure Remote с помощью `git push azure master`. 
   
1. На странице **диспетчера учетных данных Git** выполните вход с помощью имени пользователя VisualStudio.com. Другие методы проверки подлинности см. в разделе [Azure DevOps Services проверка](/vsts/git/auth-overview?view=vsts)подлинности.
   
1. После завершения развертывания просмотрите ход выполнения сборки в `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`и ход выполнения развертывания в. `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`
   
1. Перейдите к приложению в портал Azure, чтобы убедиться, что содержимое развернуто.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Устранение неполадок при развертывании

При использовании Git для публикации в приложении службы приложений в Azure могут отобразиться следующие распространенные сообщения об ошибках:

|Сообщение|Причина:|Способы устранения:
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Приложение не работает.|запуск приложения на портале Azure. Развертывание Git недоступно, если веб-приложение остановлено.|
|`Couldn't resolve host 'hostname'`|Неверные сведения об адресе удаленного ресурса "Azure".|используйте команду `git remote -v`, чтобы вывести список всех удаленных репозиториев с соответствующими URL-адресами. Проверьте правильность URL-адреса удаленного репозитория "azure". При необходимости удалите и повторно создайте этот удаленный репозиторий, используя правильный URL-адрес.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Вы не указали ветвь во `git push`время или не `push.default` установили значение в `.gitconfig`.|Выполните `git push` команду еще раз, указав главную ветвь `git push azure master`:.|
|`src refspec [branchname] does not match any.`|Предпринята попытка принудительной отправки в ветвь, отличную от главной, в удаленной службе Azure.|Выполните `git push` команду еще раз, указав главную ветвь `git push azure master`:.|
|`RPC failed; result=22, HTTP code = 5xx.`|эта ошибка может возникнуть при попытке отправить большой репозиторий Git по протоколу HTTPS.|Измените конфигурацию Git на локальном компьютере, чтобы увеличить ее `postBuffer` размер. Например, `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Вы развернули приложение Node. js с файлом _Package. JSON_ , который указывает дополнительные необходимые модули.|Проверьте сообщения `npm ERR!` об ошибках до этой ошибки для получения дополнительных сведений о сбое. Ниже приведены известные причины этой ошибки и соответствующие `npm ERR!` сообщения.<br /><br />**Неправильный формат файла Package. JSON**:`npm ERR! Couldn't read dependencies.`<br /><br />**Собственный модуль не имеет двоичного распространения для Windows**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />или диспетчер конфигурации служб <br />' NPM ERR! [modulename@version] предустановка: \маке || гмаке\`|

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Документация по проекту Kudu](https://github.com/projectkudu/kudu/wiki)
- [Непрерывное развертывание в службе приложений Azure](deploy-continuous-deployment.md)
- [Пример. Создание веб-приложения и развертывание кода из локального репозитория Git (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Пример. Создание веб-приложения и развертывание кода из локального репозитория Git (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
