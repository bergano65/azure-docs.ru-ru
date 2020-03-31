---
title: включить файл
description: включить файл
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 11/26/2019
ms.author: tamram
ms.custom: include
ms.openlocfilehash: de79ea50d12ab322d1e28d0ad650df30ecc0c222
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "74806594"
---
## <a name="install-client-library-packages"></a>Установка пакетов клиентской библиотеки

> [!NOTE]
> Примеры, приведенные здесь, используют версию 12 библиотеки клиентской библиотеки Azure Storage. Библиотека клиентов версии 12 является частью SDK Azure. Более подробную информацию о SDK Azure можно узнать в репозитории Azure SDK на [GitHub.](https://github.com/Azure/azure-sdk)

Чтобы установить пакет хранения Blob, запустите следующую команду с консоли менеджера пакетов NuGet:

```powershell
Install-Package Azure.Storage.Blobs
```

Примеры, приведенные здесь, также используют последнюю версию [клиентской библиотеки Azure Identity для проверки](https://www.nuget.org/packages/Azure.Identity/) подлинности с помощью учетных данных Azure AD. Чтобы установить пакет, запустите следующую команду из консоли менеджера пакетов NuGet:

```powershell
Install-Package Azure.Identity
```
