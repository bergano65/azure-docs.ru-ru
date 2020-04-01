---
title: TLS - Гипермасштаб (Citus) - База данных Azure для PostgreS'L
description: Инструкции и информация для настройки базы данных Azure для PostgreS-L - Hyperscale (Citus) и связанных с ними приложений для правильного использования подключений TLS.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 8a691b6d3f28800ab1cb38a72f124e950d485084
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422340"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Налаживание TLS в базу данных Azure для PostgreS'L - Гипермасштаб (Citus)
Подключение клиентских приложений к узлам координатора Hyperscale (Citus) требует безопасности уровня транспорта (TLS), ранее известного как Безопасный слой розеток (SSL). Обеспечение подключения TLS между сервером базы данных и клиентскими приложениями помогает защититься от атак «человек в середине» путем шифрования потока данных между сервером и приложением.

## <a name="enforcing-tls-connections"></a>Обеспечение подключения TLS
Для всех серверов Azure Database для серверов PostgreS'L, подготовленных через портал Azure, применение tLS-соединений включено по умолчанию. 

Аналогичным образом, строки соединения, которые предварительно определены в настройках "Строки подключения" под вашим сервером на портале Azure, включают необходимые параметры для подключения общих языков к серверу базы данных с помощью TLS. Параметр TLS варьируется в зависимости от разъема, например, "ssl'true" или "sslmode-require" или "sslmode-required" и других вариаций.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Убедитесь, что ваше приложение или фреймворк поддерживает соединения TLS
Некоторые платформы приложений, которые используют PostgreS'L для своих служб баз данных, не включают TLS по умолчанию во время установки. Если ваш сервер PostgreS'L обеспечивает подключение TLS, но приложение не настроено для TLS, приложение может не подключиться к серверу базы данных. Проконсультируйтесь с документацией приложения, чтобы узнать, как включить tLS-соединения.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Приложения, требующие проверки сертификата для подключения TLS
В некоторых случаях для безопасного подключения приложениям требуется локальный файл сертификата, созданный из файла сертификата (CER-файла) доверенного центра сертификации. Сертификат для подключения к базе данных Azure для PostgreS-L - https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pemHyperscale (Citus) находится по адресу . Загрузите файл сертификата и сохраните его в предпочтительном месте.

### <a name="connect-using-psql"></a>Подключение с помощью psql
В следующем примере показано, как подключиться к узлам координатора Hyperscale (Citus) с помощью утилиты командной строки psql. Используйте `sslmode=verify-full` настройки строки соединения для обеспечения проверки сертификата TLS. Передайте локальный путь `sslrootcert` файла сертификата параметру.

Ниже приведен пример строки соединения psql:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Подтвердите, `sslrootcert` что значение, передаваемые спутит с пути файла для сохраненного сертификата.

## <a name="next-steps"></a>Следующие шаги
Увеличьте безопасность с помощью [правил firewall в базе данных Azure для PostgreS-L - Hyperscale (Citus).](concepts-hyperscale-firewall-rules.md)
