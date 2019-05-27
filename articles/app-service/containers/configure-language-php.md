---
title: Настройка приложения PHP — служба приложений Azure | Документация Майкрософт
description: Узнайте, как настроить приложения PHP на работу в службе приложений Azure
services: app-service
documentationcenter: ''
author: cephalin
manager: jpconnock
editor: ''
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/28/2019
ms.author: cephalin
ms.openlocfilehash: 637feb855c7816dfb26229c5a65a069260a58cd3
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66003095"
---
# <a name="configure-a-linux-php-app-for-azure-app-service"></a>Настройка приложения Linux PHP для службы приложений Azure

В этом руководстве показано, как настроить встроенную среду выполнения PHP для веб-приложения, серверные части мобильных устройств и приложений API в службе приложений Azure.

Это руководство содержит основные понятия и инструкции для разработчиков на PHP, которые используют встроенный контейнер Linux в службе приложений. Если вы раньше не использовали службы приложений Azure, выполните [быстрого запуска PHP](quickstart-php.md) и [PHP с MySQL руководством](tutorial-php-mysql-app.md) первого.

## <a name="show-php-version"></a>Показать версию PHP

Чтобы отобразить текущую версию PHP, выполните следующую команду [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
```

Чтобы отобразить все поддерживаемые версии PHP, выполните следующую команду [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp list-runtimes --linux | grep PHP
```

## <a name="set-php-version"></a>как установить версию PHP;

Выполните следующую команду [Cloud Shell](https://shell.azure.com) присвоить версию PHP 7.2:

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <resource-group-name> --linux-fx-version "PHP|7.2"
```

## <a name="run-composer"></a>Запустите Composer

По умолчанию не выполняются Kudu [Composer](https://getcomposer.org/). Для включения автоматизации Composer во время развертывания Kudu, необходимо предоставить [пользовательский скрипт развертывания](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

В локальном окне терминала измените каталог на корневой папке репозитория. Выполните [шаги установки из командной строки](https://getcomposer.org/download/) для загрузки *composer.phar*.

Выполните следующие команды:

```bash
npm install kuduscript -g
kuduscript --php --scriptType bash --suppressPrompt
```

Корень репозитория сейчас содержит два новых файла, в дополнение к *composer.phar*: *.deployment* и *deploy.sh*. Эти файлы работают как для Windows и Linux версии службы приложений.

Откройте *deploy.sh* и найти `Deployment` раздел. Весь раздел, замените следующий код:

```bash
##################################################################################################################################
# Deployment
# ----------

echo PHP deployment

# 1. KuduSync
if [[ "$IN_PLACE_DEPLOYMENT" -ne "1" ]]; then
  "$KUDU_SYNC_CMD" -v 50 -f "$DEPLOYMENT_SOURCE" -t "$DEPLOYMENT_TARGET" -n "$NEXT_MANIFEST_PATH" -p "$PREVIOUS_MANIFEST_PATH" -i ".git;.hg;.deployment;deploy.sh"
  exitWithMessageOnError "Kudu Sync failed"
fi

# 3. Initialize Composer Config
initializeDeploymentConfig

# 4. Use composer
echo "$DEPLOYMENT_TARGET"
if [ -e "$DEPLOYMENT_TARGET/composer.json" ]; then
  echo "Found composer.json"
  pushd "$DEPLOYMENT_TARGET"
  php composer.phar install $COMPOSER_ARGS
  exitWithMessageOnError "Composer install failed"
  popd
fi
##################################################################################################################################
```

Зафиксируйте все изменения и повторно развернуть свой код. Будет запущен как часть развертывания автоматизации Composer.

## <a name="customize-start-up"></a>Настройка запуска

По умолчанию встроенный контейнер PHP запустите сервер Apache. При запуске он выполняется `apache2ctl -D FOREGROUND"`. Вы можете запустить другую команду при запуске, выполнив следующую команду в [Cloud Shell](https://shell.azure.com):

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
```

## <a name="access-environment-variables"></a>Доступ к переменным среды

В Службе приложений можно [задать параметры приложения](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings) вне кода приложения. Вы сможете обращаться к их с использованием стандарта [getenv()](https://secure.php.net/manual/function.getenv.php) шаблон. Например, для доступа к параметру приложения с именем `DB_HOST` используйте следующий код:

```php
getenv("DB_HOST")
```

## <a name="change-site-root"></a>Изменение корневого каталога сайта

Веб-платформу по своему усмотрению может использовать подкаталог в корневом узле. Например [Laravel](https://laravel.com/), использует `public/` подкаталог в корневом узле.

Образ PHP по умолчанию для службы приложений использует Apache и не позволяет настраивать корневой каталог сайта для вашего приложения. Чтобы обойти это ограничение, добавьте *.htaccess* файл в корневой папке репозитория со следующим содержимым:

```
<IfModule mod_rewrite.c>
    RewriteEngine on

    RewriteRule ^.*$ /public/$1 [NC,L,QSA]
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

Если вам нужно внести изменения в установку PHP, можно изменить любой из [директив php.ini](https://www.php.net/manual/ini.list.php) , выполнив следующие действия.

> [!NOTE]
> Оптимальный способ увидеть версию PHP и текущий *php.ini* конфигурации заключается в вызове [phpinfo()](https://php.net/manual/function.phpinfo.php) в вашем приложении.
>

### <a name="Customize-non-PHP_INI_SYSTEM directives"></a>Настройка без PHP_INI_SYSTEM директивы

Для настройки PHP_INI_USER PHP_INI_PERDIR и PHP_INI_ALL директивы (см. в разделе [директив php.ini](https://www.php.net/manual/ini.list.php)), добавьте *.htaccess* файл в корневой каталог приложения.

В *.htaccess* добавьте с помощью директивы `php_value <directive-name> <value>` синтаксис. Например:

```
php_value upload_max_filesize 1000M
php_value post_max_size 2000M
php_value memory_limit 3000M
php_value max_execution_time 180
php_value max_input_time 180
php_value display_errors On
php_value upload_max_filesize 10M
```

Повторное развертывание приложения с помощью изменения и перезапустите его. При развертывании с использованием Kudu (например, с помощью [Git](../deploy-local-git.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json)), оно автоматически перезапускается после развертывания.

В качестве альтернативы использованию *.htaccess*, можно использовать [ini_set()](https://www.php.net/manual/function.ini-set.php) в вашем приложении, чтобы настроить эти директивы не PHP_INI_SYSTEM.

### <a name="customize-phpinisystem-directives"></a>Настройка PHP_INI_SYSTEM директивы

Чтобы настроить PHP_INI_SYSTEM директивы (см. в разделе [директив php.ini](https://www.php.net/manual/ini.list.php)), нельзя использовать *.htaccess* подход. Служба приложений предоставляет отдельный механизм, с помощью `PHP_INI_SCAN_DIR` параметр приложения.

Во-первых, выполните следующую команду [Cloud Shell](https://shell.azure.com) добавить параметр приложения `PHP_INI_SCAN_DIR`:

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings PHP_INI_SCAN_DIR="/usr/local/etc/php/conf.d:/home/site/ini"
```

`/usr/local/etc/php/conf.d` — Это каталог по умолчанию где *php.ini* существует. `/home/site/ini` является пользовательском каталоге, в котором вы добавите пользовательский *INI-файле* файл. Разделяйте значения с `:`.

Перейдите к web сеанс SSH с Linux контейнера (`https://cephalin-container.scm.azurewebsites.net/webssh/host`).

Создайте каталог в `/home/site` вызывается `ini`, создайте *INI-файле* файл в `/home/site/ini` каталог (например, *settings.ini)* с директивы, которую нужно настроить. Используется тот же синтаксис, который будет использоваться в *php.ini* файл. 

> [!TIP]
> В контейнерах Linux, встроенные в службе приложений */home* используется в качестве постоянного общего хранилища. 
>

Например, чтобы изменить значение [expose_php](https://php.net/manual/ini.core.php#ini.expose-php) выполните следующие команды:

```bash
cd /home/site
mkdir ini
echo "expose_php = Off" >> ini/setting.ini
```

Чтобы изменения вступили в силу перезапустите приложение.

## <a name="enable-php-extensions"></a>Включение расширений PHP

Встроенные установке PHP содержит наиболее часто используемых расширений. Вы можете включить дополнительные расширения, в том же способом, которые [настроить директив php.ini](#customize-php_ini_system-directives).

> [!NOTE]
> Оптимальный способ увидеть версию PHP и текущий *php.ini* конфигурации заключается в вызове [phpinfo()](https://php.net/manual/function.phpinfo.php) в вашем приложении.
>

Чтобы включить дополнительные расширения, выполните следующие действия.

Добавить `bin` каталог в корневой каталог приложения и put `.so` расширения файлов в нем (например, *mongodb.so*). Убедитесь, что расширения совместимы с версией PHP в Azure и совместимы с VC9 и не поддерживающей многопоточное (nts).

Разверните изменения.

Выполните действия, описанные в [директивы настроить PHP_INI_SYSTEM](#customize-php_ini_system-directives), добавления расширений в пользовательский *INI-файле* файл с [расширение](https://www.php.net/manual/ini.core.php#ini.extension) или [zend_extension ](https://www.php.net/manual/ini.core.php#ini.zend-extension) директивы.

```ini
extension=/home/site/wwwroot/bin/mongodb.so
zend_extension=/home/site/wwwroot/bin/xdebug.so
```

Чтобы изменения вступили в силу перезапустите приложение.

## <a name="access-diagnostic-logs"></a>Доступ к журналам диагностики

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="open-ssh-session-in-browser"></a>Открытие сеанса SSH в браузере

[!INCLUDE [Open SSH session in browser](../../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Устранение неполадок

При работающем приложении PHP работает по-разному, в службе приложений или содержит ошибки, попробуйте сделайте следующее:

- [Получите доступ к потоку журнала](#access-diagnostic-logs).
- Тестирование приложения локально в рабочем режиме. Служба приложений запускает приложения Node.js в рабочем режиме, поэтому необходимо убедиться, что проект работает надлежащим образом в рабочем режиме локально. Например:
    - В зависимости от вашей *composer.json*, разные пакеты могут быть установлены для рабочий режим (`require` и `require-dev`).
    - Некоторые веб-платформы может развернуть статические файлы по-разному в рабочем режиме.
    - Некоторые веб-платформы может использовать специальные сценарии, при выполнении в рабочем режиме.
- Запустите приложение в службе приложений в режиме отладки. Например, в [Laravel](https://meanjs.org/), можно настроить приложение для вывода сообщения отладки в производственной среде с [параметр `APP_DEBUG` параметр приложения для `true` ](../configure-common.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#configure-app-settings).

### <a name="robots933456"></a>robots933456

Может появиться следующее сообщение в журнале контейнера:

```
2019-04-08T14:07:56.641002476Z "-" - - [08/Apr/2019:14:07:56 +0000] "GET /robots933456.txt HTTP/1.1" 404 415 "-" "-"
```

Это сообщение можно игнорировать. `/robots933456.txt` Это фиктивный путь URL-адрес, который служба приложений использует для проверки, если контейнер способен обслуживать запросы. Ответ 404 просто указывает, что путь не существует, но он позволяет службе приложений, которые знаете, что контейнер работоспособен и готов отвечать на запросы.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Учебник. по приложению PHP с MySQL](tutorial-php-mysql-app.md)

> [!div class="nextstepaction"]
> [Служба приложений под управлением Linux: вопросы и ответы](app-service-linux-faq.md)
