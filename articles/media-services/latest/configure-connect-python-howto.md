---
title: Подключение к API-интерфейсу служб мультимедиа Azure v3 — Python
description: Узнайте, как подключиться к API служб мультимедиа v3 с помощью Python.
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
ms.openlocfilehash: 2ceebd88f4988f23bf9cd32bd827aaca67d70461
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70307859"
---
# <a name="connect-to-media-services-v3-api---python"></a>Подключение к API служб мультимедиа v3 — Python

В этой статье показано, как подключиться к пакету SDK для служб мультимедиа Azure v3 для Python с помощью метода входа субъекта-службы.

## <a name="prerequisites"></a>Предварительные требования

- Скачать Python из [Python.org](https://www.python.org/downloads/)
- Обязательно задайте `PATH` переменную среды.
- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md). Обязательно запомните имя группы ресурсов и имя учетной записи служб мультимедиа.
- Выполните действия, описанные в разделе [API Access](access-api-cli-how-to.md) . Запишите идентификатор подписки, идентификатор приложения (идентификатор клиента), ключ проверки подлинности (секрет) и идентификатор клиента, необходимые на следующем шаге.

> [!IMPORTANT]
> Проверьте [соглашения об именовании](media-services-apis-overview.md#naming-conventions).

## <a name="install-the-modules"></a>Установка модулей

Для работы со службами мультимедиа Azure с помощью Python необходимо установить эти модули.

* `azure-mgmt-resource` Модуль, который включает модули Azure для Active Directory.
* `azure-mgmt-media` Модуль, который включает сущности служб мультимедиа.

Откройте программу командной строки и используйте следующие команды для установки модулей.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Подключение к клиенту Python

1. Создание файла с `.py` расширением
1. Откройте файл в любимом редакторе.
1. Добавьте следующий код в файл. Код импортирует необходимые модули и создает объект учетных данных Active Directory, необходимый для подключения к службам мультимедиа.

      Задайте значения переменных, полученные из [API доступа](access-api-cli-how-to.md) .

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

1. Запуск файла

## <a name="next-steps"></a>Следующие шаги

- Воспользуйтесь [пакетом SDK для Python](https://aka.ms/ams-v3-python-sdk).
- Ознакомьтесь со справочной документации по [Python](https://aka.ms/ams-v3-python-ref) Служб мультимедиа.
