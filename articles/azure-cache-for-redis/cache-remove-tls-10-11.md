---
title: Удаление использования TLS 1,0 и 1,1 с помощью кэша Azure для Redis | Документация Майкрософт
description: Узнайте, как удалить TLS 1,0 и 1,1 из приложения при взаимодействии с кэшем Azure для Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: maiye
editor: ''
ms.assetid: ''
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 10/22/2019
ms.author: yegu
ms.openlocfilehash: d1d44467d310b87d306ea7401e66784d684a1bf3
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72901902"
---
# <a name="remove-use-of-tls-10-and-11-with-azure-cache-for-redis"></a>Удаление использования TLS 1,0 и 1,1 с кэшем Azure для Redis

Есть ориентированная на отрасли передача по протоколу TLS 1,2 или более поздней версии. Известно, что протоколы TLS версий 1,0 и 1,1 подвержены атакам, таким как МОНСТРУ и пудель, и имеют другие распространенные слабости и уязвимости (CVE). Они также не поддерживают современные методы шифрования и комплекты шифров, рекомендованные стандартами соответствия требованиям PCI. В этом [блоге по безопасности TLS](https://www.acunetix.com/blog/articles/tls-vulnerabilities-attacks-final-part/) подробно описаны некоторые из этих уязвимостей.

Хотя ни одна из этих проблем не вызывает немедленного решения, следует рассмотреть возможность перехода с использования TLS 1,0 и 1,1 как можно раньше. Кэш Azure для Redis будет прекращать поддержку этих версий TLS, начиная с 31 марта 2020. Для взаимодействия с кэшем после этой даты приложение должно использовать по крайней мере TLS 1,2.

В этой статье содержатся общие рекомендации по обнаружению и удалению этих зависимостей из приложения.

## <a name="check-if-your-application-is-already-compliant"></a>Проверка того, что приложение уже соответствует требованиям

Самый простой способ выяснить, будет ли приложение работать с TLS 1,2, — установить минимальную версию TLS в тестовом или промежуточном кэше, который он использует для TLS 1,2. Параметр минимальной версии TLS можно найти в [дополнительных параметрах](cache-configure.md#advanced-settings) экземпляра кэша в портал Azure. Если приложение по-видимому будет работать, как ожидается после этого изменения, оно, скорее всего, будет соответствовать требованиям. Некоторые клиентские библиотеки Redis, используемые нашим приложением, могут быть настроены специально для включения TLS 1,2 для подключения к кэшу Azure для Redis по этому протоколу безопасности.

## <a name="configure-your-application-to-use-tls-12"></a>Настройка приложения для использования TLS 1,2

Большинство приложений использует клиентские библиотеки Redis для управления взаимодействием с их кэшами. Ниже приведены инструкции по настройке некоторых популярных клиентских библиотек на различных языках программирования и платформах для использования TLS 1,2.

### <a name="net-framework"></a>.NET Framework

Клиенты Redis .NET по умолчанию используют самую низкую версию TLS на .NET Framework 4.5.2 или ниже, а также максимальную версию TLS на 4,6 или более поздней версии. Если вы используете более старую версию .NET Framework, TLS 1,2 можно включить вручную.

* StackExchange. Redis: задайте `ssl=true` и `sslprotocls=tls12` в строке подключения.
* ServiceStack. Redis. выполните [эти инструкции](https://github.com/ServiceStack/ServiceStack.Redis/pull/247).

### <a name="net-core"></a>.NET Core

Клиенты Redis .NET Core по умолчанию используют наивысшую версию TLS.

### <a name="java"></a>Java:

Клиенты Redis Java используют протокол TLS 1,0 в Java версии 6 или ниже. Jedis, салат и Радиссон не смогут подключаться к кэшу Azure для Redis, если TLS 1,0 отключен в кэше. В настоящее время известных решений не существует.

На Java 7 или более поздней версии клиенты Redis не используют TLS 1,2 по умолчанию, но могут быть настроены для него. Салат и Радиссон сейчас не поддерживают это. Если кэш принимает только подключения TLS 1,2, они будут нарушены. Jedis позволяет указать базовые параметры TLS с помощью следующего фрагмента кода:

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

Предис на PHP 7 не будет работать, так как последняя поддерживает только TLS 1,0. В PHP 7.2.1 или ниже Предис по умолчанию использует TLS 1,0 или 1,1. При создании экземпляра клиента можно указать TLS 1,2.

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

## <a name="additional-information"></a>Дополнительные сведения

- [Настройка кэша Azure для Redis](cache-configure.md)
