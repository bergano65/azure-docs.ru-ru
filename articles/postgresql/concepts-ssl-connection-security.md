---
title: TLS — база данных Azure для PostgreSQL — один сервер
description: Инструкции и сведения о настройке подключения TLS для базы данных Azure для PostgreSQL-Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: d0482e5205b97b5c57c41e0ba98fb9ca819e5d5f
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82141750"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>Настройка подключения TLS в базе данных Azure для PostgreSQL — один сервер

База данных Azure для PostgreSQL предпочитает подключение клиентских приложений к службе PostgreSQL с помощью протокола TLS, ранее известного как SSL (SSL). Применение TLS-подключений между сервером базы данных и клиентскими приложениями помогает защититься от атак типа "злоумышленник в середине" путем шифрования потока данных между сервером и приложением.

По умолчанию служба базы данных PostgreSQL настроена для обязательного подключения TLS. Если клиентское приложение не поддерживает TLS подключения, можно отключить обязательное применение TLS.

## <a name="enforcing-tls-connections"></a>Принудительное подключение TLS

Для всех серверов базы данных Azure для PostgreSQL, подготовленных с помощью портал Azure и CLI, принудительное применение TLS-подключений включено по умолчанию. 

Аналогичным образом строки подключения, предварительно определенные в параметрах "строки подключения" на сервере в портал Azure, включают необходимые параметры для общих языков для подключения к серверу базы данных с помощью TLS. Параметр TLS зависит от соединителя, например "SSL = true" или "sslmode = Required" или "sslmode = обязательно" и другие варианты.

## <a name="configure-enforcement-of-tls"></a>Настройка принудительного применения TLS

При необходимости можно отключить принудительное подключение TLS. Для повышения безопасности Microsoft Azure рекомендует всегда включать параметр **Enforce SSL connection** (Применять SSL-соединение).

### <a name="using-the-azure-portal"></a>Использование портала Azure

Войдите в сервер базы данных Azure для PostgreSQL и щелкните **Безопасность подключения**. Воспользуйтесь переключателем для включения или отключения параметра **Enforce SSL connection** (Применять SSL-соединение). Затем нажмите кнопку **Сохранить**.

![Безопасность подключения — отключить принудительное применение TLS/SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Чтобы проверить правильность настройки, на странице **Обзор** просмотрите индикатор **SSL enforce status** (Состояние применения SSL).

### <a name="using-azure-cli"></a>Использование Azure CLI

Вы также можете включить или отключить параметр **ssl-enforcement** с помощью Azure CLI, используя значения `Enabled` и `Disabled` соответственно.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Убедитесь, что приложение или платформа поддерживает подключения TLS.

Некоторые платформы приложений, использующие PostgreSQL для своих служб баз данных, по умолчанию не включают TLS во время установки. Если сервер PostgreSQL применяет TLS-подключения, но приложение не настроено для TLS, приложение может не подключиться к серверу базы данных. Чтобы узнать, как включить TLS, ознакомьтесь с документацией по приложению.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Приложения, для которых требуется проверка сертификата для подключения TLS

В некоторых случаях для безопасного подключения приложениям требуется локальный файл сертификата, созданный из файла сертификата (CER-файла) доверенного центра сертификации. Сертификат для подключения к серверу базы данных Azure для PostgreSQL находится по адресу https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Скачайте файл сертификата и сохраните его в предпочтительном расположении.

### <a name="connect-using-psql"></a>Подключение с помощью psql

В следующем примере показано, как подключиться к серверу PostgreSQL с помощью служебной программы командной строки psql. Используйте параметр `sslmode=verify-full` строки подключения для принудительной проверки сертификата TLS/SSL. Передайте путь к файлу локального сертификата `sslrootcert` параметру.

Следующая команда является примером строки подключения psql:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Убедитесь, что значение, передаваемое в `sslrootcert` , соответствует пути к файлу сохраненного сертификата.

## <a name="next-steps"></a>Дальнейшие шаги

Ознакомьтесь с различными вариантами подключения приложений в [библиотеках подключений для базы данных Azure для PostgreSQL](concepts-connection-libraries.md).
