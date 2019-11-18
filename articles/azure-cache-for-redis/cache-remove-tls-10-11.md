---
title: Удаление TLS 1,0 и 1,1 из использования с кэшем Azure для Redis
description: Узнайте, как удалить TLS 1,0 и 1,1 из приложения при взаимодействии с кэшем Azure для Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: 585828855cdbd3a585ced1aa6803482c3f26f0f4
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121576"
---
# <a name="remove-tls-10-and-11-from-use-with-azure-cache-for-redis"></a>Удаление TLS 1,0 и 1,1 из использования с кэшем Azure для Redis

В рамках всей отрасли в отношении эксклюзивного использования протокола TLS версии 1,2 или более поздней. Известно, что протоколы TLS версий 1,0 и 1,1 подвержены атакам, таким как МОНСТРУ и пудель, и имеют другие распространенные слабости и уязвимости (CVE). Кроме того, они не поддерживают современные методы шифрования и комплекты шифров, Рекомендуемые в соответствии со стандартами соответствия требованиям индустрии платежных карт (PCI). Этот [блог по безопасности TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) подробно описывает некоторые из этих уязвимостей.

Хотя ни одно из этих рекомендаций не дает немедленной проблемы, рекомендуется больше не использовать TLS 1,0 и 1,1. Кэш Azure для Redis будет прекращать поддержку этих версий TLS до 31 марта 2020 г. После этой даты приложению потребуется TLS 1,2 или более поздней версии для взаимодействия с кэшем.

В этой статье содержатся общие рекомендации по обнаружению зависимостей от предыдущих версий TLS и их удаление из приложения.

## <a name="check-whether-your-application-is-already-compliant"></a>Проверьте, соответствует ли ваше приложение требованиям

Самый простой способ узнать, будет ли приложение работать с TLS 1,2, — установить значение **минимальной версии TLS** для TLS 1,2 в тестируемом или промежуточном кэше, который он использует. Параметр **минимальной версии TLS** находится в [дополнительных параметрах](cache-configure.md#advanced-settings) экземпляра кэша в портал Azure. Если после этого изменения приложение продолжит работать должным образом, это, вероятно, соответствует требованиям. Может потребоваться настроить некоторые клиентские библиотеки Redis, используемые приложением специально для включения TLS 1,2, чтобы они могли подключаться к кэшу Azure для Redis по протоколу безопасности.

## <a name="configure-your-application-to-use-tls-12"></a>Настройка приложения для использования TLS 1,2

Большинство приложений используют клиентские библиотеки Redis для управления связью с их кэшами. Ниже приведены инструкции по настройке некоторых популярных клиентских библиотек, в различных языках программирования и платформах, для использования TLS 1,2.

### <a name="net-framework"></a>.NET Framework

Клиенты Redis .NET по умолчанию используют самую раннюю версию TLS в .NET Framework 4.5.2 или более ранней версии и используют последнюю версию TLS на .NET Framework 4,6 или более поздней версии. Если вы используете более старую версию .NET Framework, TLS 1,2 можно включить вручную.

* **StackExchange. Redis:** Задайте `ssl=true` и `sslprotocls=tls12` в строке подключения.
* **ServiceStack. Redis:** Следуйте [инструкциям по ServiceStack. Redis](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

Клиенты Redis .NET Core по умолчанию используют последнюю версию TLS.

### <a name="java"></a>Java:

Клиенты Redis Java используют протокол TLS 1,0 в Java версии 6 или более ранней. Jedis, салат и Радиссон не могут подключиться к кэшу Azure для Redis, если TLS 1,0 отключен в кэше. В настоящее время нет известных обходных путей.

В Java 7 или более поздней версии клиенты Redis не используют TLS 1,2 по умолчанию, но могут быть настроены для него. Салат и Радиссон не поддерживают эту конфигурацию прямо сейчас. Если кэш принимает только подключения TLS 1,2, они нарушаются. Jedis позволяет указать базовые параметры TLS с помощью следующего фрагмента кода:

``` Java
SSLSocketFactory sslSocketFactory = (SSLSocketFactory) SSLSocketFactory.getDefault();
SSLParameters sslParameters = new SSLParameters();
sslParameters.setEndpointIdentificationAlgorithm("HTTPS");
sslParameters.setProtocols(new String[]{"TLSv1", "TLSv1.1", "TLSv1.2"});
 
URI uri = URI.create("rediss://host:port");
JedisShardInfo shardInfo = new JedisShardInfo(uri, sslSocketFactory, sslParameters, null);
 
shardInfo.setPassword("cachePassword");
 
Jedis jedis = new Jedis(shardInfo);
```

### <a name="nodejs"></a>Node.js

Узлы Redis и Иоредис по умолчанию используют TLS 1,2.

### <a name="php"></a>PHP

Предис на PHP 7 не будет работать, так как PHP 7 поддерживает только TLS 1,0. На PHP 7.2.1 или более ранней версии Предис по умолчанию использует TLS 1,0 или 1,1. При создании экземпляра клиента можно указать TLS 1,2.

``` PHP
$redis=newPredis\Client([
    'scheme'=>'tls',
    'host'=>'host',
    'port'=>6380,
    'password'=>'password',
    'ssl'=>[
        'crypto_type'=>STREAM_CRYPTO_METHOD_TLSv1_2_CLIENT,
    ],
]);
```

В PHP 7,3 или более поздней версии Предис использует последнюю версию TLS.

Фпредис не поддерживает TLS в любой версии PHP.

### <a name="python"></a>Python

Redis-копировать по умолчанию использует TLS 1,2.

### <a name="go"></a>GO

По умолчанию редиго использует TLS 1,2.

## <a name="additional-information"></a>Дополнительная информация

- [Настройка кэша Azure для Redis](cache-configure.md)
