---
title: включение файла
description: включение файла
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: b0f93f950b55052ea8d8b31538c47226413dc82a
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66155763"
---
## <a name="access-the-media-services-api"></a>Доступ к API Служб мультимедиа.

Чтобы подключиться к API Служб мультимедиа Azure, необходимо настроить аутентификацию субъекта-службы Azure AD. Следующая команда создает приложение Azure AD и подключает субъект-службу к учетной записи. Возвращаемые значения используются для настройки приложения.

Перед запуском сценария можно заменить `amsaccount` и `amsResourceGroup` именами, выбранными при создании этих ресурсов. `amsaccount` — имя учетной записи Служб мультимедиа Azure, к которой нужно присоединить субъект-службу.

Приведенная ниже команда возвращает выходные данные `json`:

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

Ответ при выполнении этой команды примерно следующий:

```json
{
  "AadClientId": "00000000-0000-0000-0000-000000000000",
  "AadEndpoint": "https://login.microsoftonline.com",
  "AadSecret": "00000000-0000-0000-0000-000000000000",
  "AadTenantId": "00000000-0000-0000-0000-000000000000",
  "AccountName": "amsaccount",
  "ArmAadAudience": "https://management.core.windows.net/",
  "ArmEndpoint": "https://management.azure.com/",
  "Region": "West US 2",
  "ResourceGroup": "amsResourceGroup",
  "SubscriptionId": "00000000-0000-0000-0000-000000000000"
}
```

Если вам нужно получить в ответе `xml`, используйте следующую команду:

```azurecli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
