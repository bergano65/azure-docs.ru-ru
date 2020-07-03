---
author: yegu-ms
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: yegu
ms.openlocfilehash: 61e93e3700b9a396d2ac4fdcbb51fc5c874cf9cb
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/26/2020
ms.locfileid: "68286245"
---
Приложения .NET могут использовать клиент кэша **StackExchange.Redis** , который можно настроить в Visual Studio, используя пакет NuGet, упрощающий настройку клиентских приложений кэша. 

> [!NOTE]
> Дополнительные сведения см. на странице GitHub, посвященной [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis), и в [документации по клиенту кэша Redis StackExchange.Azure](https://github.com/StackExchange/StackExchange.Redis#documentation).
>
>

Чтобы настроить использование пакета NuGet StackExchange.Redis клиентским приложением в Visual Studio, щелкните правой кнопкой мыши проект в **обозревателе решений** и выберите пункт **Управление пакетами NuGet**. 

![Управление пакетами NuGet](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-manage-nuget-menu.png)

Введите в поле поиска **StackExchange.Redis** или **StackExchange.Redis.StrongName**, выберите в результатах нужную версию и щелкните **Установить**.

> [!NOTE]
> Если требуется использовать версию клиентской библиотеки **StackExchange.Redis** со строгим именем, выберите **StackExchange.Redis.StrongName**; в противном случае выберите **StackExchange.Redis**.
>
>

![Пакет NuGet StackExchange.Redis](media/redis-cache-configure-stackexchange-redis-nuget/redis-cache-stackexchange-redis.png)

Пакет NuGet загружает и добавляет необходимые ссылки на сборки в клиентском приложении для доступа к кэшу Redis для Azure из клиента StackExchange.Azure кэша Redis.

> [!NOTE]
> Если ранее вы настроили проект для использования StackExchange.Redis, то можете проверять наличие обновлений для пакета с помощью **диспетчера пакетов NuGet**. Чтобы проверить наличие обновленных версий пакета NuGet StackExchange.Redis и установить их, щелкните **Updates** (Обновления) в окне **диспетчера пакетов NuGet**. Если доступно обновление для пакета NuGet для StackExchange.Redis, то можно обновить проект, чтобы использовать эту обновленную версию.
>
>

Вы также можете установить пакет StackExchange.Redis NuGet, щелкнув в меню **Средства****Диспетчер пакетов NuGet** > **Консоль диспетчера пакетов**, а затем выполнив в окне **консоли диспетчера пакетов** следующую команду.

```
Install-Package StackExchange.Redis
```
