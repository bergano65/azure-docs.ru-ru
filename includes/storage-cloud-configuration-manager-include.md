---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 3a065e5cd6e951544b3147d5833b4ad300ae5e30
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165187"
---
[Библиотека Microsoft Azure Configuration Manager для .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) содержит класс для анализа строки подключения из файла конфигурации. [Класс CloudConfigurationManager](https://msdn.microsoft.com/library/azure/mt634650.aspx) анализирует параметры конфигурации независимо от того, где работает клиентское приложение — на настольном компьютере, мобильном устройстве, виртуальной машине Azure или в облачной службе Azure.

Для ссылки на пакет CloudConfigurationManager добавьте следующую директиву `using`:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
```

Ниже приведен пример, в котором показано получение строки подключения из файла конфигурации.

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Использование диспетчера конфигураций Azure не является обязательным. Вы также можете использовать API, например [класс ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx) для .NET Framework.

