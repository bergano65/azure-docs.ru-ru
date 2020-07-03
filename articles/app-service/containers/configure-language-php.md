---
title: Настройка приложений PHP
description: Узнайте, как настроить предварительно построенный контейнер PHP для приложения. В этой статье показаны наиболее распространенные задачи настройки.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9e87466f810dc4ebf767c36ad74c358cbf6069e5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81758875"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Настройка приложения PHP для Linux для службы приложений Azure

В этом руководство показано, как настроить встроенную среду выполнения PHP для веб-приложений, мобильных серверных интерфейсов и приложений API в службе приложений Azure.

Это краткое описание содержит основные понятия и инструкции для разработчиков PHP, использующих встроенный контейнер Linux в службе приложений. Если вы никогда не использовали службу приложений Azure, сначала следуйте указаниям в руководстве по [PHP](quickstart-php.md) и [PHP с MySQL](tutorial-php-mysql-app.md) .

## <a name="show-php-version"></a>Показывать версию PHP

Чтобы отобразить текущую версию PHP, выполните следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Чтобы отобразить все поддерживаемые версии PHP, выполните следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>как установить версию PHP;

Выполните следующую команду в [Cloud Shell](https://shell.azure.com) , чтобы установить версию PHP 7,2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>Настройка автоматизации сборки

Если приложение развертывается с использованием Git или zip-пакетов с включенной автоматизацией сборки, то Автоматизация сборки службы приложений проходит через следующую последовательность:

1. Запустить пользовательский скрипт, если он `PRE_BUILD_SCRIPT_PATH`указан в.
1. Выполните команду `php composer.phar install`.
1. Запустить пользовательский скрипт, если он `POST_BUILD_SCRIPT_PATH`указан в.

`PRE_BUILD_COMMAND`и `POST_BUILD_COMMAND` представляют собой переменные среды, которые по умолчанию являются пустыми. Чтобы выполнить команды перед сборкой, определите `PRE_BUILD_COMMAND`. Чтобы выполнить команды после сборки, определите `POST_BUILD_COMMAND`.

В следующем примере указываются две переменные для ряда команд, разделенных запятыми.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Дополнительные переменные среды для настройки автоматизации сборки см. в разделе [Орикс Configuration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Дополнительные сведения о том, как служба приложений работает и создает приложения PHP в Linux, см. в [документации по Орикс: как обнаруживаются и строятся приложения PHP](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md).

## <a name="customize-start-up"></a>Настройка запуска

По умолчанию встроенный контейнер PHP запускает сервер Apache. При запуске он выполняется `apache2ctl -D FOREGROUND"`. При необходимости можно выполнить другую команду при запуске, выполнив следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Доступ к переменным среды

В Службе приложений можно [задать параметры приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) вне кода приложения. Затем вы можете получить доступ к ним с помощью стандартного шаблона [getenv ()](https://secure.php.net/manual/function.getenv.php) . Например, для доступа к параметру приложения с именем `DB_HOST` используйте следующий код:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Изменить корень сайта

Выбранная вами веб-платформа может использовать подкаталог в качестве корня сайта. Например, [Laravel](https://laravel.com/)использует `public/` подкаталог в качестве корневого каталога сайта.

Используемый по умолчанию образ PHP для службы приложений использует Apache и не позволяет настраивать корневой каталог сайта для приложения. Чтобы обойти это ограничение, добавьте в корень репозитория файл *. htaccess* со следующим содержимым:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^(.*)$ /public/$1 [NC,L,QSA]
</IfModule>
```

Если вы предпочитаете не использовать повторное создание с помощью *.htaccess*, вместо него приложение Laravel можно развернуть из [пользовательского образа Docker](quickstart-docker-go.md).

## <a name="detect-https-session"></a>Обнаружение сеанса HTTPS

В Службе приложений [завершение SSL-запросов](https://wikipedia.org/wiki/TLS_termination_proxy) происходит в подсистеме балансировки нагрузки сети, поэтому все HTTPS-запросы достигают вашего приложения в виде незашифрованных HTTP-запросов. Если логика вашего приложения проверяет, зашифрованы ли пользовательские запросы, проверяйте заголовок `X-Forwarded-Proto`.

```php
if (isset($_SERVER['X-Forwarded-Proto']) && $_SERVER['X-Forwarded-Proto'] === 'https') {
  // Do something when HTTPS is used
}
```

Популярные веб-платформы позволяют получить доступ к информации `X-Forwarded-*` в стандартном шаблоне приложения. В [CodeIgniter](https://codeigniter.com/) функция [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) по умолчанию проверяет значение `X_FORWARDED_PROTO`.

## <a name="customize-phpini-settings"></a>Настройка параметров PHP. ini

Если необходимо внести изменения в установку PHP, можно изменить любые [директивы PHP. ini](https://www.php.net/manual/ini.list.php) , выполнив следующие действия.

> [!NOTE]
> Лучший способ увидеть версию PHP и текущую конфигурацию *PHP. ini* — вызвать [функцию phpinfo ()](https://php.net/manual/function.phpinfo.php) в приложении.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Настройка-не PHP_INI_SYSTEM директивы

Чтобы настроить директивы PHP_INI_USER, PHP_INI_PERDIR и PHP_INI_ALL (см [. раздел директивы PHP. ini](https://www.php.net/manual/ini.list.php)), добавьте *htaccess* -файл в корневой каталог приложения.

В файле *htaccess* добавьте директивы, используя `php_value <directive-name> <value>` синтаксис. Пример:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Повторно разверните приложение с изменениями и перезапустите его. Если развернуть его с помощью KUDU (например, с помощью [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)), он будет автоматически перезапущен после развертывания.

В качестве альтернативы использованию *. htaccess*можно использовать в приложении [ini_set ()](https://www.php.net/manual/function.ini-set.php) , чтобы настроить эти директивы, не относящиеся к PHP_INI_SYSTEM.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Настройка директив PHP_INI_SYSTEM

Чтобы настроить директивы PHP_INI_SYSTEM (см [. раздел директивы PHP. ini](https://www.php.net/manual/ini.list.php)), нельзя использовать подход *. htaccess* . Служба приложений предоставляет отдельный механизм, `PHP_INI_SCAN_DIR` используя параметр приложения.

Сначала выполните следующую команду в [Cloud Shell](https://shell.azure.com) , чтобы добавить параметр приложения с именем `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`Каталог по умолчанию, где существует *PHP. ini* . `/home/site/ini`— Это пользовательский каталог, в который добавляется пользовательский *ini* -файл. Значения разделяются на `:`.

Перейдите к сеансу веб-SSH с вашим контейнером`https://<app-name>.scm.azurewebsites.net/webssh/host`Linux ().

Создайте `/home/site` каталог с именем `ini`, а затем создайте *ini* -файл в `/home/site/ini` каталоге (например, *Settings. ini)* с директивами, которые требуется настроить. Используйте тот же синтаксис, который используется в файле *PHP. ini* . 

> [!TIP]
> В встроенных контейнерах Linux в службе приложений */Home* используется в качестве постоянного общего хранилища. 
>

Например, чтобы изменить значение [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) выполните следующие команды:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Чтобы изменения вступили в силу, перезапустите приложение.

## <a name="enable-php-extensions"></a>Включить расширения PHP

Встроенные установки PHP содержат наиболее часто используемые расширения. Можно включить дополнительные расширения так же, как и [директивы PHP. ini](#customize-php_ini_system-directives).

> [!NOTE]
> Лучший способ увидеть версию PHP и текущую конфигурацию *PHP. ini* — вызвать [функцию phpinfo ()](https://php.net/manual/function.phpinfo.php) в приложении.
>

Чтобы включить дополнительные расширения, выполните следующие действия.

Добавьте `bin` каталог в корневой каталог приложения и вставьте в него файлы `.so` расширения (например, *MongoDB.so*). Убедитесь, что расширения совместимы с версией PHP в Azure и не совместимы с VC9 и непотокобезопасным (NTS).

Разверните изменения.

Выполните действия, описанные в разделе [Настройка директив PHP_INI_SYSTEM](#customize-php_ini_system-directives), добавьте расширения в пользовательский *ini* -файл с помощью директив [расширения](https://www.php.net/manual/ini.core.php#ini.extension) или [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) .

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Чтобы изменения вступили в силу, перезапустите приложение.

## <a name="access-diagnostic-logs"></a>Доступ к журналам диагностики

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Открытие сеанса SSH в браузере

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Устранение неполадок

Если рабочее приложение PHP работает иначе в службе приложений или содержит ошибки, попробуйте выполнить следующие действия.

- [Получите доступ к потоку журнала](#access-diagnostic-logs).
- Протестируйте приложение локально в рабочем режиме. Служба приложений запускает приложения Node. js в рабочем режиме, поэтому необходимо убедиться, что проект работает в режиме рабочей среды локально. Пример:
    - В зависимости от версии *Composer. JSON*различные пакеты могут быть установлены для рабочего режима (`require` VS `require-dev`).
    - Некоторые веб-платформы могут развертывать статические файлы в рабочем режиме по-разному.
    - При работе в рабочем режиме некоторые веб-платформы могут использовать пользовательские сценарии запуска.
- Запустите приложение в службе приложений в режиме отладки. Например, в [Laravel](https://meanjs.org/)можно настроить приложение для вывода отладочных сообщений в рабочей среде, [задав `APP_DEBUG` для `true`параметра приложения ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)значение.

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Руководство по приложению PHP с MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Служба приложений под управлением Linux: вопросы и ответы](app-service-linux-faq.md)
