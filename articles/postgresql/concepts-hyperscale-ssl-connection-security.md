---
title: TLS-Scale (Цитус) — база данных Azure для PostgreSQL
description: Инструкции и сведения о настройке базы данных Azure для PostgreSQL-Scale (Цитус) и связанных приложений для правильного использования TLS-подключений.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 791eed9419375c7245488b8ec61a1c5481be382e
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580565"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Настройка TLS в базе данных Azure для PostgreSQL — масштабирование (Цитус)
Для подключений клиентских приложений к узлу координатора Цитус () требуется протокол TLS, который ранее назывался SSL (SSL). Применение TLS-подключений между сервером базы данных и клиентскими приложениями помогает защититься от атак типа "злоумышленник в середине" путем шифрования потока данных между сервером и приложением.

## <a name="enforcing-tls-connections"></a>Принудительное подключение TLS
Для всех серверов базы данных Azure для PostgreSQL, подготовленных с помощью портал Azure, принудительное применение TLS-подключений включено по умолчанию. 

Аналогичным образом строки подключения, предварительно определенные в параметрах "строки подключения" на сервере в портал Azure, включают необходимые параметры для общих языков для подключения к серверу базы данных с помощью TLS. Параметр TLS зависит от соединителя, например "SSL = true" или "sslmode = Required" или "sslmode = обязательно" и другие варианты.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Убедитесь, что приложение или платформа поддерживает подключения TLS.
Некоторые платформы приложений, использующие PostgreSQL для своих служб баз данных, по умолчанию не включают TLS во время установки. Если сервер PostgreSQL применяет TLS-подключения, но приложение не настроено для TLS, приложение может не подключиться к серверу базы данных. Чтобы узнать, как включить TLS, ознакомьтесь с документацией по приложению.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Приложения, для которых требуется проверка сертификата для подключения TLS
В некоторых случаях для безопасного подключения приложениям требуется локальный файл сертификата, созданный из файла сертификата (CER-файла) доверенного центра сертификации. Сертификат для подключения к базе данных Azure для PostgreSQL-Scale (Цитус) находится в папке https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Скачайте файл сертификата и сохраните его в предпочтительном расположении.

### <a name="connect-using-psql"></a>Подключение с помощью psql
В следующем примере показано, как подключиться к узлу координатора Цитус с помощью служебной программы командной строки psql. Используйте параметр `sslmode=verify-full` строки подключения для принудительной проверки сертификата TLS. Передайте путь к файлу локального сертификата `sslrootcert` параметру.

Ниже приведен пример строки подключения psql.
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Убедитесь, что значение, передаваемое в `sslrootcert` , соответствует пути к файлу сохраненного сертификата.

## <a name="next-steps"></a>Дальнейшие действия
Повысьте безопасность с помощью [правил брандмауэра в базе данных Azure для PostgreSQL-Scale (Цитус)](concepts-hyperscale-firewall-rules.md).
