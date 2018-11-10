---
title: Создание первой функции на платформе Linux в Azure
description: Сведения о создании первой функции, выполняемой на платформе Linux в Azure, с помощью Azure CLI и Azure Functions Core Tools.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 09/12/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: multiple
manager: jeconnoc
ms.openlocfilehash: 1045e0cc0d114bb8b35e6136a2054b3642eac7e8
ms.sourcegitcommit: 1d3353b95e0de04d4aec2d0d6f84ec45deaaf6ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50249882"
---
# <a name="create-your-first-function-hosted-on-linux-using-core-tools-and-the-azure-cli-preview"></a>Создание первой функции, выполняемой в Linux, с помощью основных инструментов и Azure CLI (предварительная версия)

Решение "Функции Azure" позволяет выполнять код в [бессерверной](https://azure.microsoft.com/overview/serverless-computing/) среде Linux без необходимости создавать виртуальную машину или публиковать веб-приложение. Размещение в Linux сейчас доступно в виде предварительной версии. Для этого требуется наличие [среды выполнения Функций версии 2.0](functions-versions.md).

В этом кратком руководстве вы узнаете, как использовать Azure CLI для создания первого приложения-функции на платформе Linux. Код функции создается локально, а затем развертывается в Azure с помощью [Azure Functions Core Tools](functions-run-local.md).

Описанные далее действия можно выполнять на компьютерах с Mac, Windows или Linux. В этой статье показано, как создавать функции на языке JavaScript или C#.

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

## <a name="create-a-linux-function-app-in-azure"></a>Создание приложения-функции Linux в Azure

Для выполнения функций в Linux вам понадобится приложение-функция, Приложение-функция предоставляет бессерверную среду для выполнения кода функции. Это позволяет группировать функции в логические единицы и упростить развертывание и совместное использование ресурсов, а также управление ими. Создайте приложение-функцию, выполняемое в Linux, с помощью команды [az functionapp create](/cli/azure/functionapp#az_functionapp_create).

В следующей команде используйте уникальное имя приложения-функции, в котором будут отображаться заполнитель `<app_name>` и имя учетной записи хранения `<storage_name>`. `<app_name>` также является доменом DNS по умолчанию для приложения-функции. Это имя должно быть уникальным среди всех приложений Azure.

```azurecli
az functionapp create --name <app_name> --storage-account  <storage_name>  --resource-group myResourceGroup \
--location "westus" --is-linux
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
