---
title: включить файл
description: включить файл
services: media-services
author: Juliako
ms.service: media-services
ms.topic: include
ms.date: 05/01/2019
ms.author: juliako
ms.custom: include file
ms.openlocfilehash: 9d8de8826fc3dfcc2360eb3b6c82b3ff8d65d845
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "79461237"
---
### <a name="access-the-media-services-api"></a>Доступ к API Служб мультимедиа.

Чтобы подключиться к API Служб мультимедиа Azure, необходимо настроить аутентификацию субъекта-службы Azure AD. Следующая команда создает приложение Azure AD и подключает субъект-службу к учетной записи. Возвращаемые значения используются для настройки приложения.

Перед запуском сценария можно заменить `amsaccount` и `amsResourceGroup` именами, выбранными при создании этих ресурсов. `amsaccount` — имя учетной записи Служб мультимедиа Azure, к которой нужно присоединить субъект-службу.

Если у вас есть доступ к нескольким подпискам, сначала сделайте активной подписку, в которой была создана учетная запись Служб мультимедиа.

```azurecli
az account set --subscription subscriptionId
```

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
