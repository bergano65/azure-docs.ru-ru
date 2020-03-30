---
title: Подключение с перенаправлением - База данных Azure для MyS'L
description: В этой статье описывается, как можно настроить приложение для подключения к базе данных Azure для MyS'L с перенаправлением.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f987d5d9640c3bfef61320df379a68eae2f4712b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246341"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Подключение к базе данных Azure для MyS'L с перенаправлением

В этой теме объясняется, как подключить приложение к базе данных Azure для сервера MyS'L к режиму перенаправления. Перенаправление направлено на снижение задержки сети между клиентскими приложениями и серверами MyS'L, позволяя приложениям подключаться непосредственно к серверным узлам бэкэнда.

## <a name="before-you-begin"></a>Перед началом
Войдите на [портал Azure](https://portal.azure.com). Создайте базу данных Azure для сервера MyS'L с версией двигателя 5.6, 5.7 или 8.0. Дополнительные сведения см. в статье [Создание базы данных Azure для сервера MySQL с помощью портала Azure](quickstart-create-mysql-server-database-using-azure-portal.md) или [Создание сервера базы данных Azure для MySQL с помощью Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

Перенаправление в настоящее время поддерживается только тогда, когда **SSL включен** в базу данных Azure для сервера MyS'L. Дополнительные сведения о настройке SSL см. в статье [Настройка SSL-подключений в приложении для безопасного подключения к базе данных Azure для MySQL](howto-configure-ssl.md#step-3--enforcing-ssl-connections-in-azure).

## <a name="php"></a>PHP

Поддержка перенаправления в приложениях PHP доступна через расширение [mysqlnd_azure,](https://github.com/microsoft/mysqlnd_azure) разработанное корпорацией Майкрософт. 

Расширение mysqlnd_azure доступно для добавления к приложениям PHP через PECL, и настоятельно рекомендуется установить и настроить расширение через официально опубликованный [пакет PECL.](https://pecl.php.net/package/mysqlnd_azure)

> [!IMPORTANT]
> Поддержка перенаправления в [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) расширения PHP в настоящее время находится в предварительном просмотре.

### <a name="redirection-logic"></a>Логика перенаправления

>[!IMPORTANT]
> Логика ренаправления/начала поведения версия 1.1.0 была обновлена и **рекомендуется использовать версию 1.1.0 .**

Поведение перенаправления определяется значением `mysqlnd_azure.enableRedirect`. В приведенной ниже таблице описывается поведение перенаправления, основанное на значении этого параметра, начинающееся в **версии 1.1.0 .**

Если вы используете старую версию расширения mysqlnd_azure (версия 1.0.0-1.0.3), поведение `mysqlnd_azure.enabled`перенаправления определяется значением . Действительные значения `off` (действия так же, как поведение, `on` изложенное `preferred` в таблице ниже) и (действует, как в таблице ниже).  

|**mysqlnd_azure.enableРепрямого значения**| **Поведение**|
|----------------------------------------|-------------|
|`off` либо `0`|Перенаправление не будет использоваться. |
|`on` либо `1`|- Если SSL не включен в базу данных Azure для сервера MyS'L, подключение не будет сделано. Будет возвращена следующая ошибка: *"mysqlnd_azure.enableRedirect, но опция SSL не установлена в строке соединения. Перенаправление возможно только с Помощью SSL.*<br>- Если SSL включен на сервере MyS'L, но перенаправление не поддерживается на сервере, первое соединение прерывается и возвращается следующая ошибка: *"Подключение прервано, потому что перенаправление не включено на сервере MyS'L или пакет сети не соответствует протоколу перенаправления".*<br>- Если сервер MyS'L поддерживает перенаправление, но перенаправленное соединение не удалось по любой причине, а также прервать первое прокси-соединение. Верните ошибку перенарежного соединения.|
|`preferred` либо `2`<br>  (значение по умолчанию)|- mysqlnd_azure будет использовать перенаправление, если это возможно.<br>- Если соединение не использует SSL, сервер не поддерживает перенаправление, или перенаправленное соединение не может подключиться по любой несмертельной причине, в то время как прокси-соединение по-прежнему является действительным, оно вернется к первому прокси-соединению.|

В следующих разделах документа будет `mysqlnd_azure` изложено, как установить расширение с помощью PECL и установить значение этого параметра.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Предварительные требования 
- PHP версии 7.2.15 и 7.3.2 "
- PHP PEAR 
- php-mysql
- База данных Azure для сервера MyS'L с включенным SSL

1. Установите [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) с [помощью PECL](https://pecl.php.net/package/mysqlnd_azure). Рекомендуется использовать версию 1.1.0.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Найдите каталог расширения`extension_dir`( ) запустив ниже:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Изменение каталогов в возвращенную `mysqlnd_azure.so` папку и обеспечение того, чтобы она была размещена в этой папке. 

4. Найдите папку для файлов .ini, запустив ниже: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Изменение каталогов в эту возвращенную папку. 

6. Создайте новый файл `mysqlnd_azure`.ini для . Убедитесь, что алфавит ный порядок имени после mysqnld, так как модули загружаются в соответствии с порядком имени файлов ini. Например, `mysqlnd` если .ini `10-mysqlnd.ini`назван, назовите mysqlnd ini как `20-mysqlnd-azure.ini`.

7. В новом файле .ini добавьте следующие строки, чтобы включить перенаправление.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Предварительные требования 
- PHP версии 7.2.15 и 7.3.2 "
- php-mysql
- База данных Azure для сервера MyS'L с включенным SSL

1. Определите, работаете ли вы версию PHP x64 или x86, запустив следующую команду:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Загрузите соответствующую версию [x64](https://github.com/microsoft/mysqlnd_azure) или x86 mysqlnd_azure DLL от [PECL,](https://pecl.php.net/package/mysqlnd_azure) которая соответствует вашей версии PHP. Рекомендуется использовать версию 1.1.0.

3. Извлеките файл застежки-молнии и найдите DLL под названием. `php_mysqlnd_azure.dll`

4. Найдите каталог расширения`extension_dir`() запустив нижеприведенную команду:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Копирование `php_mysqlnd_azure.dll` файла в каталог, возвращенный в шаге 4. 

6. Найдите папку PHP, содержащую `php.ini` файл, используя следующую команду:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Измените `php.ini` файл и добавьте следующие дополнительные строки, чтобы включить перенаправление. 

    В разделе Динамические расширения: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    В разделе Настройки модуля:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Подтверждение перенаправления

Вы также можете подтвердить, что перенаправление настроено с приведенным ниже образцом кода PHP. Создайте файл PHP, который называется, `mysqlConnect.php` и вставьте ниже приведенный код. Обновите имя сервера, имя пользователя и пароль своими собственными. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enableRedirect: ", ini_get("mysqlnd_azure.enableRedirect"), "\n";
  $db = mysqli_init();
  //The connection must be configured with SSL for redirection test
  $link = mysqli_real_connect ($db, $host, $username, $password, $db_name, 3306, NULL, MYSQLI_CLIENT_SSL);
  if (!$link) {
     die ('Connect error (' . mysqli_connect_errno() . '): ' . mysqli_connect_error() . "\n");
  }
  else {
    echo $db->host_info, "\n"; //if redirection succeeds, the host_info will differ from the hostname you used used to connect
    $res = $db->query('SHOW TABLES;'); //test query with the connection
    print_r ($res);
    $db->close();
  }
?>
 ```

## <a name="next-steps"></a>Дальнейшие действия
Для получения дополнительной информации [Connection Strings](howto-connection-string.md)о строках соединения см.
