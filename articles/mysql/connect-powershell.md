---
title: Создание строки подключения с помощью PowerShell в Базе данных Azure для MySQL
description: В этом кратком руководстве приводится пример скрипта Azure PowerShell для создания строки подключения для Базы данных Azure для MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: quickstart
ms.date: 8/3/2020
ms.openlocfilehash: 9de569e09e1b8bcf29f47077cbe15f0a6c40ddd1
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87544925"
---
# <a name="azure-database-for-mysql-generate-a-connection-string-with-powershell"></a>База данных Azure для MySQL: Создание строки подключения с помощью PowerShell

В этой статье описывается, как создать строку подключения для сервера Базы данных Azure для MySQL. Строку подключения можно использовать для подключения к Базе данных Azure для MySQL из множества различных приложений.

## <a name="requirements"></a>Требования

В качестве отправной точки в этой статье используются ресурсы, созданные в соответствии со следующим руководством:

* [Краткое руководство. Создание сервера Базы данных Azure для MySQL с помощью Azure PowerShell](quickstart-create-mysql-server-database-using-azure-powershell.md)

## <a name="get-the-connection-string"></a>Получение строки подключения

Командлет `Get-AzMySqlConnectionString` используется для создания строки подключения, позволяющей приложениям подключаться к Базе данных Azure для MySQL. В примере ниже возвращается строка подключения для клиента PHP от **mydemoserver**.

```azurepowershell-interactive
Get-AzMySqlConnectionString -Client PHP -Name mydemoserver -ResourceGroupName myresourcegroup
```

```Output
$con=mysqli_init();mysqli_ssl_set($con, NULL, NULL, {ca-cert filename}, NULL, NULL); mysqli_real_connect($con, "mydemoserver.mysql.database.azure.com", "myadmin@mydemoserver", {your_password}, {your_database}, 3306);
```

Допустимые значения для параметра `Client` включают следующие:

* ADO&#46;NET
* JDBC
* Node.js
* PHP
* Python
* Ruby
* WebApp

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Проектирование Базы данных Azure для MySQL с помощью PowerShell](tutorial-design-database-using-powershell.md)
