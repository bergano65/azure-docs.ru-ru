---
title: Доступ к API Служб мультимедиа Azure (Azure CLI) | Документы Майкрософт
description: В этом руководстве приведены сведения о получении доступа к API Служб мультимедиа Azure.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.custom: mvc
ms.date: 01/28/2019
ms.author: juliako
ms.openlocfilehash: 1b872c5c2ff0f581300a843650d7434c7c526c84
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59545625"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Доступ к API Служб мультимедиа Azure с помощью Azure CLI
 
Чтобы подключиться к API Служб мультимедиа Azure, необходимо настроить аутентификацию субъекта-службы Azure AD. Приложение должно запросить токен Azure AD со следующими параметрами:

* Конечная точка клиента Azure AD.
* Универсальный код ресурса (URI) для ресурса служб мультимедиа.
* Универсальный код ресурса (URI) для ресурса REST служб мультимедиа.
* Значения приложения Azure AD: идентификатор и секрет клиента.

Дополнительные сведения см. в разделе [разработка с использованием API-интерфейсы служб мультимедиа v3](media-services-apis-overview.md).

В этой статье показано, как с помощью Azure CLI создать приложение Azure AD и субъект-службу, а также получить значения, необходимые при доступе к ресурсам Служб мультимедиа Azure.

## <a name="prerequisites"></a>Технические условия 

[Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md).

Запишите значения, которые вы использовали в качестве имени группы ресурсов и имени учетной записи Служб мультимедиа.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="see-also"></a>См. также

- [Масштабирование зарезервированных единиц мультимедиа с помощью CLI](media-reserved-units-cli-how-to.md)
- [Создание учетной записи Служб мультимедиа с помощью CLI](./scripts/cli-create-account.md) 
- [Сброс данных учетной записи с помощью CLI](./scripts/cli-reset-account-credentials.md)
- [Создание средства с помощью CLI](./scripts/cli-create-asset.md)
- [Отправка файла с помощью CLI](./scripts/cli-upload-file-asset.md)
- [Создание преобразований с помощью CLI](./scripts/cli-create-transform.md)
- [Создание заданий с помощью CLI](./scripts/cli-create-jobs.md)
- [Создание сетки событий с помощью CLI](./scripts/cli-create-event-grid.md)
- [Публикация ресурса с помощью CLI](./scripts/cli-publish-asset.md)
- [Фильтрация с помощью CLI](filters-dynamic-manifest-cli-howto.md)

## <a name="next-steps"></a>Дальнейшие действия

[Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
