---
title: включение файла
description: включение файла
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: de79ea50d12ab322d1e28d0ad650df30ecc0c222
ms.sourcegitcommit: 5aefc96fd34c141275af31874700edbb829436bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74806594"
---
## <a name="install-client-library-packages"></a>Установка пакетов клиентской библиотеки

> [!NOTE]
> В примерах, показанных здесь, используется клиентская библиотека службы хранилища Azure версии 12. Клиентская библиотека версии 12 является частью пакета SDK для Azure. Дополнительные сведения о пакете SDK для Azure см. в репозитории Azure SDK на сайте [GitHub](https://github.com/Azure/azure-sdk).

Чтобы установить пакет хранилища BLOB-объектов, выполните следующую команду в консоли диспетчера пакетов NuGet:

```powershell
Install-Package Azure.Storage.Blobs
```

В примерах, показанных здесь, также используется последняя версия [клиентской библиотеки удостоверений Azure для .NET](https://www.nuget.org/packages/Azure.Identity/) для аутентификации с помощью учетных данных Azure AD. Чтобы установить пакет, выполните следующую команду в консоли диспетчера пакетов NuGet:

```powershell
Install-Package Azure.Identity
```
