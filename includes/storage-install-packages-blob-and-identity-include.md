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
ms.openlocfilehash: 0adf1280fa50e9ee594f3025dff70786f5ba2199
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666174"
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

Дополнительные сведения о проверке подлинности с помощью клиентской библиотеки удостоверений Azure из службы хранилища Azure см. в разделе **Проверка подлинности с помощью библиотеки удостоверений Azure** в статье [авторизация доступа к BLOB-объектам и очередям с Azure Active Directory и управляемыми удостоверениями для ресурсов Azure](/azure/storage/common/storage-auth-aad-msi?toc=%2Fazure%2Fstorage%2Fblobs%2Ftoc.json#authenticate-with-the-azure-identity-library).