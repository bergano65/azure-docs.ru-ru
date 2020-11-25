---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: cedfd719a5f0aeed6fc2e932d3aa5189b83c9796
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027213"
---
[Библиотека Microsoft Azure Configuration Manager для .NET](https://www.nuget.org/packages/Microsoft.Azure.ConfigurationManager/) содержит класс для анализа строки подключения из файла конфигурации. Класс [CloudConfigurationManager](/previous-versions/azure/reference/mt634650(v=azure.100)) анализирует параметры конфигурации. Он анализирует параметры клиентских приложений, которые выполняются на рабочем столе, на мобильном устройстве, в виртуальной машине Azure или в облачной службе Azure.

Чтобы создать ссылку на `CloudConfigurationManager` пакет, добавьте следующие `using` директивы:

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