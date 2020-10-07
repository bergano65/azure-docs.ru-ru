---
title: Создание строки подключения с помощью PowerShell в Базе данных Azure для MySQL
description: В этой статье приводится пример скрипта Azure PowerShell для создания строки подключения к Базе данных Azure для MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc, devx-track-azurepowershell
ms.topic: quickstart
ms.date: 8/5/2020
ms.openlocfilehash: 37a257e2ca7f354659c032833480dd207cc1bed9
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "87839952"
---
# <a name="how-to-generate-an-azure-database-for-mysql-connection-string-with-powershell"></a>Как создать строку подключения к Базе данных Azure для MySQL с помощью PowerShell

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
> [Настройка параметров сервера Базы данных Azure для MySQL с помощью PowerShell](howto-configure-server-parameters-using-powershell.md)
