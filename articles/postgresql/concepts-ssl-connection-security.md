---
title: SSL — база данных Azure для PostgreSQL — один сервер
description: Инструкции и сведения о настройке SSL-подключений для базы данных Azure для PostgreSQL — Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 21b4dffa135e1311be8c738c634de22304665695
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768152"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Настройка SSL-подключений в базе данных Azure для PostgreSQL — один сервер
База данных Azure для PostgreSQL предпочитает подключать клиентские приложения к службе PostgreSQL с помощью SSL (Secure Sockets Layer). Применение SSL-соединений между сервером базы данных и клиентскими приложениями помогает защититься от атак типа "злоумышленник в середине" путем шифрования потока данных между сервером и приложением.

По умолчанию в службе базы данных PostgreSQL настроено обязательное использование SSL-соединения. Если клиентское приложение не поддерживает SSL-подключение, можно отключить обязательное использование SSL. 

## <a name="enforcing-ssl-connections"></a>Применение SSL-соединений
Для всех серверов базы данных Azure для PostgreSQL, подготовленных с помощью портала Azure и интерфейса командной строки, применение SSL-соединений включается по умолчанию. 

Аналогичным образом предопределенные строки подключения в разделе "Строки подключения" в настройках сервера на портале Azure содержат необходимые параметры для распространенных языков, что позволяет подключиться к серверу базы данных с помощью SSL. Значение параметра SSL зависит от соединителя. Например, может использоваться "ssl=true", "sslmode=require", "sslmode=required" или другой вариант.

## <a name="configure-enforcement-of-ssl"></a>Настройка применения SSL
При необходимости применение SSL-соединений можно отключить. Для повышения безопасности Microsoft Azure рекомендует всегда включать параметр **Enforce SSL connection** (Применять SSL-соединение).

### <a name="using-the-azure-portal"></a>Использование портала Azure
Войдите в сервер базы данных Azure для PostgreSQL и щелкните **Безопасность подключения**. Воспользуйтесь переключателем для включения или отключения параметра **Enforce SSL connection** (Применять SSL-соединение). Нажмите кнопку **Сохранить**. 

![Безопасность подключения: отключить применение SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Чтобы проверить правильность настройки, на странице **Обзор** просмотрите индикатор **SSL enforce status** (Состояние применения SSL).

### <a name="using-azure-cli"></a>Использование Azure CLI
Вы также можете включить или отключить параметр **ssl-enforcement** с помощью Azure CLI, используя значения `Enabled` и `Disabled` соответственно.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Проверка поддержки SSL-соединений в приложении или платформе
Некоторые платформы приложений, использующие PostgreSQL для своих служб баз данных, по умолчанию не поддерживают SSL во время установки. Если сервер PostgreSQL применяет SSL-подключения, но приложение не настроено для использования SSL, приложение может не подключиться к серверу базы данных. Сведения о включении SSL-соединений можно найти в документации приложения.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Приложения, требующие проверки сертификата для SSL-соединений
В некоторых случаях для безопасного подключения приложениям требуется локальный файл сертификата, созданный из файла сертификата (CER-файла) доверенного центра сертификации. Сертификат для подключения к серверу базы данных Azure для PostgreSQL находится по адресу https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Скачайте файл сертификата и сохраните его в предпочтительном расположении. 

### <a name="connect-using-psql"></a>Подключение с помощью psql
В следующем примере показано, как подключиться к серверу PostgreSQL с помощью служебной программы командной строки psql. Используйте параметр строки подключения `sslmode=verify-full` для принудительной проверки SSL-сертификата. Передайте путь к локальному файлу сертификата в параметр `sslrootcert`.

Ниже приведен пример строки подключения psql.
```
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Убедитесь, что значение, передаваемое в `sslrootcert`, совпадает с путем к файлу сохраненного сертификата.


## <a name="next-steps"></a>Дальнейшие действия
Ознакомьтесь с различными вариантами подключения приложений в [библиотеках подключений для базы данных Azure для PostgreSQL](concepts-connection-libraries.md).
