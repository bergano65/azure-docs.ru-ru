---
title: Создание строки подключения с помощью PowerShell — база данных Azure для PostgreSQL
description: В этой статье приведен пример Azure PowerShell для создания строки подключения для подключения к базе данных Azure для PostgreSQL.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: how-to
ms.date: 8/6/2020
ms.openlocfilehash: a8e82c1863fde7a7580300606949e00a34c4e58c
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87910402"
---
# <a name="how-to-generate-an-azure-database-for-postgresql-connection-string-with-powershell"></a>Создание строки подключения базы данных Azure для PostgreSQL с помощью PowerShell

В этой статье показано, как создать строку подключения для сервера базы данных Azure для PostgreSQL. Строку подключения можно использовать для подключения к базе данных Azure для PostgreSQL из множества различных приложений.

## <a name="requirements"></a>Требования

В этой статье в качестве отправной точки используются ресурсы, созданные в следующем руководстве:

* [Краткое руководство. Создание сервера базы данных Azure для PostgreSQL с помощью PowerShell](quickstart-create-postgresql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Получение строки подключения

`Get-AzPostgreSqlConnectionString`Командлет используется для создания строки подключения для подключения приложений к базе данных Azure для PostgreSQL. В следующем примере возвращается строка подключения для клиента PHP из **mydemoserver**.

```azurepowershell-interactive
Get-AzPostgreSqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
host=mydemoserver.postgres.database.azure.com port=5432 dbname={your_database} user=myadmin@mydemoserver password={your_password} sslmode=require
```

Допустимые значения для `Client` параметра:

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Настройка параметров сервера базы данных Azure для PostgreSQL с помощью PowerShell](howto-configure-server-parameters-using-powershell.md)
