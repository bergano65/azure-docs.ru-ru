---
title: SSL - База данных Azure для PostgreS'L - Единый сервер
description: Инструкции и информация о том, как настроить SSL-соединение для базы данных Azure для PostgreS-L - Единый сервер.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 303da4dcb68a79e69254f6610afc0003bf0aa22c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477006"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Настройка SSL-соединения в базу данных Azure для PostgreS-L - Единый сервер

База данных Azure для PostgreSQL предпочитает подключать клиентские приложения к службе PostgreSQL с помощью SSL (Secure Sockets Layer). Обеспечение SSL-соединений между сервером базы данных и клиентскими приложениями помогает защититься от атак «человек в середине» путем шифрования потока данных между сервером и вашим приложением.

По умолчанию в службе базы данных PostgreSQL настроено обязательное использование SSL-соединения. Вы можете отключить требование SSL, если клиентское приложение не поддерживает подключение SSL.

## <a name="enforcing-ssl-connections"></a>Применение SSL-соединений

Для всех серверов базы данных Azure для PostgreSQL, подготовленных с помощью портала Azure и интерфейса командной строки, применение SSL-соединений включается по умолчанию. 

Аналогичным образом предопределенные строки подключения в разделе "Строки подключения" в настройках сервера на портале Azure содержат необходимые параметры для распространенных языков, что позволяет подключиться к серверу базы данных с помощью SSL. Значение параметра SSL зависит от соединителя. Например, может использоваться "ssl=true", "sslmode=require", "sslmode=required" или другой вариант.

## <a name="configure-enforcement-of-ssl"></a>Настройка применения SSL

При необходимости применение SSL-соединений можно отключить. Для повышения безопасности Microsoft Azure рекомендует всегда включать параметр **Enforce SSL connection** (Применять SSL-соединение).

### <a name="using-the-azure-portal"></a>Использование портала Azure

Войдите в сервер базы данных Azure для PostgreSQL и щелкните **Безопасность подключения**. Воспользуйтесь переключателем для включения или отключения параметра **Enforce SSL connection** (Применять SSL-соединение). Затем нажмите кнопку **Сохранить**.

![Безопасность подключения: отключить применение SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Чтобы проверить правильность настройки, на странице **Обзор** просмотрите индикатор **SSL enforce status** (Состояние применения SSL).

### <a name="using-azure-cli"></a>Использование Azure CLI

Вы также можете включить или отключить параметр **ssl-enforcement** с помощью Azure CLI, используя значения `Enabled` и `Disabled` соответственно.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Проверка поддержки SSL-соединений в приложении или платформе

Некоторые платформы приложений, которые используют PostgreS'L для своих служб баз данных, не включают SSL по умолчанию во время установки. Если ваш сервер PostgreS'L обеспечивает sSL-соединения, но приложение не настроено для SSL, приложение может не подключиться к серверу базы данных. Сведения о включении SSL-соединений можно найти в документации приложения.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Приложения, требующие проверки сертификата для SSL-соединений

В некоторых случаях для безопасного подключения приложениям требуется локальный файл сертификата, созданный из файла сертификата (CER-файла) доверенного центра сертификации. Сертификат для подключения к базе данных Azure для https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pemсервера PostgreS'L находится на . Загрузите файл сертификата и сохраните его в предпочтительном месте.

### <a name="connect-using-psql"></a>Подключение с помощью psql

В следующем примере показано, как подключиться к серверу PostgreS'L с помощью утилиты командной строки psql. Используйте `sslmode=verify-full` настройки строки соединения для обеспечения проверки сертификата SSL. Передайте локальный путь `sslrootcert` файла сертификата параметру.

Следующая команда является примером строки соединения psql:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Подтвердите, `sslrootcert` что значение, передаваемые спутит с пути файла для сохраненного сертификата.

## <a name="next-steps"></a>Дальнейшие действия

Просмотрите различные варианты подключения приложений в [библиотеках подключения для базы данных Azure для PostgreS'L.](concepts-connection-libraries.md)
