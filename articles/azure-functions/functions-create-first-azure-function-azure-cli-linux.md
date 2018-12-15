---
title: Создание первой функции на платформе Linux в Azure
description: Сведения о создании первой функции, выполняемой на платформе Linux в Azure, с помощью Azure CLI и Azure Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/28/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: javascript
manager: jeconnoc
ms.openlocfilehash: b6df653f89f05a9b253ecea102ed8310ff2a53b7
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438295"
---
# <a name="create-your-first-function-hosted-on-linux-using-core-tools-and-the-azure-cli-preview"></a>Создание первой функции, выполняемой в Linux, с помощью основных инструментов и Azure CLI (предварительная версия)

Решение "Функции Azure" позволяет выполнять код в [бессерверной](https://azure.com/serverless) среде Linux без необходимости создавать виртуальную машину или публиковать веб-приложение. Размещение в Linux сейчас доступно в виде предварительной версии. Для этого требуется наличие [среды выполнения Функций версии 2.0](functions-versions.md). Дополнительные сведения о рекомендациях по предварительной версии для выполнения приложения-функции на платформе Linux см. [в этой статье](https://aka.ms/funclinux).

В этом кратком руководстве вы узнаете, как использовать Azure CLI для создания первого приложения-функции на платформе Linux. Код функции создается локально, а затем развертывается в Azure с помощью [Azure Functions Core Tools](functions-run-local.md).

Описанные далее действия можно выполнять на компьютерах с Mac, Windows или Linux. В этой статье показано, как создавать функции на языке JavaScript или C#. Сведения о том, как создавать функции Python, см в статье о[ создании функций Python с помощью Azure Functions Core Tools и Azure CLI (предварительная версия)](functions-create-first-function-python.md).

## <a name="prerequisites"></a>Предварительные требования

Перед выполнением этого примера вам понадобится следующее:

+ Установка [основных инструментов Azure версии 2.x](functions-run-local.md#v2).

+ Установка [Azure CLI]( /cli/azure/install-azure-cli). В этой статье требуется Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Можно также использовать [Azure Cloud Shell](https://shell.azure.com/bash).

+ Активная подписка Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="create-the-local-function-app-project"></a>Создание локального проекта приложения-функции

Выполните следующую команду из командной строки, чтобы создать проект приложения-функции в папке `MyFunctionProj` текущего локального каталога. Также в `MyFunctionProj` создается репозиторий GitHub.

```bash
func init MyFunctionProj
```

При появлении запроса с помощью клавиш со стрелками выберите время выполнения рабочей роли из приведенных ниже параметров языка.

+ `dotnet`. Создает проект библиотеки классов .NET (CSPROJ-файл).
+ `node`. Создает проект JavaScript.
+ `python`. Создает проект Python. Сведения о функциях Python см. в см в статье о[ создании функций Python с помощью Azure Functions Core Tools и Azure CLI (предварительная версия)](functions-create-first-function-python.md).

При выполнении команды вы увидите выходные данные примерно следующего содержания.

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Создание плана службы приложений Linux

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Создание приложения-функции Linux в Azure

Для выполнения функций в Linux вам понадобится приложение-функция, Приложение-функция предоставляет бессерверную среду для выполнения кода функции. Это позволяет группировать функции в логические единицы и упростить развертывание и совместное использование ресурсов, а также управление ими. Создайте приложение-функцию, выполняемое в Linux, с помощью команды [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

В следующей команде используйте уникальное имя приложения-функции, в котором будут отображаться заполнитель `<app_name>` и имя учетной записи хранения `<storage_name>`. `<app_name>` также является доменом DNS по умолчанию для приложения-функции. Это имя должно быть уникальным среди всех приложений Azure. Также следует задать среду выполнения `<language>` для приложения-функции из `dotnet` (C#), `node` (JavaScript) или `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westus --os-type Linux \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

> [!NOTE]
> Если у вас есть группа ресурсов с именем `myResourceGroup` с приложениями службы приложений не на платформе Linux, необходимо использовать другую группу ресурсов. Вы не можете разместить приложения Windows и Linux в одной группе ресурсов.  

После создания приложения-функции может отобразиться следующее сообщение:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Теперь вы можете опубликовать проект в новом приложении-функции в Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

## <a name="next-steps"></a>Дополнительная информация

В этой статье показано, как разместить приложение-функцию в контейнере по умолчанию Службы приложений Azure. Вы также можете размещать в Linux собственные функции в пользовательском контейнере.

> [!div class="nextstepaction"]
> [Создание функции в Linux из пользовательского образа](functions-create-function-linux-custom-image.md)
