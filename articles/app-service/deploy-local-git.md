---
title: развертывание из локального репозитория Git
description: Узнайте, как включить локальное развертывание репозитория Git в службе приложений Azure. Один из простейших способов развертывания кода из локальной машины.
ms.assetid: ac50a623-c4b8-4dfd-96b2-a09420770063
ms.topic: article
ms.date: 06/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: efe4c07a6231e0b2c95b049db056a4e5d055db98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77152998"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Локальное развертывание Git в службе приложений Azure

Это руководство показывает, как развернуть приложение в [службу приложений Azure](overview.md) из репозитория Git на локальном компьютере.

## <a name="prerequisites"></a>Предварительные требования

Выполните следующие шаги для изучения данного руководства.

- [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]
  
- [Установите Git](https://www.git-scm.com/downloads).
  
- Иметь локальный репозиторий Git с кодом, который вы хотите развернуть. Чтобы загрузить репозиторий образца, запустите следующую команду в окне локального терминала:
  
  ```bash
  git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
  ```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-build-server"></a>Развертывание с сервером сборки Kudu

Самый простой способ включить локальное развертывание Git для вашего приложения с помощью сервера сборки приложений Kudu — использовать Azure Cloud Shell. 

### <a name="configure-a-deployment-user"></a>Настойка пользователя развертывания

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="get-the-deployment-url"></a>Получить URL-адрес развертывания

Чтобы получить URL-адрес для включения локального развертывания [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) Git для существующего приложения, запустите в облачной оболочке. Замените \<> и \<групповое название приложения> именами вашего приложения и группы ресурсов Azure.

```azurecli-interactive
az webapp deployment source config-local-git --name <app-name> --resource-group <group-name>
```
> [!NOTE]
> Если вы используете linux-план обслуживания приложений, вам нужно добавить этот параметр: --беговое python'3.7


Или создать новое приложение с поддержкой [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) Git, запустите `--deployment-local-git` в облачной оболочке с параметром. Замените \<>, \<> группы и \<> с именем плана, с именами для нового приложения Git, его группой ресурсов Azure и планом обслуживания приложений Azure.

```azurecli-interactive
az webapp create --name <app-name> --resource-group <group-name> --plan <plan-name> --deployment-local-git
```

Любая команда возвращает `https://<deployment-username>@<app-name>.scm.azurewebsites.net/<app-name>.git`URL-адрес типа: . Используйте этот URL-адрес для развертывания приложения на следующем этапе.

Вместо использования этого URL-адреса на уровне учетной записи можно также включить локальный Git, используя учетные данные на уровне приложений. Служба приложений Azure автоматически генерирует эти учетные данные для каждого приложения. 

Получите учетные данные приложения, запустив следующую команду в облачной оболочке. Замените \<> и \<групповое имя приложения и> название группы на имя приложения и имя группы ресурсов Azure.

```azurecli-interactive
az webapp deployment list-publishing-credentials --name <app-name> --resource-group <group-name> --query scmUri --output tsv
```

Используйте URL-адрес, который возвращается для развертывания приложения на следующем этапе.

### <a name="deploy-the-web-app"></a>Развертывание веб-приложения

1. Откройте локальное окно терминала в локальное хранилище Git и добавьте пульт дистанционного управления Azure. В следующей команде \<замените URL-> URL-адресом URL-адреса или URL-адресом приложения, который был получен на предыдущем этапе.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Нажмите на пульт дистанционного управления Azure с помощью `git push azure master`. 
   
1. В окне **Git Credential Manager** введите [пароль пользователя развертывания,](#configure-a-deployment-user)а не пароль входа в Azure.
   
1. Просмотрите выходные данные. Автоматизацию времени выполнения, например MSBuild для `npm install` ASP.NET, для Node.js и `pip install` Для Python, можно увидеть. 
   
1. Просмотрите приложение на портале Azure, чтобы убедиться, что содержимое развернуто.

## <a name="deploy-with-azure-pipelines-builds"></a>Развертывание с помощью трубопроводов Azure

Если у учетной записи есть необходимые разрешения, можно настроить azure Pipelines (Preview) для включения локального развертывания Git для вашего приложения. 

- Учетная запись Azure должна иметь разрешения на запись в Active Directory Azure и создание службы. 
  
- Ваша учетная запись Azure должна иметь роль **владельца** в подписке Azure.

- Вы должны быть администратором в проекте Azure DevOps, который вы хотите использовать.

Включить локальное развертывание Git для приложения с помощью проводов Azure (Предварительный просмотр):

1. На [портале Azure](https://portal.azure.com)ищите **службы приложений**и выберите их. 

1. Выберите приложение Azure App Service и выберите **Центр развертывания** в левом меню.
   
1. На странице **Центра развертывания** выберите **локальный Git,** а затем выберите **Продолжить**. 
   
   ![Выберите локальный Git, а затем выберите Продолжить](media/app-service-deploy-local-git/portal-enable.png)
   
1. На странице **поставщика сборки** выберите **azure Pipelines (Preview),** а затем выберите **Продолжить**. 
   
   ![Выберите конвейеры Azure (Предварительный просмотр), а затем выберите Продолжить.](media/app-service-deploy-local-git/pipeline-builds.png)

1. На странице **Настройка** наверсните новую организацию Azure DevOps или укажите существующую организацию, а затем выберите **Продолжить**.
   
   > [!NOTE]
   > Если существующая организация Azure DevOps не указана в списке, возможно, потребуется связать ее с подпиской Azure. Для получения дополнительной информации [см.](/azure/devops/pipelines/apps/cd/deploy-webdeploy-webapps#cd)
   
1. В зависимости от [уровня ценообразования](https://azure.microsoft.com/pricing/details/app-service/plans/)в ашего тарифном плане Службы Приложения вы можете увидеть **развертывание для постановки** страницы. Выберите, включать ли [слоты развертывания,](deploy-staging-slots.md)а затем выберите **Продолжить.**
   
1. На странице **Резюме** просмотрите настройки, а затем выберите **finish**.
   
1. Когда конвейер Azure будет готов, копируйте URL-адрес репозитория Git со страницы **Центра развертывания** для использования на следующем этапе. 
   
   ![Копирование URL-адреса репозитория Git](media/app-service-deploy-local-git/vsts-repo-ready.png)

1. В окне локального терминала добавьте пульт дистанционного управления Azure в локальное хранилище Git. В команде замените \<URL-> URL-адресом репозитория Git, полученным на предыдущем этапе.
   
   ```bash
   git remote add azure <url>
   ```
   
1. Нажмите на пульт дистанционного управления Azure с помощью `git push azure master`. 
   
1. На странице **Менеджера учетных данных Git** вопийте с visualstudio.com именем пользователя. Для других методов проверки подлинности смотрите [обзор аутентификации служб Azure DevOps.](/vsts/git/auth-overview?view=vsts)
   
1. После завершения развертывания просмотрите ход `https://<azure_devops_account>.visualstudio.com/<project_name>/_build`сборки в `https://<azure_devops_account>.visualstudio.com/<project_name>/_release`и ход развертывания в .
   
1. Просмотрите приложение на портале Azure, чтобы убедиться, что содержимое развернуто.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-deployment"></a>Устранение неполадок с развертыванием

Вы можете видеть следующие распространенные сообщения об ошибках при использовании Git для публикации в приложении App Service в Azure:

|Сообщение|Причина|Решение
---|---|---|
|`Unable to access '[siteURL]': Failed to connect to [scmAddress]`|Приложение не запущено и запущено.|запуск приложения на портале Azure. Развертывание Git недоступно при прекращении веб-приложения.|
|`Couldn't resolve host 'hostname'`|Информация адреса для пульта дистанционного управления «лазурный» неверна.|используйте команду `git remote -v`, чтобы вывести список всех удаленных репозиториев с соответствующими URL-адресами. Проверьте правильность URL-адреса удаленного репозитория "azure". При необходимости удалите и повторно создайте этот удаленный репозиторий, используя правильный URL-адрес.|
|`No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`|Вы не указали ветку во время, `git push` `push.default` или `.gitconfig`вы не установили значение в .|Повторите `git push` еще раз, указав мастер-ветку: `git push azure master`.|
|`src refspec [branchname] does not match any.`|Вы пытались нажать на ветку, кроме мастера на пульте "лазурный".|Повторите `git push` еще раз, указав мастер-ветку: `git push azure master`.|
|`RPC failed; result=22, HTTP code = 5xx.`|эта ошибка может возникнуть при попытке отправить большой репозиторий Git по протоколу HTTPS.|Измените конфигурацию git на локальной машине, `postBuffer` чтобы сделать больше. Например: `git config --global http.postBuffer 524288000`.|
|`Error - Changes committed to remote repository but your web app not updated.`|Вы развернули приложение Node.js с файлом _package.json,_ который определяет дополнительные необходимые модули.|Просмотрите `npm ERR!` сообщения об ошибке до этой ошибки для получения большего контекста сбоя. Ниже приведены известные причины этой `npm ERR!` ошибки, а также соответствующие сообщения:<br /><br />**Несформированный файл package.json**:`npm ERR! Couldn't read dependencies.`<br /><br />**Родной модуль не имеет двоичного дистрибутива для Windows:**<br />`npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1` <br />или диспетчер конфигурации служб <br />`npm ERR! [modulename@version] preinstall: \make || gmake\ `|

## <a name="additional-resources"></a>Дополнительные ресурсы

- [Документация по проекту Kudu](https://github.com/projectkudu/kudu/wiki)
- [Непрерывное развертывание в службе приложений Azure](deploy-continuous-deployment.md)
- [Пример: Создайте веб-приложение и разместите код из локального репозитория Git (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
- [Пример: Создайте веб-приложение и разместите код из локального репозитория Git (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
