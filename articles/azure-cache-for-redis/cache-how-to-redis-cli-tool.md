---
title: Как использовать Redis-CLI с кэшем Azure для Redis
description: Узнайте, как использовать *redis-cli.exe* в качестве средства командной строки для взаимодействия с кэшем Azure для Redis в качестве клиента.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 03/22/2018
ms.openlocfilehash: bd2da798cae92a7e47bd879b69dd108618463402
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "81010772"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Использование программы командной строки Redis с кэшем Redis для Azure

*redis cli.exe* является популярной программой командной строки для взаимодействия с кэшем Redis для Azure в качестве клиента. Ее также можно использовать с кэшем Redis для Azure.

Программа доступна для платформ Windows — скачайте [программы командной строки Redis для Windows](https://github.com/MSOpenTech/redis/releases/). 

Если вы хотите запустить программу командной строки на другой платформе, скачайте кэш Azure для Redis из [https://redis.io/download](https://redis.io/download) .

## <a name="gather-cache-access-information"></a>Сбор сведений для доступа к кэшу

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Существует три способа сбора сведений, необходимых для доступа к кэшу.

1. С помощью Azure CLI и команды [az redis list-keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys).
2. С помощью Azure PowerShell и командлета [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-AzRedisCacheKey).
3. С помощью портала Azure.

В этом разделе вы будете получать ключи с портала Azure.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Включение доступа для redis cli.exe

При использовании кэша Azure для Redis по умолчанию включается только TLS-порт (6380). `redis-cli.exe`Программа командной строки не поддерживает TLS. У вас есть два варианта конфигурации для использования программы.

1. [Включение порта, не ЯВЛЯЮЩЕГОСЯ TLS (6379)](cache-configure.md#access-ports)  -  **Такая конфигурация не рекомендуется** , так как в этой конфигурации ключи доступа отправляются через TCP в виде открытого текста. Это изменение может нарушить доступ к кэшу. Единственным сценарием, где может потребоваться эта конфигурация, является просто доступ к кэшу теста.

2. Скачайте и установите [stunnel](https://www.stunnel.org/downloads.html).

    Выполните команду **stunnel GUI Start**, чтобы запустить сервер.

    Щелкните правой кнопкой мыши значок сервера stunnel и выберите пункт **Show Log Window** (Открыть окно журнала).

    В меню окна журнала stunnel щелкните **Конфигурация**  >  **изменить конфигурацию** , чтобы открыть текущий файл конфигурации.

    В разделе **Определения службы** добавьте следующую запись для программы *redis cli.exe*. Вместо `yourcachename` вставьте фактическое имя кэша. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Сохраните и закройте файл конфигурации. 
  
    В меню окна журнала stunnel щелкните **Конфигурация**  >  **Перезагрузить конфигурацию**.


## <a name="connect-using-the-redis-command-line-tool"></a>Подключитесь с помощью программы командной строки Redis.

При использовании stunnel запустите *redis cli.exe* и для подключения к кэшу передайте только *порт* и *ключ доступа* (первичный или вторичный).

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel с redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Если вы используете тестовый кэш с **незащищенным** портом без протокола TLS, запустите `redis-cli.exe` и передайте *имя узла*, *порт*и *ключ доступа* (первичный или дополнительный) для подключения к тестовому кэшу.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel с redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Дальнейшие шаги

Узнайте больше об использовании [консоли Redis](cache-configure.md#redis-console) для выполнения команд.

