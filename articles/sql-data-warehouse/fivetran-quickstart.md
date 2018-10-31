---
title: Краткое руководство по использованию Fivetran и хранилища данных SQL Azure | Документация Майкрософт
description: Быстро приступите к работе с Fivetran и хранилищем данных SQL Azure.
services: sql-data-warehouse
author: hirokib
manager: jrj
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 10/12/2018
ms.author: elbutter
ms.reviewer: craigg
ms.openlocfilehash: 8e738becfe356908af5baffc0ebf225916b2616e
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2018
ms.locfileid: "49355296"
---
# <a name="get-started-quickly-with-fivetran-and-sql-data-warehouse"></a>Быстрое начало работы с Fivetran и хранилищем данных SQL

В этом кратком руководстве предполагается, что у вас имеется экземпляр хранилища данных SQL.

## <a name="setup-connection"></a>Настройка подключения

1. Найдите полное имя сервера и имя базы данных для подключения к хранилищу данных SQL Azure.

   [Как найти имя сервера и имя базы данных на портале?](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-connect-overview)

2. В мастере установки выберите подключение к базе данных напрямую или через туннель SSH.

   Если вы выбрали прямое подключения к базе данных, необходимо создать правило брандмауэра, чтобы разрешить доступ. Этот метод является наиболее простым и безопасным.

   Если вы выбрали подключение через туннель SSH, Fivetran подключится к отдельному серверу в сети, предоставляющему туннель SSH к базе данных. Этот метод необходимо использовать в том случае, если база данных находится в подсети, недоступной в виртуальной сети.

3. Добавьте IP-адрес 52.0.2.4 в брандмауэр уровня сервера, чтобы разрешить входящие подключения к хранилищу данных SQL Azure из Fivetran.

   [Как добавить брандмауэр уровня сервера?](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal#create-a-server-level-firewall-rule)

## <a name="setup-user-credentials"></a>Настройка учетных данных пользователя

Подключитесь к хранилищу данных SQL Azure с помощью SQL Server Management Studio или другого инструмента на свой выбор как администратор сервера. Выполните следующие команды SQL, чтобы создать пользователя для Fivetran.

В базе данных master: ` CREATE LOGIN fivetran WITH PASSWORD = '<password>'; `

В базе данных хранилища данных SQL:

```
CREATE USER fivetran_user_without_login without login;
CREATE USER fivetran FOR LOGIN fivetran;
GRANT IMPERSONATE on USER::fivetran_user_without_login to fivetran;
```

После создания пользователя fivetran предоставьте ему следующие разрешения в хранилище.

```
GRANT CONTROL to fivetran;
```

Добавьте подходящий класс ресурсов для созданного пользователя в зависимости от требований к памяти для создания индекса columnstore. Например, для интеграции таких решений, как Marketo и Salesforce, необходим более высокий класс ресурсов из-за большого количества столбцов и большего объема данных, который требует больше памяти для создания индексов columnstore.

Рекомендуется использовать статические классы ресурсов. Вы можете начать с класса ресурсов `staticrc20`, который позволяет выделить 200 МБ для пользователя независимо от выбранного уровня производительности. При сбое создания индекса columnstore с текущим классом ресурсов потребуется повысить класс ресурсов.

```
EXEC sp_addrolemember '<resource_class_name>', 'fivetran';
```

Дополнительные сведения можно найти в документации по [ограничениям параллелизма и памяти](https://docs.microsoft.com/azure/sql-data-warehouse/memory-and-concurrency-limits#data-warehouse-limits) и [классам ресурсов](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-memory-optimizations-for-columnstore-compression#ways-to-allocate-more-memory).

Разрешение CONTROL требуется для создания учетных данных уровня базы данных, которые будут использоваться при загрузке файлов из хранилища BLOB-объектов с помощью PolyBase.

Введите учетные данные для доступа к хранилищу данных SQL Azure.

1. Узел (имя вашего сервера).
2. Порт
3. База данных
4. Пользователь (именем пользователя должно быть `fivetran@<server_name>`, где `<server_name>` является частью универсального кода ресурса (URI) узла Azure: `<server_name>.database.windows.net`).
5. Пароль