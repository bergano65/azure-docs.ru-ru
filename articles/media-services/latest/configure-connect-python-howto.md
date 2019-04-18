---
title: Подключение к API v3 служб мультимедиа Azure - Python
description: Узнайте, как подключиться к API служб мультимедиа версии 3 с Python.
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
ms.openlocfilehash: 971e36b600a2c6be516e39ce84ca5780a2f23bbd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59700217"
---
# <a name="connect-to-media-services-v3-api---python"></a>Подключение к API v3 служб мультимедиа - Python

В этой статье показано, как соединиться с Python версии 3 пакета SDK служб мультимедиа Azure с помощью субъекта-службы входа, в методе.

## <a name="prerequisites"></a>Технические условия

- Скачать Python [python.org](https://www.python.org/downloads/)
- Не забудьте задать `PATH` переменной среды
- [Создание учетной записи Служб мультимедиа](create-account-cli-how-to.md). Обязательно запомните имя группы ресурсов и имя учетной записи служб мультимедиа.
- Выполните действия, описанные в [доступ к API](access-api-cli-how-to.md) раздела. Запишите идентификатор подписки, идентификатор приложения (идентификатор клиента), ключ проверки подлинности (секрет) и идентификатор клиента, вам потребуется в дальнейшем.

## <a name="install-the-modules"></a>Установка модулей

Для работы со службами мультимедиа Azure с помощью Python, необходимо установить эти модули.

* `azure-mgmt-resource` Модуль, который включает в себя модули Azure для Active Directory.
* `azure-mgmt-media` Модуль, который включает в себя сущности служб мультимедиа.

Откройте средство командной строки и используйте следующие команды для установки модулей.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Подключиться к клиенту Python

1. Создайте файл с `.py` расширения
1. Откройте файл в любом редакторе
1. Добавьте в файл в следующем коде. Код импортирует необходимые модули и создает объект учетных данных Active Directory, необходимые для подключения к службам мультимедиа.

      Задать значения этих переменных на значения, полученные из [доступ к API](access-api-cli-how-to.md)

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

1. Запустите файл

## <a name="next-steps"></a>Дальнейшие действия

- Воспользуйтесь [пакетом SDK для Python](https://aka.ms/ams-v3-python-sdk).
- Ознакомьтесь со справочной документации по [Python](https://aka.ms/ams-v3-python-ref) Служб мультимедиа.
