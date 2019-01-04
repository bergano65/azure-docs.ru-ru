---
title: Создание приложения-функции на платформе Linux в плане службы приложений Azure
description: Узнайте, как с помощью Azure CLI создавать приложение-функцию на платформе Linux в плане службы приложений.
services: functions
keywords: ''
author: ggailey777
ms.author: glenga
ms.date: 11/28/2018
ms.topic: conceptual
ms.service: azure-functions
ms.custom: mvc
ms.devlang: azure-cli
manager: jeconnoc
ms.openlocfilehash: d9800ff3fc82636c5cae12167738667ec84326ee
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52855555"
---
# <a name="create-a-function-app-on-linux-in-an-azure-app-service-plan-preview"></a>Создание приложения-функции на платформе Linux в плане службы приложений Azure (предварительная версия)

Функции Azure позволяют размещать в Linux собственные функции в контейнере службы приложения Azure по умолчанию. В этой статье рассматриваются способы использования Azure CLI для создания приложения-функции, размещенного на Linux в Azure, которая выполняется в [плане службы приложений](functions-scale.md#app-service-plan). Также можно [использовать собственный настраиваемый контейнер](functions-create-function-linux-custom-image.md). Размещение на Linux в данный момент находится в предварительной версии.

В плане службы приложений вы отвечаете за масштабирование вашего приложения-функции. Чтобы воспользоваться преимуществами бессерверных возможностей Azure Functions, можно разместить свои функции в [плане потребления](functions-scale.md#consumption-plan) на платформе Linux.

Выполните действия, приведенные ниже, с помощью компьютера Mac, Windows или Linux.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим кратким руководством вам понадобится:

+ Активная подписка Azure.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать интерфейс командной строки локально, для работы с этим руководством вам понадобится Azure CLI 2.0.21 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli). 

[!INCLUDE [functions-create-resource-group](../../includes/functions-create-resource-group.md)]

[!INCLUDE [functions-create-storage-account](../../includes/functions-create-storage-account.md)]

## <a name="create-a-linux-app-service-plan"></a>Создание плана службы приложений Linux

[!INCLUDE [app-service-plan-no-h](../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

## <a name="create-a-function-app-on-linux"></a>Создание приложения-функции в Linux

Для выполнения функций в Linux вам понадобится приложение-функция, предоставляющее среду для выполнения кода функции. Это позволяет группировать функции в логические единицы и упростить развертывание и совместное использование ресурсов, а также управление ими. Создайте приложение-функцию с помощью команды [az functionapp create](/cli/azure/functionapp#az-functionapp-create) в плане службы приложений Linux.

В следующей команде замените `<app_name>` уникальным именем вашего приложения функции, а `<storage_name>` — именем учетной записи хранения. `<app_name>` используется по умолчанию в качестве домена DNS для приложения-функции. Поэтому это имя должно быть уникальным для всех приложений в Azure. Также следует задать среду выполнения `<language>` для приложения-функции из `dotnet` (C#), `node` (JavaScript) или `python`.

```azurecli-interactive
az functionapp create --resource-group myResourceGroup --plan myAppServicePlan \
--name <app_name> --storage-account  <storage_name> --runtime <language>
```

После создания и развертывания приложения-функции в Azure CLI отображаются следующие сведения:

```json
{
  "availabilityState": "Normal",
  "clientAffinityEnabled": true,
  "clientCertEnabled": false,
  "cloningInfo": null,
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

`myAppServicePlan` — это план Linux, поэтому встроенный образ Docker используется для создания контейнера, который запускает приложение-функцию в Linux.

[!INCLUDE [functions-cleanup-resources](../../includes/functions-cleanup-resources-simple.md)]

## <a name="next-steps"></a>Дальнейшие действия

В этой статье описывается как в Azure создать приложение-функцию размещенную в Linux. Теперь в это приложение-функцию можно [развернуть проект функции](https://docs.microsoft.com/cli/azure/functionapp/deployment/source?view=azure-cli-latest). Чтобы [создать проект функций](functions-run-local.md#create-a-local-functions-project) на локальном компьютере и развернуть его в новом приложении-функции Linux, можно использовать Azure Functions Core Tools.  

> [!div class="nextstepaction"] 
> [Как программировать и тестировать функции Azure в локальной среде](functions-run-local.md)
