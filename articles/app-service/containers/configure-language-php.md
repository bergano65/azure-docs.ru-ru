---
title: Настройка приложений PHP
description: Узнайте, как настроить готовый контейнер PHP для вашего приложения. В этой статье показаны наиболее распространенные задачи настройки.
ms.devlang: php
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 9933205095587d9e8e0d8a5641d213f159512450
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/31/2020
ms.locfileid: "84234939"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Настройка приложения PHP в Linux для Службы приложений Azure

В этом руководстве показано, как настроить встроенную среду выполнения PHP в веб-приложениях, серверных частях мобильных приложений и приложениях API Службы приложений Azure.

Это руководство содержит основные понятия и инструкции для разработчиков PHP, которые используют встроенный контейнер Linux в Службе приложений. Если вы раньше не использовали Службу приложений Azure, сначала ознакомьтесь с [кратким руководством по PHP](quickstart-php.md) и [руководством по использованию PHP с MySQL](tutorial-php-mysql-app.md).

## <a name="show-php-version"></a>Отображение версии PHP

Чтобы отобразить сведения о текущей версии PHP, выполните следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Чтобы отобразить сведения обо всех поддерживаемых версиях PHP, выполните следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>как установить версию PHP;

Чтобы настроить для PHP версию 7.2, выполните следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="customize-build-automation"></a>Настройка автоматизации сборки

Если приложение развертывается с использованием Git или ZIP-пакетов с включенной автоматизацией сборки, то автоматизация сборки cлужбы приложений проходит в такой последовательности:

1. Запустите пользовательский скрипт, если он указан `PRE_BUILD_SCRIPT_PATH`.
1. Выполните `php composer.phar install`.
1. Запустите пользовательский скрипт, если он указан `POST_BUILD_SCRIPT_PATH`.

`PRE_BUILD_COMMAND` и `POST_BUILD_COMMAND` являются переменными среды, которые по умолчанию пустые. Чтобы выполнить команды перед сборкой, определите `PRE_BUILD_COMMAND`. Чтобы выполнить команды после сборки, определите `POST_BUILD_COMMAND`.

В следующем примере указываются две переменные для ряда команд, разделенных запятыми.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PRE_BUILD_COMMAND="echo foo, scripts/prebuild.sh"
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings POST_BUILD_COMMAND="echo foo, scripts/postbuild.sh"
```

Дополнительные переменные среды для настройки автоматизации сборки см. в статье [Конфигурация Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md).

Дополнительные сведения о том, как Служба приложений выполняет и создает приложения PHP в Linux, см. в документации по [Oryx (сведения об обнаружении и создании приложений PHP)](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/php.md).

## <a name="customize-start-up"></a>Настройка запуска

По умолчанию во встроенном контейнере PHP выполняется сервер Apache. При запуске он выполняет команду `apache2ctl -D FOREGROUND"`. При необходимости вы можете выполнять другую команду при запуске. Для этого выполните в [Cloud Shell](https://shell.azure.com) следующую команду:

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Доступ к переменным среды

