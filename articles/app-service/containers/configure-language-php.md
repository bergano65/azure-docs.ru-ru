---
title: Настройка приложений PHP
description: Узнайте, как настроить предварительно построенный контейнер PHP для вашего приложения. В этой статье показаны наиболее распространенные задачи настройки.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9e87466f810dc4ebf767c36ad74c358cbf6069e5
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758875"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Настройте приложение Linux PHP для службы приложений Azure

В этом руководстве показано, как настроить встроенное время выполнения PHP для веб-приложений, мобильных задних концов и приложений API в Azure App Service.

В этом руководстве содержатся ключевые концепции и инструкции для разработчиков PHP, которые используют встроенный контейнер Linux в App Service. Если вы никогда не пользовались службой приложений Azure, сначала следуйте [quickstart](quickstart-php.md) и [PHP с помощью учебника MyS'L.](tutorial-php-mysql-app.md)

## <a name="show-php-version"></a>Показать версию PHP

Чтобы показать текущую версию PHP, запустите следующую команду в [облачной оболочке:](https://shell.azure.com)

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Чтобы показать все поддерживаемые версии PHP, запустите следующую команду в [облачной оболочке:](https://shell.azure.com)

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>как установить версию PHP;

Запустите следующую команду в [облачной оболочке,](https://shell.azure.com) чтобы настроить версию PHP до 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>Настройка автоматизации сборки

Если развертывать приложение с помощью пакетов Git или zip с включенной автоматизацией сборки, служба App Service выполняет этапы автоматизации через следующую последовательность:

1. Выполнить пользовательский `PRE_BUILD_SCRIPT_PATH`скрипт, если указано .
1. Выполните `php composer.phar install`.
1. Выполнить пользовательский `POST_BUILD_SCRIPT_PATH`скрипт, если указано .

`PRE_BUILD_COMMAND`и `POST_BUILD_COMMAND` являются переменными среды, которые по умолчанию пусты. Чтобы запустить команды предварительной `PRE_BUILD_COMMAND`сборки, определите. Чтобы запустить команды после сборки, определите. `POST_BUILD_COMMAND`

В следующем примере указаны две переменные к серии команд, разделенных запятыми.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Для дополнительных переменных среды для [Oryx configuration](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md)настройки автоматизации сборки см.

Для получения дополнительной информации о том, как Служба приложений приложений работает и создает приложения PHP в Linux, [см.](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md)

## <a name="customize-start-up"></a>Настройка запуска

По умолчанию встроенный контейнер PHP запускает сервер Apache. При запуске, он `apache2ctl -D FOREGROUND"`работает . Если хотите, вы можете запустить другую команду при запуске, запустив следующую команду в [облачной оболочке:](https://shell.azure.com)

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Доступ к переменным среды

В Службе приложений можно [задать параметры приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) вне кода приложения. Затем вы можете получить к ним доступ по стандартному шаблону [getenv()](https://secure.php.net/manual/function.getenv.php) Например, для доступа к параметру приложения с именем `DB_HOST` используйте следующий код:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Изменение корня сайта

Веб-рамки по вашему выбору могут использовать субдиректорию в качестве корня сайта. Например, [Laravel](https://laravel.com/), `public/` использует субдиректорию в качестве корня сайта.

Изображение PHP по умолчанию для Службы App использует Apache, и оно не позволяет настроить корень сайта для вашего приложения. Чтобы обойти это ограничение, добавьте файл *.htaccess* в корень репозитория со следующим содержанием:

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

## <a name="customize-phpini-settings"></a>Настройка настроек php.ini

Если вам необходимо внести изменения в установку PHP, вы можете изменить любую из [директив php.ini,](https://www.php.net/manual/ini.list.php) выдянув следующие действия.

> [!NOTE]
> Лучший способ увидеть версию PHP и текущую конфигурацию *php.ini* — вызвать [phpinfo()](https://php.net/manual/function.phpinfo.php) в вашем приложении.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Директивы по настройке не PHP_INI_SYSTEM

Чтобы настроить директивы PHP_INI_USER, PHP_INI_PERDIR и PHP_INI_ALL (см. [директивы php.ini),](https://www.php.net/manual/ini.list.php)добавьте файл *.htaccess* в корневой каталог приложения.

В *файле .htaccess* добавьте директивы с помощью синтаксиса. `php_value <directive-name> <value>` Пример:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Перераспределите приложение с изменениями и перезапустите его. При развертывании с помощью Kudu (например, с помощью [Git)](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)он автоматически перезапускается после развертывания.

В качестве альтернативы использованию *.htaccess*вы можете использовать [ini_set()](https://www.php.net/manual/function.ini-set.php) в приложении, чтобы настроить эти неPHP_INI_SYSTEM директивы.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Настройка директив PHP_INI_SYSTEM

Для настройки PHP_INI_SYSTEM директив (см. [директивы php.ini)](https://www.php.net/manual/ini.list.php)нельзя использовать подход *.htaccess.* Служба приложений предоставляет отдельный механизм с использованием настройки `PHP_INI_SCAN_DIR` приложения.

Во-первых, запустите следующую команду в [облачной оболочке,](https://shell.azure.com) чтобы добавить настройку приложения под названием: `PHP_INI_SCAN_DIR`

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d`— это каталог по умолчанию, где существует *php.ini.* `/home/site/ini`это пользовательский каталог, в котором вы добавите пользовательский файл *.ini.* Вы разделяете значения `:`с .

Перейдите к веб-сессии SSH`https://<app-name>.scm.azurewebsites.net/webssh/host`с контейнером Linux ().

Создайте каталог `/home/site` в `ini`вызове, затем создайте `/home/site/ini` файл *.ini* в каталоге (например, *settings.ini)* с директивами, которые вы хотите настроить. Используйте тот же синтаксис, который вы использовали бы в файле *php.ini.* 

> [!TIP]
> Во встроенных контейнерах Linux в App *Service/дом* используется в качестве сохраняемого общего хранилища. 
>

Например, изменить значение [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) запустить следующие команды:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Чтобы изменения вступили в силу, перезапустите приложение.

## <a name="enable-php-extensions"></a>Включить расширения PHP

Встроенные установки PHP содержат наиболее часто используемые расширения. Вы можете включить дополнительные расширения таким же образом, как вы [настраиваете php.ini директивы.](#customize-php_ini_system-directives)

> [!NOTE]
> Лучший способ увидеть версию PHP и текущую конфигурацию *php.ini* — вызвать [phpinfo()](https://php.net/manual/function.phpinfo.php) в вашем приложении.
>

Чтобы включить дополнительные расширения, выполните следующие действия.

Добавьте `bin` каталог в корневой каталог приложения `.so` и поместите в него файлы расширения (например, *mongodb.so).* Убедитесь, что расширения совместимы с версией PHP в Azure и совместимы с VC9 и не безопасны для потоков (nts).

Развертывайте изменения.

Следуйте шагам в [директивах PHP_INI_SYSTEM,](#customize-php_ini_system-directives)добавляйте расширения в пользовательский файл *.ini* с [помощью расширения](https://www.php.net/manual/ini.core.php#ini.extension) или [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension) директив.

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

Когда работающая приложение PHP ведет себя по-разному в App Service или имеет ошибки, попробуйте следующее:

- [Получите доступ к потоку журнала](#access-diagnostic-logs).
- Проверьте приложение локально в режиме производства. Служба приложения App запускает приложения Node.js в производственном режиме, поэтому необходимо убедиться, что ваш проект работает в обычном режиме локально. Пример:
    - В зависимости от вашего *composer.json,* различные`require` пакеты `require-dev`могут быть установлены для режима производства ( по сравнению с ).
    - Некоторые веб-платформы могут развертывать статические файлы по-разному в режиме производства.
    - Некоторые веб-платформы могут использовать пользовательские сценарии запуска при запуске в режиме производства.
- Запустите приложение в службе приложений в режиме отладки. Например, в [Laravel](https://meanjs.org/)можно настроить приложение на вывод сообщений в производство, [установив `APP_DEBUG` настройку приложения. `true` ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings)

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Руководство по приложению PHP с MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Служба приложений под управлением Linux: вопросы и ответы](app-service-linux-faq.md)
