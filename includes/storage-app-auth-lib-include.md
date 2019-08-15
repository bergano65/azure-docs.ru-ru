---
title: включение файла
description: включение файла
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/18/2019
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: 5e605e4ad987db16b98649b32dc96fa1620b1564
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012000"
---
Клиентская библиотека [проверки Подлинности Microsoft Azure приложений](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) для .NET (Предварительная версия) упрощает процесс получения и обновления маркера из кода. Клиентская библиотека проверки подлинности приложений управляет проверкой подлинности автоматически. Библиотека использует учетные данные разработчика для проверки подлинности во время локальной разработки. Использовать учетные данные разработчика во время локального развертывания более безопасно, так как вам не нужно создавать учетные данные Azure AD или совместно использовать учетные данные с другими разработчиками. При последующем развертывании решения в Azure библиотека автоматически переключается на использование учетных данных приложения.

Чтобы использовать библиотеку проверки подлинности приложений в приложении службы хранилища Azure, установите последнюю предварительную версию пакета из [NuGet](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication), а также последнюю версии [общей клиентской библиотеки службы хранилища Azure для .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Common/) и [клиентской библиотеки хранилища BLOB-объектов Azure. для .NET](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/). Добавьте в код следующие операторы **using** :

```csharp
using Microsoft.Azure.Services.AppAuthentication;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
```
