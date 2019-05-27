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
ms.date: 05/19/2019
ms.author: juliako
ms.openlocfilehash: f2cb2e2ee6393a59125ee879f2058516eb50d6b1
ms.sourcegitcommit: 24fd3f9de6c73b01b0cee3bcd587c267898cbbee
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65956713"
---
# <a name="create-an-azure-media-services-account"></a>Создание учетной записи служб мультимедиа Azure

Чтобы начать шифрование, кодирование, анализ, потоковую передачу мультимедийного содержимого и управление им в Azure, необходимо создать учетную запись Служб мультимедиа. Учетную запись Служб мультимедиа необходимо связать с одной или несколькими учетными записями хранения.

> [!NOTE]
> Учетная запись Служб мультимедиа и все связанные учетные записи хранения должны размещаться в одной подписке Azure. Настоятельно рекомендуется использовать учетные записи хранения в том же расположении, в котором находится учетная запись Служб мультимедиа, чтобы уменьшить задержку передачи данных и избежать дополнительных затрат на исходящий трафик.

В этой статье описаны действия по созданию учетной записи Служб мультимедиа Azure с помощью Azure CLI.  

## <a name="prerequisites"></a>Технические условия

Активная подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio), прежде чем начинать работу.

[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

## <a name="set-the-azure-subscription"></a>Настройка подписки Azure

В приведенной ниже команде укажите идентификатор подписки Azure, который необходимо использовать в учетной записи Служб мультимедиа. Чтобы просмотреть список доступных подписок, перейдите на страницу [Подписки](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade).

```azurecli
az account set --subscription mySubscriptionId
```
 
[!INCLUDE [media-services-cli-create-v3-account-include](../../../includes/media-services-cli-create-v3-account-include.md)]
 
## <a name="next-steps"></a>Дальнейшие действия

* [Доступ к API v3](access-api-cli-how-to.md)
* [Краткое руководство по потоковой передаче видеофайлов — .NET](stream-files-dotnet-quickstart.md)
* [Присоединение дополнительного хранилища в учетную запись служб мультимедиа](https://docs.microsoft.com/cli/azure/ams/account/storage?view=azure-cli-latest#az-ams-account-storage-add)

## <a name="see-also"></a>См. также

[Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

