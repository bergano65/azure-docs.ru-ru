---
title: SSL - Гипермасштаб (Citus) - База данных Azure для PostgreS'L
description: Инструкции и информация для настройки базы данных Azure для PostgreS-L - Hyperscale (Citus) и связанных с ними приложений для правильного использования SSL-соединений.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 3e4ef5d2d6db3a3d4f8923f47079f2484639a751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973991"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>Налаживание SSL в базу данных Azure для PostgreS-L - Гипермасштаб (Citus)
Подключение клиентских приложений к узлам координатора Hyperscale (Citus) требует безопасных разъемов слоя (SSL). Обеспечение SSL-соединений между сервером базы данных и клиентскими приложениями помогает защититься от атак «человек в середине» путем шифрования потока данных между сервером и вашим приложением.

## <a name="enforcing-ssl-connections"></a>Применение SSL-соединений
Для всех серверов Azure Database для серверов PostgreS'L, подготовленных через портал Azure, применение SSL-соединений включено по умолчанию. 

Аналогичным образом предопределенные строки подключения в разделе "Строки подключения" в настройках сервера на портале Azure содержат необходимые параметры для распространенных языков, что позволяет подключиться к серверу базы данных с помощью SSL. Значение параметра SSL зависит от соединителя. Например, может использоваться "ssl=true", "sslmode=require", "sslmode=required" или другой вариант.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Проверка поддержки SSL-соединений в приложении или платформе
Некоторые платформы приложений, которые используют PostgreS'L для своих служб баз данных, не включают SSL по умолчанию во время установки. Если ваш сервер PostgreS'L обеспечивает sSL-соединения, но приложение не настроено для SSL, приложение может не подключиться к серверу базы данных. Сведения о включении SSL-соединений можно найти в документации приложения.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Приложения, требующие проверки сертификата для SSL-соединений
В некоторых случаях для безопасного подключения приложениям требуется локальный файл сертификата, созданный из файла сертификата (CER-файла) доверенного центра сертификации. Сертификат для подключения к базе данных Azure для PostgreS-L - https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pemHyperscale (Citus) находится по адресу . Загрузите файл сертификата и сохраните его в предпочтительном месте.

### <a name="connect-using-psql"></a>Подключение с помощью psql
В следующем примере показано, как подключиться к узлам координатора Hyperscale (Citus) с помощью утилиты командной строки psql. Используйте `sslmode=verify-full` настройки строки соединения для обеспечения проверки сертификата SSL. Передайте локальный путь `sslrootcert` файла сертификата параметру.

Ниже приведен пример строки соединения psql:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Подтвердите, `sslrootcert` что значение, передаваемые спутит с пути файла для сохраненного сертификата.

## <a name="next-steps"></a>Дальнейшие действия
Увеличьте безопасность с помощью [правил firewall в базе данных Azure для PostgreS-L - Hyperscale (Citus).](concepts-hyperscale-firewall-rules.md)
