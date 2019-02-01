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
ms.openlocfilehash: 43f9443e4b5cd700500bd9803f2737ed9e0aa633
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223173"
---
# <a name="access-azure-media-services-api-with-the-azure-cli"></a>Доступ к API Служб мультимедиа Azure с помощью Azure CLI
 
Чтобы подключиться к API Служб мультимедиа Azure, необходимо настроить аутентификацию субъекта-службы Azure AD. Приложение должно запросить токен Azure AD со следующими параметрами:

* Конечная точка клиента Azure AD.
* Универсальный код ресурса (URI) для ресурса служб мультимедиа.
* Универсальный код ресурса (URI) для ресурса REST служб мультимедиа.
* Значения приложения Azure AD: идентификатор и секрет клиента.

В этой статье показано, как с помощью Azure CLI создать приложение Azure AD и субъект-службу, а также получить значения, необходимые при доступе к ресурсам Служб мультимедиа Azure.

## <a name="prerequisites"></a>Предварительные требования 

[Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md).

Запишите значения, которые вы использовали в качестве имени группы ресурсов и имени учетной записи Служб мультимедиа.
 
[!INCLUDE [media-services-cli-instructions](../../../includes/media-services-cli-instructions.md)]

[!INCLUDE [media-services-v3-cli-access-api-include](../../../includes/media-services-v3-cli-access-api-include.md)]

## <a name="next-steps"></a>Дополнительная информация

[Краткое руководство по потоковой передаче видеофайлов — .NET](stream-files-dotnet-quickstart.md)

## <a name="see-also"></a>См. также

[Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/ams?view=azure-cli-latest)
