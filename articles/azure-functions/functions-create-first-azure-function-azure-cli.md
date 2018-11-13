---
title: Создание первой функции с помощью Azure CLI
description: Узнайте, как создать свою первую Функцию Azure для бессерверного выполнения с помощью Azure CLI и основных инструментов службы "Функции Azure".
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.assetid: 674a01a7-fd34-4775-8b69-893182742ae0
ms.date: 09/10/2018
ms.topic: quickstart
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: fdee336298212f2536c2408e49f40e25e2c24161
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51227694"
---
# <a name="create-your-first-function-from-the-command-line"></a>Создание первой функции из командной строки

В этом кратком руководстве рассматривается создание первой функции из командной строки или терминала. Используйте Azure CLI для создания приложения-функции, которое является [независимой от сервера](https://azure.microsoft.com/solutions/serverless/) инфраструктурой, где размещается ваша функция. Проект кода функции создается из шаблона с помощью [основных инструментов службы "Функции Azure"](functions-run-local.md), которые также используются для развертывания проекта приложения-функции в Azure.

Выполните действия, приведенные ниже, с помощью компьютера Mac, Windows или Linux.

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

Когда появится запрос, выберите среду выполнения рабочей роли из следующих языков:

+ `dotnet`. Создает проект библиотеки классов .NET (CSPROJ-файл).
+ `node`. Создает проект JavaScript.

При выполнении команды вы увидите выходные данные примерно следующего содержания.

```output
Writing .gitignore
Writing host.json
Writing local.settings.json
Initialized empty Git repository in C:/functions/MyFunctionProj/.git/
```

Чтобы перейти к новой папке проекта `MyFunctionProj`, используйте следующую команду.

```bash
cd MyFunctionProj
```

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-update-function-code](../../includes/functions-update-function-code.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-function-app"></a>Создание приложения-функции

Для выполнения функций вам понадобится приложение-функция, предоставляющее среду для выполнения кода функции без сервера. Это позволяет группировать функции в логические единицы и упростить развертывание и совместное использование ресурсов, а также управление ими. Создайте приложение-функцию с помощью команды [az functionapp create](/cli/azure/functionapp#az-functionapp-create). 

В следующей команде замените `<app_name>` уникальным именем вашего приложения функции, а `<storage_name>` — именем учетной записи хранения. `<app_name>` используется по умолчанию в качестве домена DNS для приложения-функции. Поэтому это имя должно быть уникальным для всех приложений в Azure. Параметр _deployment-source-url_ — это пример репозитория в GitHub, который содержит функцию Hello World, активируемую с помощью HTTP.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westeurope \
--name <app_name> --storage-account  <storage_name>  
```

Если задать параметр _consumption-plan-location_, приложение-функция размещено в плане размещения по мере использования. В этом бессерверном плане ресурсы добавляются динамически, когда этого требуют функции, и оплачивается только выполнение функций. Дополнительные сведения см. в статье [Выбор правильного плана обслуживания для Функций Azure](functions-scale.md).

После создания приложения-функции в Azure CLI отображаются следующие сведения:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "containerSize": 1536,
  "dailyMemoryTimeQuota": 0,
  "defaultHostName": "quickstart.azurewebsites.net",
  "enabled": true,
  "enabledHostNames": [
    "quickstart.azurewebsites.net",
    "quickstart.scm.azurewebsites.net"
  ],
   ....
    // Remaining output has been truncated for readability.
}
```

### <a name="configure-the-function-app-nodejs"></a>Настройка приложения-функции (Node.js)

При создании приложения-функции JavaScript очень важно выбрать правильную версию Node.js. Для версии 2.x среды выполнения Функций требуется Node.js версии 8.x. Параметр приложения `WEBSITE_NODE_DEFAULT_VERSION` позволяет управлять версиями Node.js, которые использует приложение-функция в Azure. Чтобы указать для Node.js версию `8.11.1`, используйте команду [az functionapp config appsettings set](https://docs.microsoft.com/cli/azure/functionapp/config/appsettings#set).

В следующей команде Azure CLI <app_name> — это имя приложения-функции.

```azurecli-interactive
az functionapp config appsettings set --name <app_name> \
--resource-group myResourceGroup \
--settings WEBSITE_NODE_DEFAULT_VERSION=8.11.1
```

Проверьте значение параметра в выходных данных.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]

