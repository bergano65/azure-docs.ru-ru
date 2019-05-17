---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 761c3a9aecadd9c1eabdb586f95c47e2988720d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "65756360"
---
[Библиотека Microsoft Azure Configuration Manager для .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) содержит класс для анализа строки подключения из файла конфигурации. [Класс CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) анализирует параметры конфигурации независимо от того, где работает клиентское приложение — на настольном компьютере, мобильном устройстве, виртуальной машине Azure или в облачной службе Azure.

Для ссылки на пакет CloudConfigurationManager добавьте следующую директиву `using`:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage;
```

Ниже приведен пример, в котором показано получение строки подключения из файла конфигурации.

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Использование диспетчера конфигураций Azure не является обязательным. Вы также можете использовать API, например [класс ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) для .NET Framework.

