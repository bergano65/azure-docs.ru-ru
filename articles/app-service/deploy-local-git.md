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
ms.author: dariagrigoriu;cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: fa961e43408fed014be2266c14c7972d39435b97
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67836914"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Развертывание локального репозитория Git в службе приложений Azure

В этом практическом руководстве показано, как развернуть приложение в [службе приложений Azure](overview.md) из репозитория Git на локальном компьютере.

## <a name="prerequisites"></a>предварительные требования

Выполните следующие шаги для изучения данного руководства.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Установка Git](https://www.git-scm.com/downloads).
  
- Есть локальный репозиторий Git с кодом, который вы хотите развернуть. Чтобы скачать пример репозитория, выполните следующую команду в локальном окне терминала:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Развертывание с помощью сервер сборки Kudu

Чтобы включить локальное развертывание Git для приложения с помощью сервер сборки Kudu службы приложений проще всего использовать Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Настойка пользователя развертывания

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Получить URL-адрес развертывания

Чтобы получить URL-адрес, чтобы включить локальное развертывание Git для существующего приложения, выполните [ `az webapp deployment source config-local-git` ](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) в Cloud Shell. Замените \<имя_приложения > и \<имя группы > с именами, приложения и его группу ресурсов Azure.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```

Или, чтобы создать новое приложение с поддержкой Git, выполните [ `az webapp create` ](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) в Cloud Shell с `--deployment-local-git` параметра. Замените \<имя_приложения >, \<имя группы >, и \<плана name > с именами для нового приложения Git, соответствующей группы ресурсов Azure и его план службы приложений Azure.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Либо команда возвращает URL-адрес, например: `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`. Используйте этот URL-адрес для развертывания приложения на следующем шаге.

Вместо этого URL-адреса уровня учетной записи, вы можете также включить локального репозитория Git с помощью учетных данных на уровне приложения. Служба приложений Azure автоматически создает эти учетные данные для каждого приложения. 

Получение учетных данных приложения, выполнив следующую команду в Cloud Shell. Замените \<имя_приложения > и \<имя группы > с именем вашего приложения и имя группы ресурсов Azure.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Используйте URL-адрес, который возвращает для развертывания приложения на следующем шаге.

### <a name="deploy-the-web-app"></a>Развертывание веб-приложения

1. Откройте в окне терминала на локальном в локальном репозитории Git и добавьте удаленное Azure. В следующей команде замените \<URL-адрес > с URL-адрес развертывания для конкретного пользователя или URL-адрес конкретного приложения, полученный из предыдущего шага.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Удаленную службу приложений Azure с помощью `git push azure master`. 
   
1. В **Git Credential Manager** окно, введите ваш [пароль пользователя развертывания](#configure-a-deployment-user), не Azure входа пароль.
   
1. Просмотрите выходные данные. Среда выполнения автоматизации, например MSBuild для ASP.NET, может появиться `npm install` для Node.js, и `pip install` для Python. 
   
1. Перейдите к приложению на портале Azure, чтобы убедиться, что содержимое развернуто.

## <a name="deploy-with-azure-pipelines-builds"></a>Развертывание с помощью Azure конвейеры сборки

Если у вас есть необходимые разрешения, можно настроить конвейеры Azure (Предварительная версия), чтобы включить локальное развертывание Git для вашего приложения. 

- Учетная запись Azure, необходимо иметь разрешения на запись в Azure Active Directory и создать службу. 
  
- Учетная запись Azure, необходимо иметь **владельца** роли в подписке Azure.

- Вы должны обладать правами администратора в проект Azure DevOps, который вы хотите использовать.

Чтобы включить локальное развертывание Git для приложения с помощью конвейеров Azure (Предварительная версия):

1. Перейдите на страницу приложения службы приложений Azure в [портала Azure](https://portal.azure.com)и выберите **центр развертывания** в меню слева.
   
1. На **центр развертывания** выберите **локального репозитория Git**, а затем выберите **Продолжить**. 
   
   ![Выберите локальный репозиторий Git, а затем выберите Продолжить](media/app-service-deploy-local-git/portal-enable.png)
   
1. На **поставщик построения** выберите **конвейеры Azure (Предварительная версия)** , а затем выберите **Продолжить**. 
   
   ![Выберите конвейеры Azure (Предварительная версия), а затем выберите продолжить.](media/app-service-deploy-local-git/pipeline-builds.png)

1. На **Настройка** странице, настроить новую организацию Azure DevOps, или указать существующую организацию и выберите **Продолжить**.
   
   > [!NOTE]
   > Если нет в списке существующих организации DevOps в Azure, может потребоваться связать ее с подпиской Azure. Дополнительные сведения см. в разделе [определения конвейера выпуска компакт-ДИСК](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd).
   
1. В зависимости от плана службы приложений [ценовой](https://azure.microsoft.com/pricing/details/app-service/plans/), может появиться **развертывание в промежуточной** страницы. Выберите необходимость [включить слотов развертывания](deploy-staging-slots.md), а затем выберите **Продолжить**.
   
1. На **Сводка** странице, проверьте параметры, а затем выберите **Готово**.
   
1. Когда конвейер Azure будет готово, скопируйте URL-адрес репозитория Git из **центр развертывания** страница, используемая на следующем шаге. 
   
   ![Скопируйте URL-адрес репозитория Git](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. В локальном окне терминала добавьте удаленный Azure в локальный репозиторий Git. В команде замените \<URL-адрес > с URL-адрес репозитория Git, полученным на предыдущем шаге.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Удаленную службу приложений Azure с помощью `git push azure master`. 
   
1. На **Git Credential Manager** странице, войдите с помощью имени пользователя и visualstudio.com. Другие методы проверки подлинности, см. в разделе [обзор проверки подлинности служб Azure DevOps](/vsts/git/auth-overview?view=vsts).
   
1. После завершения развертывания, просмотрите ход компоновки в `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`и ход выполнения развертывания в `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`.
   
1. Перейдите к приложению на портале Azure, чтобы убедиться, что содержимое развернуто.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Устранение неполадок с развертыванием

Возможны следующие распространенные сообщения об ошибках при использовании Git для публикации в приложении службы приложений в Azure.

|Сообщение|Причина:|Способы устранения:
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Приложение не будет работать.|запуск приложения на портале Azure. Развертывание Git недоступно при остановке веб-приложения.|
|`Couldn't resolve host 'hostname'`|Сведения об адресах «Azure» удаленный неправильный.|используйте команду `git remote -v`, чтобы вывести список всех удаленных репозиториев с соответствующими URL-адресами. Проверьте правильность URL-адреса удаленного репозитория "azure". При необходимости удалите и повторно создайте этот удаленный репозиторий, используя правильный URL-адрес.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Не указана ветвь при `git push`, или вы не задали `push.default` значение в `.gitconfig`.|Запустите `git push` еще раз, указав в главную ветвь: `git push azure master`.|
|`src refspec [branchname] does not match any.`|Предпринята попытка в удаленную ветвь, отличную от главной в «azure».|Запустите `git push` еще раз, указав в главную ветвь: `git push azure master`.|
|`RPC failed; result=22, HTTP code = 5xx.`|эта ошибка может возникнуть при попытке отправить большой репозиторий Git по протоколу HTTPS.|Измените конфигурацию git на локальном компьютере, чтобы сделать `postBuffer` больше. Например, `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Вы развернули приложение Node.js с _package.json_ файл, в котором указаны дополнительные необходимые модули.|Просмотрите `npm ERR!` сообщения об ошибках до этой ошибки дополнительный контекст для данного сбоя. Ниже перечислены известные причины этой ошибки, и соответствующий `npm ERR!` сообщений:<br /><br />**Файл имеет неправильный формат package.json**: `npm ERR! Couldn't read dependencies.`<br /><br />**Собственный модуль не имеющий двоичного дистрибутива для Windows**:<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />или диспетчер конфигурации служб <br />"npm ERR! [modulename@version] предварительной установки: \make || gmake\`|

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Документация по проекту Kudu](https://github.com/projectkudu/kudu/wiki)
- [Непрерывное развертывание в службе приложений Azure](deploy-continuous-deployment.md)
- [Пример. Создание веб-приложения и развертывание кода из локального репозитория Git (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Пример. Создание веб-приложения и развертывание кода из локального репозитория Git (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
