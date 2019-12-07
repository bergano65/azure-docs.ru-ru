---
title: Доступ к API Служб мультимедиа Azure (Azure CLI) | Документы Майкрософт
description: Чтобы получить доступ к API служб мультимедиа Azure, выполните действия, описанные в этой статье.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 05/15/2019
ms.author: juliako
ms.openlocfilehash: d66b3e1b6ed2c8eef9f5cd21c0657648ad550ebe
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896157"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Доступ к API Служб мультимедиа Azure с помощью Azure CLI
 
Чтобы использовать проверку подлинности субъекта-службы Azure AD для подключения к API служб мультимедиа Azure, приложению необходимо запросить маркер Azure AD со следующими параметрами:

* Конечная точка клиента Azure AD.
* Универсальный код ресурса (URI) для ресурса служб мультимедиа.
* Универсальный код ресурса (URI) для ресурса REST служб мультимедиа.
* Значения приложения Azure AD: идентификатор и секрет клиента.

Подробное описание см. в разделе [доступ к API служб мультимедиа v3](media-services-apis-overview.md#accessing-the-azure-media-services-api).

В этой статье показано, как с помощью Azure CLI создать приложение Azure AD и субъект-службу, а также получить значения, необходимые при доступе к ресурсам Служб мультимедиа Azure.

## <a name="prerequisites"></a>Технические условия 

[Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md).

Запишите значения, которые вы использовали в качестве имени группы ресурсов и имени учетной записи Служб мультимедиа.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>Дополнительные материалы

- [Масштабирование зарезервированных единиц мультимедиа с помощью CLI](media-reserved-units-cli-how-to.md)
- [Создание учетной записи Служб мультимедиа с помощью CLI](create-account-cli-how-to.md) 
- [Сброс данных учетной записи с помощью CLI](cli-reset-account-credentials.md)
- [Создание средства с помощью CLI](cli-create-asset.md)
- [Отправка файла с помощью CLI](cli-upload-file-asset.md)
- [Создание преобразований с помощью CLI](cli-create-transform.md)
- [Кодирование с помощью пользовательского преобразования — CLI](custom-preset-cli-howto.md)
- [Создание заданий с помощью CLI](cli-create-jobs.md)
- [Создание сетки событий с помощью CLI](job-state-events-cli-how-to.md)
- [Публикация ресурса с помощью CLI](cli-publish-asset.md)
- [Фильтрация с помощью CLI](filters-dynamic-manifest-cli-howto.md)
- [Azure CLI](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)

## <a name="next-steps"></a>Дальнейшие действия

Конечная точка потоковой передачи, из которой требуется потоковая передача содержимого, должна находиться в состоянии выполнения. Следующая команда CLI запускает конечную точку потоковой передачи по умолчанию:

`az ams streaming-endpoint start -n default -a <amsaccount> -g <amsResourceGroup>`

