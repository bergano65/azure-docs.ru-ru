---
title: Настройка SSL в базе данных Azure для PostgreSQL — масштабирование (Цитус)
description: Инструкции и сведения о настройке базы данных Azure для PostgreSQL-Scale (Цитус) и связанных приложений для правильного использования SSL-соединений.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: be02c9114f30a63fe710cfd2fc56347bfc68fab5
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273717"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>Настройка SSL в базе данных Azure для PostgreSQL — масштабирование (Цитус)
Для подключения клиентского приложения к узлу координатора Цитус требуется SSL (SSL). Применение SSL-соединений между сервером базы данных и клиентскими приложениями помогает защититься от атак типа "злоумышленник в середине" путем шифрования потока данных между сервером и приложением.

## <a name="enforcing-ssl-connections"></a>Применение SSL-соединений
Для всех серверов базы данных Azure для PostgreSQL, подготовленных с помощью портал Azure, принудительное применение SSL-соединений включено по умолчанию. 

Аналогичным образом предопределенные строки подключения в разделе "Строки подключения" в настройках сервера на портале Azure содержат необходимые параметры для распространенных языков, что позволяет подключиться к серверу базы данных с помощью SSL. Значение параметра SSL зависит от соединителя. Например, может использоваться "ssl=true", "sslmode=require", "sslmode=required" или другой вариант.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Проверка поддержки SSL-соединений в приложении или платформе
Некоторые платформы приложений, использующие PostgreSQL для своих служб баз данных, по умолчанию не поддерживают SSL во время установки. Если сервер PostgreSQL применяет SSL-подключения, но приложение не настроено для использования SSL, приложение может не подключиться к серверу базы данных. Сведения о включении SSL-соединений можно найти в документации приложения.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Приложения, требующие проверки сертификата для SSL-соединений
В некоторых случаях для безопасного подключения приложениям требуется локальный файл сертификата, созданный из файла сертификата (CER-файла) доверенного центра сертификации. Сертификат для подключения к базе данных Azure для PostgreSQL-Scale (Цитус) находится в папке https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Скачайте файл сертификата и сохраните его в предпочтительном расположении.

### <a name="connect-using-psql"></a>Подключение с помощью psql
В следующем примере показано, как подключиться к узлу координатора Цитус с помощью служебной программы командной строки psql. Используйте параметр строки подключения для принудительной проверки SSL-сертификата. `sslmode=verify-full` Передайте путь `sslrootcert` к файлу локального сертификата параметру.

Ниже приведен пример строки подключения psql.
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Убедитесь, что значение, передаваемое в `sslrootcert` , соответствует пути к файлу сохраненного сертификата.

## <a name="next-steps"></a>Следующие шаги
Повысьте безопасность с помощью [правил брандмауэра в базе данных Azure для PostgreSQL-Scale (Цитус)](concepts-hyperscale-firewall-rules.md).