В Службе приложений можно [задать параметры приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) вне кода приложения. Затем вы сможете получать к ним доступ, используя стандартный шаблон [getenv()](https://secure.php.net/manual/function.getenv.php). Например, для доступа к параметру приложения с именем `DB_HOST` используйте следующий код:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Изменение корня сайта

Выбранная вами веб-платформа может использовать подкаталог в качестве корня сайта. Например, [Laravel](https://laravel.com/) использует для этого подкаталог `public/`.

Образ PHP по умолчанию для Службы приложений использует Apache и не позволяет настраивать корень сайта для приложения. Чтобы обойти это ограничение, добавьте файл *.htaccess* в корень репозитория со следующим содержимым:

```
<IfModule mod_rewrite.c>
    RewriteEngine on
    RewriteCond %{REQUEST_URI} ^/$
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

## <a name="customize-phpini-settings"></a>Настройка параметров php.ini

Если вам необходимо внести изменения в установку PHP, вы можете изменить любые [директивы php.ini](https://www.php.net/manual/ini.list.php), выполнив следующие инструкции.

> [!NOTE]
> Оптимальный способ для просмотра версии PHP и текущей конфигурации *php.ini* — это вызов [phpinfo()](https://www.php.net/manual/function.phpinfo.php) в приложении.
>

### <a name="customize-non-php_ini_system-directives"></a><a name="Customize-non-PHP_INI_SYSTEM directives"></a>Настройка не относящихся к PHP_INI_SYSTEM директив

Чтобы настроить директивы PHP_INI_USER, PHP_INI_PERDIR и PHP_INI_ALL (см. дополнительные сведения о [директивах php.ini](https://www.php.net/manual/ini.list.php)), добавьте файл *.htaccess* в корневой каталог приложения.

В файле *.htaccess* добавьте директивы, используя синтаксис `php_value <directive-name> <value>`. Пример:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Повторно разверните приложение с внесенными изменениями и перезапустите его. При развертывании с помощью Kudu (например, с использованием [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)) приложение будет автоматически перезапущено после развертывания.

В качестве альтернативы *.htaccess* вы можете использовать [ini_set()](https://www.php.net/manual/function.ini-set.php) в приложении для настройки таких не относящихся к PHP_INI_SYSTEM директив.

### <a name="customize-php_ini_system-directives"></a><a name="customize-php_ini_system-directives"></a>Настройка директив PHP_INI_SYSTEM

При настройке директив PHP_INI_SYSTEM (см. подробные сведения о [директивах php.ini](https://www.php.net/manual/ini.list.php)) нельзя использовать подход с *.htaccess*. Служба приложений предоставляет отдельный механизм с использованием параметра приложения `PHP_INI_SCAN_DIR`.

Сначала выполните следующую команду в [Cloud Shell](https://shell.azure.com), чтобы добавить параметр приложения с именем `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` — это каталог по умолчанию, в котором располагается файл *php.ini*. `/home/site/ini` — это пользовательский каталог, в который вы добавите пользовательский файл *.ini*. Значения в нем нужно разделять с помощью символа `:`.

Перейдите к веб-сеансу SSH с контейнером Linux (`https://<app-name>.scm.azurewebsites.net/webssh/host`).

Создайте каталог по пути `/home/site` с именем `ini`, а затем создайте файл *.ini* в каталоге `/home/site/ini` (например, *settings.ini)* с нужными директивами. Используйте тот же синтаксис, который используется в файле *php.ini*. 

> [!TIP]
> Во встроенных контейнерах Linux в Службе приложений в качестве постоянного общего хранилища используется каталог */home*. 
>

Например, чтобы изменить значение [expose_php](https://php.net/manual/ini.core.php#ini.expose-php), выполните следующие команды:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Чтобы изменения вступили в силу, перезапустите приложение.

## <a name="enable-php-extensions"></a>Включение расширений PHP

Встроенные установки PHP содержат наиболее часто используемые расширения. Для включения дополнительных расширений выполните те же действия, что и для [настройки директив php.ini](#customize-php_ini_system-directives).

> [!NOTE]
> Оптимальный способ для просмотра версии PHP и текущей конфигурации *php.ini* — это вызов [phpinfo()](https://php.net/manual/function.phpinfo.php) в приложении.
>

Чтобы включить дополнительные расширения, выполните следующие действия.

Добавьте каталог `bin` в корневой каталог приложения и поместите в него файлы с расширением `.so` (например, *mongodb.so*). Убедитесь, что расширения совместимы с версией PHP в Azure, а также с VC9 и непотокобезопасного кода (nts).

Примените изменения.

Выполните действия, описанные в разделе [Настройка директив PHP_INI_SYSTEM](#customize-php_ini_system-directives), добавьте расширения в пользовательский файл *.ini* с директивами [extension](https://www.php.net/manual/ini.core.php#ini.extension) или [zend_extension](https://www.php.net/manual/ini.core.php#ini.zend-extension).

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

Если рабочее приложение PHP в Службе приложений ведет себя иначе или сообщает об ошибках, попробуйте сделать следующее:

- [Получите доступ к потоку журнала](#access-diagnostic-logs).
- Протестируйте приложение локально в рабочем режиме. Служба приложений запускает приложения Node.js в рабочем режиме, поэтому вам необходимо убедиться в том, что проект работает надлежащим образом локально в рабочем режиме. Пример:
    - В зависимости от содержимого файла *composer.json* для рабочего режима могут быть установлены разные пакеты (`require` или `require-dev`).
    - Некоторые веб-платформы в рабочем режиме могут выполнять другие операции при развертывании статических файлов.
    - Некоторые веб-платформы могут использовать специальные скрипты запуска в рабочем режиме.
- Выполните свое приложение в Службе приложений в режиме отладки. Например, в [Laravel](https://meanjs.org/) вы можете настроить для приложения вывод сообщений отладки в производственной среде, [задав для параметра приложения `APP_DEBUG` значение `true`](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство. по приложению PHP с MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Служба приложений под управлением Linux: вопросы и ответы](app-service-linux-faq.md)
