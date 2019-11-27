---
title: Подключение к базе данных Azure для MySQL с перенаправлением
description: В этой статье описывается, как настроить приложение для подключения к базе данных Azure для MySQL с перенаправлением.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/15/2019
ms.openlocfilehash: eed52c444bbcad60b4e629cf476c285f53c9f515
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233744"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Подключение к базе данных Azure для MySQL с перенаправлением

В этом разделе объясняется, как подключить приложение сервера базы данных Azure для MySQL с режимом перенаправления. Перенаправление нацелено на сокращение задержки в сети между клиентскими приложениями и серверами MySQL, позволяя приложениям подключаться непосредственно к внутренним узлам сервера.

> [!IMPORTANT]
> Поддержка перенаправления в [MYSQLND_AZURE](https://github.com/microsoft/mysqlnd_azure) PHP сейчас доступна в предварительной версии.

## <a name="before-you-begin"></a>Перед началом работы
Войдите на [портале Azure](https://portal.azure.com). Создайте сервер базы данных Azure для MySQL с ядром версии 5,6, 5,7 или 8,0. Дополнительные сведения см. в статье [Создание базы данных Azure для сервера MySQL с помощью портала Azure](quickstart-create-mysql-server-database-using-azure-portal.md) или [Создание сервера базы данных Azure для MySQL с помощью Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

В настоящее время перенаправление поддерживается, только если включен протокол SSL. Дополнительные сведения о настройке SSL см. в статье [Настройка SSL-подключений в приложении для безопасного подключения к базе данных Azure для MySQL](https://docs.microsoft.com/azure/mysql/howto-configure-ssl#step-3-enforcing-ssl-connections-in-azure). 

## <a name="php"></a>PHP

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>предварительным требованиям 
- Версии PHP 7.2.15 + и 7.3.2 +
- ГРУША PHP 
- PHP — MySQL
- Сервер базы данных Azure для MySQL с включенным SSL

1. Установите [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) с помощью [PECL](https://pecl.php.net/package/mysqlnd_azure).

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Откройте каталог расширения (`extension_dir`), выполнив следующую команду:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Измените каталоги на возвращенную папку и убедитесь, что `mysqlnd_azure.so` находится в этой папке. 

4. Откройте папку для ini-файлов, выполнив следующую команду: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Измените каталоги на эту возвращенную папку. 

6. Создайте новый ini-файл для `mysqlnd_azure`. Убедитесь, что в алфавитном порядке указано имя после мискнлд, так как модули загружаются в соответствии с порядком имен ini-файлов. Например, если `mysqlnd`. ini имеет имя `10-mysqlnd.ini`, присвойте параметру мисклнд ini имя `20-mysqlnd-azure.ini`.

7. В новом ini-файле добавьте следующие строки, чтобы включить перенаправление.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enabled=on
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>предварительным требованиям 
- Версии PHP 7.2.15 + и 7.3.2 +
- PHP — MySQL
- Сервер базы данных Azure для MySQL с включенным SSL

1. Определите, используется ли версия PHP для x64 или x86, выполнив следующую команду:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Скачайте соответствующую версию [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) DLL для платформы x64 или x86 из [PECL](https://pecl.php.net/package/mysqlnd_azure) , которая соответствует вашей версии PHP. 

3. Извлеките ZIP-файл и найдите библиотеку DLL с именем `php_mysqlnd_azure.dll`.

4. Откройте каталог расширения (`extension_dir`), выполнив следующую команду:

    ```cmd
    php -i | find "extension_dir"s
    ```

5. Скопируйте файл `php_mysqlnd_azure.dll` в каталог, возвращенный на шаге 4. 

6. Откройте папку PHP, содержащую файл `php.ini`, с помощью следующей команды:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Измените файл `php.ini` и добавьте следующие дополнительные строки, чтобы включить перенаправление. 

    В разделе Динамические расширения: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    В разделе Параметры модуля:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enabled=on
    ```

### <a name="confirm-redirection"></a>Подтверждение перенаправления

Также можно подтвердить, что перенаправление настроено с помощью приведенного ниже примера кода PHP. Создайте файл PHP с именем `mysqlConnect.php` и вставьте приведенный ниже код. Обновите имя сервера, имя пользователя и пароль. 
 
 ```php
<?php
$host = '<yourservername>.mysql.database.azure.com';
$username = '<yourusername>@<yourservername>';
$password = '<yourpassword>';
$db_name = 'testdb';
  echo "mysqlnd_azure.enabled: ", ini_get("mysqlnd_azure.enabled") == true?"On":"Off", "\n";
  $db = mysqli_init();
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

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о строках подключения см. в [соответствующей статье](howto-connection-string.md).

