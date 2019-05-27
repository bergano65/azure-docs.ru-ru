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
ms.date: 06/05/2018
ms.author: dariagrigoriu;cephalin
ms.custom: seodec18
ms.openlocfilehash: b879036dcd79901cb634fa197932e833cb22d12a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "65956068"
---
# <a name="local-git-deployment-to-azure-app-service"></a>Развертывание локального репозитория Git в службе приложений Azure

В этом руководстве показано, как развернуть в [службе приложений Azure](overview.md) свой код из репозитория Git на локальном компьютере.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Технические условия

Выполните следующие шаги для изучения данного руководства.

* [Установка Git](https://www.git-scm.com/downloads).
* Обеспечьте локальный репозиторий Git с кодом, который вы хотите развернуть.

Чтобы в дальнейшем использовать пример репозитория, выполните следующую команду в локальном окне терминала.

```bash
git clone https://github.com/Azure-Samples/nodejs-docs-hello-world.git
```

[!INCLUDE [Prepare repository](../../includes/app-service-deploy-prepare-repo.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="deploy-with-kudu-builds"></a>Развертывание с помощью Kudu сборок

Включить локальное развертывание Git для вашего приложения с сервера сборки Kudu проще всего с помощью облачной среды.

### <a name="configure-a-deployment-user"></a>Настойка пользователя развертывания

[!INCLUDE [Configure a deployment user](../../includes/configure-deployment-user-no-h.md)]

### <a name="enable-local-git-with-kudu"></a>Включить локальный Git с помощью Kudu

Чтобы включить локальное развертывание Git для вашего приложения с сервера сборки Kudu, выполните [`az webapp deployment source config-local-git`](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-local-git) в Cloud Shell.

```azurecli-interactive
az webapp deployment source config-local-git --name <app_name> --resource-group <group_name>
```

Чтобы вместо этого создать приложение с поддержкой Git, выполните команду [`az webapp create`](/cli/azure/webapp?view=azure-cli-latest#az-webapp-create) в Cloud Shell с параметром `--deployment-local-git`.

```azurecli-interactive
az webapp create --name <app_name> --resource-group <group_name> --plan <plan_name> --deployment-local-git
```

Результат команды `az webapp create` должен выглядеть примерно так:

```json
Local git is configured with url of 'https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git'
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
  "containerSize": 0,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "<app_name>.azurewebsites.net",
  "deploymentLocalGitUrl": "https://<username>@<app_name>.scm.azurewebsites.net/<app_name>.git",
  "enabled": true,
  < JSON data removed for brevity. >
}
```

### <a name="deploy-your-project"></a>Развертывание проекта

Вернитесь к _окну терминала (в локальном расположении)_ и добавьте удаленное приложение Azure в локальный репозиторий Git. Замените _\<url>_ URL-адресом удаленного репозитория Git, полученным на шаге [Включение использования репозитория Git для приложения](#enable-local-git-with-kudu).

```bash
git remote add azure <url>
```

Отправьте код в удаленное приложение Azure, чтобы развернуть приложение. При появлении запроса введите пароль, созданный в разделе на шаге [настройки пользователя развертывания](#configure-a-deployment-user) (а не используемый для входа на портал Azure).

```bash
git push azure master
```

В выходных данных могут отображаться автоматизированные операции времени выполнения, например MSBuild для ASP.NET, `npm install` для Node.js и `pip install` для Python. 

Перейдите к своему приложению, чтобы убедиться, что содержимое развернуто.

## <a name="deploy-with-azure-devops-builds"></a>Развертывание с помощью сборок Azure DevOps

> [!NOTE]
> Чтобы служба приложений создавала необходимые конвейеры Azure Pipelines в организации Azure DevOps Services, ваша учетная запись Azure должна иметь роль **владельца** в подписке Azure.
>

Чтобы включить локальное развертывание Git для вашего приложения с сервера сборки с помощью Kudu, перейдите в свое приложение на [портале Azure](https://portal.azure.com).

На левой навигационной панели страницы приложения щелкните **Центр развертывания** > **Локальный Git** > **Продолжить**.

![](media/app-service-deploy-local-git/portal-enable.png)

Нажмите кнопку **конвейеры Azure (Предварительная версия)** > **по-прежнему**.

![](media/app-service-deploy-local-git/pipeline-builds.png)

В **Настройка** странице, настроить новую организацию DevOps в Azure или указать существующую организацию. По завершении нажмите кнопку **Продолжить**.

> [!NOTE]
> Если вы хотите использовать существующую организацию Azure DevOps, отсутствующий в списке, необходимо [привязка служб Azure DevOps организации к подписке Azure](https://github.com/projectkudu/kudu/wiki/Setting-up-a-VSTS-account-so-it-can-deploy-to-a-Web-App).

В зависимости от [ценовой категории](https://azure.microsoft.com/pricing/details/app-service/plans/) вашего плана App Service также можно увидеть страницу **Разворачивание по этапам**. Выберите, где нужно включить слоты развертывания, затем щелкните **Продолжить**.

На странице **Сводка** проверьте параметры и нажмите **Готово**.

Настройка организации Azure DevOps Services займет несколько минут. Когда она будет готова, скопируйте URL-адрес репозитория Git в центре развертывания.

![](media/app-service-deploy-local-git/vsts-repo-ready.png)

Вернитесь к _окну терминала (в локальном расположении)_ и добавьте удаленное приложение Azure в локальный репозиторий Git. Замените _\<url>_ на URL-адрес, полученный на последнем шаге.

```bash
git remote add vsts <url>
```

Отправьте код в удаленное приложение Azure, чтобы развернуть приложение. При запросе диспетчера ввода учетных данных Git войдите с помощью пользователя visualstudio.com. Дополнительные методы проверки подлинности см. в статье [Authentication overview](/vsts/git/auth-overview?view=vsts) (Общие сведения о проверке подлинности).

```bash
git push vsts master
```

После завершения развертывания можно увидеть выполнение сборки в `https://<vsts_account>.visualstudio.com/<project_name>/_build` и выполнение развертывания в `https://<vsts_account>.visualstudio.com/<project_name>/_release`.

Перейдите к своему приложению, чтобы убедиться, что содержимое развернуто.

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshooting-kudu-deployment"></a>Устранение неполадок при развертывании Kudu

Ниже перечислены распространенные ошибки или проблемы, возникающие при использовании Git для публикации в приложение службы приложений в Azure.

---
**Симптом**: `Unable to access '[siteURL]': Failed to connect to [scmAddress]`

**Причина:** эта ошибка может возникнуть, если приложение не работает.

**Решение:** запуск приложения на портале Azure. Если веб-приложение остановлено развертывание Git недоступно.

---
**Симптом**: `Couldn't resolve host 'hostname'`

**Причина:** эта ошибка может возникнуть, если при создании удаленного репозитория azure был введен неправильный адрес.

**Решение:** используйте команду `git remote -v`, чтобы вывести список всех удаленных репозиториев с соответствующими URL-адресами. Проверьте правильность URL-адреса удаленного репозитория "azure". При необходимости удалите и повторно создайте этот удаленный репозиторий, используя правильный URL-адрес.

---
**Симптом**: `No refs in common and none specified; doing nothing. Perhaps you should specify a branch such as 'master'.`

**Причина:** эта ошибка может возникнуть, если при использовании команды `git push` не была указана ветвь или если не задано значение `push.default` в `.gitconfig`.

**Решение:** выполните команду `git push` еще раз, указав главную ветвь. Например:

```bash
git push azure master
```

---
**Симптом**: `src refspec [branchname] does not match any.`

**Причина:** эта ошибка может возникнуть при попытке принудительно отправить данные в ветвь, отличную от главной, в удаленном репозитории azure.

**Решение:** выполните команду `git push` еще раз, указав главную ветвь. Например:

```bash
git push azure master
```

---
**Симптом**: `RPC failed; result=22, HTTP code = 5xx.`

**Причина:** эта ошибка может возникнуть при попытке отправить большой репозиторий Git по протоколу HTTPS.

**Решение:** измените конфигурацию Git на локальном компьютере, чтобы увеличить значение postBuffer.

```bash
git config --global http.postBuffer 524288000
```

---
**Симптом**: `Error - Changes committed to remote repository but your web app not updated.`

**Причина:** эта ошибка может возникнуть при развертывании приложения Node.js, содержащего файл _package.json_, в котором указаны дополнительные необходимые модули.

**Решение:** до этой ошибки должны быть зарегистрированы дополнительные сообщения, содержащие "npm ERR!", и они могут предоставлять дополнительный контекст для данного сбоя. Ниже перечислены известные причины этой ошибки и соответствующее сообщение npm ERR!. сообщение:

* **Malformed package.json file**: npm ERR! Couldn't read dependencies (не удалось прочитать зависимости).
* **Собственный модуль, не имеющий двоичного дистрибутива для Windows**:

  * `npm ERR! \cmd "/c" "node-gyp rebuild"\ failed with 1`

      Или
  * `npm ERR! [modulename@version] preinstall: \make || gmake\`

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Документация по проекту Kudu](https://github.com/projectkudu/kudu/wiki)
* [Непрерывное развертывание в службе приложений Azure](deploy-continuous-deployment.md)
* [Пример. Создание веб-приложения и развертывание кода из локального репозитория Git (Azure CLI)](./scripts/cli-deploy-local-git.md?toc=%2fcli%2fazure%2ftoc.json)
* [Пример. Создание веб-приложения и развертывание кода из локального репозитория Git (PowerShell)](./scripts/powershell-deploy-local-git.md?toc=%2fpowershell%2fmodule%2ftoc.json)
