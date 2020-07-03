---
title: Развертывание кода с помощью ZIP-файла или с файлом WAR
description: Узнайте, как развернуть приложение в Службе приложений Azure с помощью ZIP-файла (или WAR-файла для разработчиков Java).
ms.topic: article
ms.date: 08/12/2019
ms.reviewer: sisirap
ms.custom: seodec18
ms.openlocfilehash: 716f6813e37aec086a7d496e001fe2ca0f4aab57
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "75945180"
---
# <a name="deploy-your-app-to-azure-app-service-with-a-zip-or-war-file"></a>Развертывание приложения в Службе приложений Azure с помощью ZIP- или WAR-файла

В этой статье показано, как с помощью ZIP- или WAR-файла развернуть веб-приложение в [Службе приложений Azure](overview.md). 

Развертывание из ZIP-файла осуществляется с помощью той же службы Kudu, которая обеспечивает непрерывное развертывание на основе интеграции. Kudu поддерживает следующие возможности развертывания из ZIP-файла: 

- удаление файлов, оставшихся после предыдущего развертывания;
- включение процесса сборки по умолчанию, в том числе восстановления пакетов;
- настройку развертывания, в том числе выполнение скриптов развертывания;  
- журналы развертывания. 
- Предельный размер файла в 2048 МБ.

Дополнительные сведения см. в [документации по Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file).

[WAR](https://wikipedia.org/wiki/WAR_(file_format))-файл развертывается в службе приложений для запуска веб-приложения Java. См. раздел [Развертывание WAR-файла](#deploy-war-file).

## <a name="prerequisites"></a>Предварительные требования

Чтобы выполнить действия, описанные в этой статье, [Создайте приложение службы приложений](/azure/app-service/)или используйте приложение, созданное для другого руководства.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [Create a project ZIP file](../../includes/app-service-web-deploy-zip-prepare.md)]

[!INCLUDE [Deploy ZIP file](../../includes/app-service-web-deploy-zip.md)]
В настоящее время указанная выше конечная точка не работает для служб приложений Linux. Вместо этого рекомендуется использовать FTP или [API-интерфейс развертывания ZIP](https://docs.microsoft.com/azure/app-service/containers/app-service-linux-faq#continuous-integration-and-deployment) .

## <a name="deploy-zip-file-with-azure-cli"></a>Развертывание ZIP-файла с помощью с Azure CLI

Разверните загруженный ZIP-файл в веб-приложение с помощью команды [az webapp deployment source config-zip](/cli/azure/webapp/deployment/source?view=azure-cli-latest#az-webapp-deployment-source-config-zip).  

Следующий пример развертывает загруженный ZIP-файл. При использовании локальной установки Azure CLI укажите путь к локальному ZIP-файлу `--src`.

```azurecli-interactive
az webapp deployment source config-zip --resource-group <group-name> --name <app-name> --src clouddrive/<filename>.zip
```

Эта команда позволяет развертывать файлы и каталоги из ZIP-файлов в папку для приложения службы приложений по умолчанию (`\home\site\wwwroot`). Затем приложение перезапускается.

По умолчанию подсистема развертывания предполагает, что ZIP-файл готов к выполнению "как есть" и не выполняет автоматизацию сборки. Чтобы включить ту же автоматизацию сборки, что и в [развертывании Git](deploy-local-git.md), задайте параметр `SCM_DO_BUILD_DURING_DEPLOYMENT` приложения, выполнив следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings SCM_DO_BUILD_DURING_DEPLOYMENT=true
```

Дополнительные сведения см. в [документации по Kudu](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).

[!INCLUDE [app-service-deploy-zip-push-rest](../../includes/app-service-deploy-zip-push-rest.md)]  

## <a name="deploy-war-file"></a>Развертывание WAR-файла

Чтобы развернуть WAR-файл в службе приложений, отправьте запрос POST в `https://<app-name>.scm.azurewebsites.net/api/wardeploy`. В тексте сообщения запроса POST должен содержаться WAR-файл. Учетные данные развертывания для приложения указываются в запросе с использованием обычной проверки подлинности HTTP.

Всегда используйте `/api/wardeploy` при РАЗВЕРТЫВАНИи War-файлов. Этот API расширяет WAR-файл и размещает его на общем файловом диске. использование других API-интерфейсов развертывания может привести к несовместимости поведения. 

Для обычной аутентификации HTTP требуются учетные данные развертывания службы приложений. См. дополнительные сведения об [установке и сбросе учетных данных на уровне пользователя](deploy-configure-credentials.md#userscope).

### <a name="with-curl"></a>Использование cURL

В примере ниже для развертывания WAR-файла используется средство cURL. Замените заполнители `<username>`, `<war-file-path>` и `<app-name>`. Когда в cURL появится запрос, введите пароль.

```bash
curl -X POST -u <username> --data-binary @"<war-file-path>" https://<app-name>.scm.azurewebsites.net/api/wardeploy
```

### <a name="with-powershell"></a>С помощью PowerShell

В следующем примере с помощью [Publish-азвебапп](/powershell/module/az.websites/publish-azwebapp) загружаются файлы. War. Замените заполнители `<group-name>`, `<app-name>` и `<war-file-path>`.

```powershell
Publish-AzWebapp -ResourceGroupName <group-name> -Name <app-name> -ArchivePath <war-file-path>
```

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="next-steps"></a>Следующие шаги

Чтобы изучить более сложные сценарии развертывания, ознакомьтесь с [развертыванием в Azure с помощью Git](deploy-local-git.md). Развертывание в Azure на основе Git обеспечивает систему управления версиями, восстановление пакета, MSBuild и многое другое.

## <a name="more-resources"></a>Дополнительные ресурсы

* [Deploying from a zip file](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file) (Развертывание из ZIP-файла)
* [Учетные данные развертывания службы приложений Azure](deploy-ftp.md)
