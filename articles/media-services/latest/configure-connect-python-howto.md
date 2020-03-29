---
title: Подключение к Azure Media Services v3 API - Python
description: В этой статье показано, как подключиться к Media Services v3 API с Python.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 98a8cdf4120cf56184eb5735249640e3423acdf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888467"
---
# <a name="connect-to-media-services-v3-api---python"></a>Подключение к Медиа-сервисам v3 API - Python

В этой статье показано, как подключиться к Сервису мультимедиа Azure s3 Python SDK с помощью основного знака службы в методе.

## <a name="prerequisites"></a>Предварительные требования

- Скачать Python из [python.org](https://www.python.org/downloads/)
- Убедитесь в `PATH` том, чтобы установить переменную среды
- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md). Не забудьте запомнить имя группы ресурсов и имя учетной записи Media Services.
- Выполните действия в теме [APIs доступа.](access-api-cli-how-to.md) Запись идентификатора подписки, идентификатора приложения (идентификаторклиента клиента), ключа проверки подлинности (секретного) и идентификатора клиента, который вам нужен на более позднем этапе.

> [!IMPORTANT]
> Просмотр [именования конвенций](media-services-apis-overview.md#naming-conventions).

## <a name="install-the-modules"></a>Установка модулей

Для работы с медиа-службами Azure с помощью Python необходимо установить эти модули.

* Модуль, `azure-mgmt-resource` который включает модули Azure для Active Directory.
* Модуль, `azure-mgmt-media` который включает в себя средства массовой информации лиц.

Откройте инструмент командной строки и используйте следующие команды для установки модулей.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Подключение к клиенту Python

1. Создание файла `.py` с расширением
1. Откройте файл в любимом редакторе
1. Добавьте код, который следует за файлом. Код импортирует необходимые модули и создает объект учетных данных Active Directory, который необходимо подключить к медиа-службам.

      Установите значения переменных в значения, полученные из [ApIs Access](access-api-cli-how-to.md)

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. Выполнить файл

## <a name="next-steps"></a>Дальнейшие действия

- Воспользуйтесь [пакетом SDK для Python](https://aka.ms/ams-v3-python-sdk).
- Ознакомьтесь со справочной документации по [Python](https://aka.ms/ams-v3-python-ref) Служб мультимедиа.
