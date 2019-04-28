---
title: включение файла
description: включение файла
services: media-services
author: WenJason
ms.service: media-services
ms.topic: include
origin.date: 05/29/2018
ms.date: 06/25/2018
ms.author: v-nany
ms.custom: include file
ms.openlocfilehash: 4dde0a47f0452da2dd951df86ccb6e02a44521ed
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60309077"
---
## <a name="access-the-media-services-api"></a>Доступ к API Служб мультимедиа.

Чтобы подключиться к API Служб мультимедиа Azure, необходимо настроить аутентификацию субъекта-службы Azure AD. Следующая команда создает приложение Azure AD и подключает субъект-службу к учетной записи. Возвращаемые значения используются для настройки приложения.

Перед запуском скрипта можно заменить `amsaccount` и `amsResourceGroup` именами, выбранными при создании этих ресурсов. `amsaccount` — имя учетной записи Служб мультимедиа Azure, к которой нужно присоединить субъект-службу.

Приведенная ниже команда возвращает выходные данные `json`:

```cli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup
```

Ответ при выполнении этой команды примерно следующий:

```json
{
  "AadClientId": "00000000-4cdd-418a-8a72-0755ace03de5",
  "AadEndpoint": "https://login.partner.microsoftonline.cn",
  "AadSecret": "00000000-02f5-4bf2-9057-1c4f7baff155",
  "AadTenantId": "00000000-86f1-41af-91ab-2d7cd011db47",
  "AccountName": "amsaccount22",
  "ArmAadAudience": "https://management.core.chinacloudapi.cn/",
  "ArmEndpoint": "https://management.chinacloudapi.cn/",
  "Region": "chinaeast",
  "ResourceGroup": "amsResourceGroup2",
  "SubscriptionId": "00000000-6753-4ca2-b1ae-193798e2c9d8"
}
```

Если вам нужно получить в ответе `xml`, используйте следующую команду:

```cli
az ams account sp create --account-name amsaccount --resource-group amsResourceGroup --xml
```
