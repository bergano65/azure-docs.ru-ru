---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 5102866cdda51ef545fd95b32946cb17c6e40a3c
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038171"
---
[Библиотека Microsoft Azure Configuration Manager для .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) содержит класс для анализа строки подключения из файла конфигурации. Класс [CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) анализирует параметры конфигурации. Он анализирует параметры клиентских приложений, которые выполняются на рабочем столе, на мобильном устройстве, в виртуальной машине Azure или в облачной службе Azure.

Чтобы создать ссылку на пакет `CloudConfigurationManager`, добавьте следующие директивы `using`:

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

Использование диспетчера конфигураций Azure не является обязательным. Вы также можете использовать API, например [класс ConfigurationManager](/dotnet/api/system.configuration.configurationmanager).NET Framework.
