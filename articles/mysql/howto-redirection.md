---
title: Подключение с перенаправлением — база данных Azure для MySQL
description: В этой статье описывается, как настроить приложение для подключения к базе данных Azure для MySQL с перенаправлением.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f987d5d9640c3bfef61320df379a68eae2f4712b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80246341"
---
# <a name="connect-to-azure-database-for-mysql-with-redirection"></a>Подключение к базе данных Azure для MySQL с перенаправлением

В этом разделе объясняется, как подключить приложение сервера базы данных Azure для MySQL с режимом перенаправления. Перенаправление нацелено на сокращение задержки в сети между клиентскими приложениями и серверами MySQL, позволяя приложениям подключаться непосредственно к внутренним узлам сервера.

## <a name="before-you-begin"></a>Подготовка к работе
Войдите на [портал Azure](https://portal.azure.com). Создайте сервер базы данных Azure для MySQL с ядром версии 5,6, 5,7 или 8,0. Дополнительные сведения см. в статье [Создание базы данных Azure для сервера MySQL с помощью портала Azure](quickstart-create-mysql-server-database-using-azure-portal.md) или [Создание сервера базы данных Azure для MySQL с помощью Azure CLI](quickstart-create-mysql-server-database-using-azure-cli.md).

В настоящее время перенаправление поддерживается только в том случае, если на сервере базы данных Azure для MySQL **включен протокол SSL** . Дополнительные сведения о настройке SSL см. в статье [Настройка SSL-подключений в приложении для безопасного подключения к базе данных Azure для MySQL](howto-configure-ssl.md#step-3--enforcing-ssl-connections-in-azure).

## <a name="php"></a>PHP

Поддержка перенаправления в приложениях PHP доступна через расширение [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) , разработанное корпорацией Майкрософт. 

Расширение mysqlnd_azure доступно для добавления в приложения PHP через PECL, и настоятельно рекомендуется установить и настроить расширение с помощью официально опубликованного [пакета PECL](https://pecl.php.net/package/mysqlnd_azure).

> [!IMPORTANT]
> Поддержка перенаправления в расширении [MYSQLND_AZURE](https://github.com/microsoft/mysqlnd_azure) PHP сейчас доступна в предварительной версии.

### <a name="redirection-logic"></a>Логика перенаправления

>[!IMPORTANT]
> Логика перенаправления/поведение начала версии 1.1.0 обновлена, поэтому **рекомендуется использовать версию 1.1.0 +**.

Поведение перенаправления определяется значением `mysqlnd_azure.enableRedirect`. В следующей таблице показано поведение перенаправления на основе значения этого параметра, начиная с **версии 1.1.0 +**.

Если используется более старая версия расширения mysqlnd_azure (версия 1.0.0-1.0.3), поведение перенаправления определяется значением `mysqlnd_azure.enabled`. Допустимые значения `off` (действуют аналогично, как описано в таблице ниже) и `on` (действует как `preferred` в приведенной ниже таблице).  

|**значение mysqlnd_azure. Енаблередирект**| **Поведение**.|
|----------------------------------------|-------------|
|`off` или `0`|Перенаправление не будет использоваться. |
|`on` или `1`|Если протокол SSL не включен на сервере базы данных Azure для MySQL, соединение не будет установлено. Будет возвращена следующая ошибка: *"mysqlnd_azure. енаблередирект имеет значение ON, но параметр SSL не задан в строке подключения. Перенаправление возможно только при использовании SSL. "*<br>— Если на сервере MySQL включен протокол SSL, но перенаправление не поддерживается на сервере, первое подключение прерывается и возвращается следующая ошибка: *"соединение прервано, так как перенаправление не включено на сервере MySQL, или сетевой пакет не соответствует протоколу перенаправления".*<br>— Если сервер MySQL поддерживает перенаправление, но по какой-либо причине перенаправленное соединение не удалось, также прерывается первое подключение прокси. Возвращает ошибку перенаправленного соединения.|
|`preferred` или `2`<br>  (значение по умолчанию)|-mysqlnd_azure будет использовать перенаправление, если это возможно.<br>— Если соединение не использует протокол SSL, сервер не поддерживает перенаправление, или перенаправленное подключение не удается подключиться по неустранимым причинам, в то время как прокси-сервер все еще является допустимым, он перейдет к первому прокси-серверу.|

В последующих разделах документа будет показано, как установить `mysqlnd_azure` расширение с помощью PECL и задать значение этого параметра.

### <a name="ubuntu-linux"></a>Ubuntu Linux

#### <a name="prerequisites"></a>Предварительные условия 
- Версии PHP 7.2.15 + и 7.3.2 +
- ГРУША PHP 
- PHP — MySQL
- Сервер базы данных Azure для MySQL с включенным SSL

1. Установите [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) с помощью [PECL](https://pecl.php.net/package/mysqlnd_azure). Рекомендуется использовать версию 1.1.0 +.

    ```bash
    sudo pecl install mysqlnd_azure
    ```

2. Откройте каталог расширения (`extension_dir`), выполнив следующую команду:

    ```bash
    php -i | grep "extension_dir"
    ```

3. Измените каталоги на возвращенную папку и убедитесь `mysqlnd_azure.so` , что она находится в этой папке. 

4. Откройте папку для ini-файлов, выполнив следующую команду: 

    ```bash
    php -i | grep "dir for additional .ini files"
    ```

5. Измените каталоги на эту возвращенную папку. 

6. Создайте новый ini-файл для `mysqlnd_azure`. Убедитесь, что в алфавитном порядке указано имя после мискнлд, так как модули загружаются в соответствии с порядком имен ini-файлов. Например, если `mysqlnd` указан ini-файла `10-mysqlnd.ini` `20-mysqlnd-azure.ini`, присвойте параметру мисклнд ini имя.

7. В новом ini-файле добавьте следующие строки, чтобы включить перенаправление.

    ```bash
    extension=mysqlnd_azure
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="windows"></a>Windows

#### <a name="prerequisites"></a>Предварительные условия 
- Версии PHP 7.2.15 + и 7.3.2 +
- PHP — MySQL
- Сервер базы данных Azure для MySQL с включенным SSL

1. Определите, используется ли версия PHP для x64 или x86, выполнив следующую команду:

    ```cmd
    php -i | findstr "Thread"
    ```

2. Скачайте соответствующую версию [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) DLL для платформы x64 или x86 из [PECL](https://pecl.php.net/package/mysqlnd_azure) , которая соответствует вашей версии PHP. Рекомендуется использовать версию 1.1.0 +.

3. Извлеките ZIP-файл и найдите библиотеку DLL `php_mysqlnd_azure.dll`с именем.

4. Откройте каталог расширения (`extension_dir`), выполнив следующую команду:

    ```cmd
    php -i | find "extension_dir"
    ```

5. Скопируйте `php_mysqlnd_azure.dll` файл в каталог, возвращенный на шаге 4. 

6. Откройте папку PHP, содержащую `php.ini` файл, с помощью следующей команды:

    ```cmd
    php -i | find "Loaded Configuration File"
    ```

7. Измените `php.ini` файл и добавьте следующие дополнительные строки, чтобы включить перенаправление. 

    В разделе Динамические расширения: 
    ```cmd
    extension=mysqlnd_azure
    ```
    
    В разделе Параметры модуля:     
    ```cmd 
    [mysqlnd_azure]
    mysqlnd_azure.enableRedirect = on/off/preferred
    ```

### <a name="confirm-redirection"></a>Подтверждение перенаправления

Также можно подтвердить, что перенаправление настроено с помощью приведенного ниже примера кода PHP. Создайте файл PHP с именем `mysqlConnect.php` и вставьте приведенный ниже код. Обновите имя сервера, имя пользователя и пароль. 
 
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

## <a name="next-steps"></a>Дальнейшие шаги
Дополнительные сведения о строках подключения см. в разделе [строки подключения](howto-connection-string.md).
