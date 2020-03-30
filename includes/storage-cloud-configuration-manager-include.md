---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5102866cdda51ef545fd95b32946cb17c6e40a3c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72038171"
---
[Библиотека Microsoft Azure Configuration Manager для .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) содержит класс для анализа строки подключения из файла конфигурации. Класс [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) разбирает настройки конфигурации. Он разбирает настройки для клиентских приложений, работающих на рабочем столе, на мобильном устройстве, в виртуальном компьютере Azure или в облачном сервисе Azure.

Чтобы ссылаться на `CloudConfigurationManager` пакет, добавьте следующие `using` директивы:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.Azure.Storage;
```

Ниже приведен пример, в котором показано получение строки подключения из файла конфигурации.

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Использование диспетчера конфигураций Azure не является обязательным. Вы также можете использовать API, [например, класс ConfigurationManager.NET](/dotnet/api/system.configuration.configurationmanager)Framework.
