---
title: 'Настройка SSL-соединения в базе данных Azure для PostgreSQL: один сервер'
description: 'Инструкции и сведения о настройке базы данных Azure для PostgreSQL: один сервер и связанных приложений для правильного использования SSL-подключения.'
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 686adfb2998eff10ef4b9f378163b164ba970c56
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461840"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Настройка SSL-соединения в базе данных Azure для PostgreSQL: один сервер
База данных Azure для PostgreSQL предпочитает подключать клиентские приложения к службе PostgreSQL с помощью SSL (Secure Sockets Layer). Применение SSL-соединений между сервером базы данных и клиентскими приложениями обеспечивает защиту от атак «man-in--middle» счет шифрования потока данных между сервером и приложения.

По умолчанию в службе базы данных PostgreSQL настроено обязательное использование SSL-соединения. Вы можете отключить обязательное использование SSL, если клиентское приложение не поддерживает SSL-соединений. 

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
Для некоторых платформ приложений, использующие PostgreSQL для служб базы данных не включают SSL по умолчанию во время установки. Если серверу PostgreSQL ограничивает SSL-соединений, но приложение не настроено для использования протокола SSL, приложение может завершиться ошибкой для подключения к серверу базы данных. Сведения о включении SSL-соединений можно найти в документации приложения.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Приложения, требующие проверки сертификата для SSL-соединений
В некоторых случаях для безопасного подключения приложениям требуется локальный файл сертификата, созданный из файла сертификата (CER-файла) доверенного центра сертификации. Сертификат для подключения к базе данных Azure для сервера PostgreSQL находится в https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Загрузите файл сертификата и сохраните его для предпочтительного расположения. 

### <a name="connect-using-psql"></a>Подключение с помощью psql
В следующем примере показано, как подключиться к серверу PostgreSQL с помощью служебной программы командной строки psql. Используйте `sslmode=verify-full` строку подключения для принудительного применения проверки сертификата SSL. Передайте путь к файлу локального сертификата `sslrootcert` параметра.

Ниже приведен пример строки подключения psql:
```
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Убедитесь, что значение, передаваемое в `sslrootcert` соответствующий путь к файлу для сохранения сертификата.


## <a name="next-steps"></a>Дальнейшие действия
Просмотрите вариантах подключения приложений в [библиотеки подключений для базы данных Azure для PostgreSQL](concepts-connection-libraries.md).
