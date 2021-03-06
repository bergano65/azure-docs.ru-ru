---
title: Создание первой функции на платформе Linux в Azure
description: Из этой статьи вы узнаете, как создать функцию, выполняемую на платформе Linux в Azure, с помощью средств командной строки, Azure Functions Core Tools и Azure CLI.
ms.date: 03/12/2019
ms.topic: quickstart
ms.custom: mvc, fasttrack-edit
ms.openlocfilehash: 57c1b4a0ef5e8f030360bca6d69f66e8752f3da1
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74964146"
---
# <a name="quickstart-create-your-first-function-hosted-on-linux-using-command-line-tools"></a>Краткое руководство. Создание функции, выполняемой в Linux, с помощью инструментов командной строки

Решение "Функции Azure" позволяет выполнять код в [бессерверной](https://azure.com/serverless) среде Linux без необходимости создавать виртуальную машину или публиковать веб-приложение. Для функций, выполняемых в Linux, требуется [среда выполнения Функций версии 2.x или более поздней версии](functions-versions.md). Бессерверные функции выполняются в [Плане потребления](functions-scale.md#consumption-plan).

В этом кратком руководстве вы узнаете, как использовать Azure CLI для создания первого приложения-функции на платформе Linux. Код функции создается локально, а затем развертывается в Azure с помощью [Azure Functions Core Tools](functions-run-local.md).

Описанные далее действия можно выполнять на компьютерах с Mac, Windows или Linux. В этой статье показано, как создавать функции на языке JavaScript или C#. Сведения о том, как создавать функции Python, см. в [этой статье](functions-create-first-function-python.md).

## <a name="prerequisites"></a>Предварительные требования

Перед выполнением этого примера вам понадобится следующее:

+ установить [Azure Functions Core Tools](./functions-run-local.md#v2) 2.6.666 или последующей версии;

+ Установка [Azure CLI]( /cli/azure/install-azure-cli). В этой статье требуется Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Можно также использовать [Azure Cloud Shell](https://shell.azure.com/bash).

+ Активная подписка Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [functions-create-function-app-cli](../../includes/functions-create-function-app-cli.md)]

## <a name="enable-extension-bundles"></a>Включение пакетов расширений

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

[!INCLUDE [functions-create-function-core-tools](../../includes/functions-create-function-core-tools.md)]

[!INCLUDE [functions-run-function-test-local](../../includes/functions-run-function-test-local.md)]

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-function-app-in-azure"></a>Создание приложения-функции Linux в Azure

Для выполнения функций в Linux вам понадобится приложение-функция, Приложение-функция предоставляет бессерверную среду для выполнения кода функции. Это позволяет группировать функции в логические единицы и упростить развертывание и совместное использование ресурсов, а также управление ими. Создайте приложение-функцию, выполняемое в Linux, с помощью команды [az functionapp create](/cli/azure/functionapp#az-functionapp-create).

В следующей команде используйте уникальное имя приложения-функции, в котором будут отображаться заполнитель `<app_name>` и имя учетной записи хранения `<storage_name>`. `<app_name>` также является доменом DNS по умолчанию для приложения-функции. Это имя должно быть уникальным среди всех приложений Azure. Вам также следует задать среду выполнения `<language>` для приложения-функции из `dotnet` (C#), `node` (JavaScript/TypeScript) или `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --consumption-plan-location westus --os-type Linux \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

После создания приложения-функции может отобразиться следующее сообщение:

```output
Your serverless Linux function app 'myfunctionapp' has been successfully created.
To active this function app, publish your app content using Azure Functions Core Tools or the Azure portal.
```

Теперь вы можете опубликовать проект в новом приложении-функции в Azure.

[!INCLUDE [functions-publish-project](../../includes/functions-publish-project.md)]

[!INCLUDE [functions-test-function-code](../../includes/functions-test-function-code.md)]

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources.md)]

[!INCLUDE [functions-quickstart-next-steps-cli](../../includes/functions-quickstart-next-steps-cli.md)]