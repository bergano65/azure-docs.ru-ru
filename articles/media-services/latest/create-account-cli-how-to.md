---
title: Создание учетной записи Служб мультимедиа с помощью Azure CLI — Azure | Документация Майкрософт
description: В этом кратком руководстве описано, как создать учетную запись Служб мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: seodec18
ms.date: 01/15/2019
ms.author: juliako
ms.openlocfilehash: 9c026eb9a74cbba2ff188a3f08be625043ede474
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352059"
---
# <a name="create-an-azure-media-services-account"></a>Создание учетной записи служб мультимедиа Azure

Чтобы начать шифрование, кодирование, анализ, потоковую передачу мультимедийного содержимого и управление им в Azure, необходимо создать учетную запись Служб мультимедиа. При создании учетной записи служб мультимедиа также создается связанная учетная запись хранения (или используется уже существующая).  

Учетная запись Службы мультимедиа Azure и связанная с ней учетная запись хранения должны размещаться в одном центре обработки данных и в одной группе ресурсов.

В этой статье описаны действия по созданию учетной записи Служб мультимедиа Azure с помощью Azure CLI.  

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

- Активная подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.
- Установите и используйте CLI на локальном компьютере. Для работы с этим руководством вам понадобится Azure CLI 2.0 или более поздней версии. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli). 

    Сейчас в Azure Cloud Shell работают не все команды [интерфейса командной строки Служб мультимедиа версии 3](https://aka.ms/ams-v3-cli-ref). Рекомендуется использовать интерфейс командной строки локально.

## <a name="set-the-azure-subscription"></a>Настройка подписки Azure

В приведенной ниже команде укажите идентификатор подписки Azure, который необходимо использовать в учетной записи Служб мультимедиа. Чтобы просмотреть список доступных подписок, перейдите на страницу [Подписки](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>Дополнительная информация

[Краткое руководство по потоковой передаче видеофайлов — .NET](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>См. также

[Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

