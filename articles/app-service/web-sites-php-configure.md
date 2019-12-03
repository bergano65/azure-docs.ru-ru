---
title: Настройка среды выполнения PHP
description: Узнайте, как настроить установку PHP по умолчанию или добавить пользовательскую установку PHP в службе приложений Azure.
author: msangapu-msft
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.devlang: php
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 2d35c31e23da7addcf0b4c341c6925f258d5c232
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688264"
---
# <a name="configure-php-in-azure-app-service"></a>Настройка PHP в службе приложений Azure

## <a name="introduction"></a>Общие сведения

В этом руководстве показано, как настроить встроенную среду выполнения PHP в веб-приложениях, серверных частях мобильных приложений и API-приложениях [службы приложений Azure](https://go.microsoft.com/fwlink/?LinkId=529714), предоставить настраиваемую среду выполнения PHP и включить расширения. Чтобы использовать службу приложений, подпишитесь на [бесплатную пробную версию]. Чтобы это руководство было для вас максимально эффективным, необходимо сначала создать приложение PHP в службе приложений.

## <a name="how-to-change-the-built-in-php-version"></a>Практическое руководство. Изменение встроенной версии PHP

По умолчанию при создании приложения службы приложений устанавливается среда PHP 5.6, которая будет сразу готова для использования. Оптимальный способ увидеть доступные версии, конфигурацию по умолчанию и поддерживаемые расширения заключается в развертывании сценария, который вызывает функцию [phpinfo()] .

Версии PHP 7.0 и PHP 7.2 также доступны, но не включены по умолчанию. Чтобы обновить версию PHP, выполните следующие действия.

### <a name="azure-portal"></a>портала Azure

1. Перейдите к приложению в [портал Azure](https://portal.azure.com) и прокрутите страницу **конфигурации** .

2. В окне **Конфигурация**выберите **Общие параметры** и выберите новую версию PHP.

3. Нажмите кнопку **сохранить** в верхней части колонки **Общие параметры** .

### <a name="azure-powershell-windows"></a>Azure PowerShell (только для Windows)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Запустите Azure PowerShell и войдите в свою учетную запись.

        PS C:\> Connect-AzAccount
2. Укажите версию PHP для приложения.

        PS C:\> Set-AzureWebsite -PhpVersion {5.6 | 7.0 | 7.2} -Name {app-name}
3. После этого версия PHP будет установлена. Можно подтвердить эти параметры.

        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-cli"></a>Azure CLI 

Для использования интерфейса командной строки Azure на компьютер требуется [установить Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

1. Откройте терминал и войдите в свою учетную запись.

        az login

1. Проверьте список поддерживаемых сред выполнения.

        az webapp list-runtimes | grep php

2. Укажите версию PHP для приложения.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

3. После этого версия PHP будет установлена. Можно подтвердить эти параметры.

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Практическое руководство. Изменение встроенной конфигурации PHP

Для любой встроенной среды выполнения PHP можно изменить параметры конфигурации, выполнив приведенные ниже действия. (Дополнительные сведения о директивах php.ini см. в разделе [Список директив php.ini].)

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>Изменение параметров конфигурации PHP\_INI\_USER, PHP\_INI\_PERDIR, PHP\_INI\_ALL

1. Добавьте файл [.user.ini] в корневой каталог.
1. Добавьте параметры конфигурации в файл `.user.ini`, используя тот же синтаксис, что и для файла `php.ini`. Например, чтобы включить параметр `display_errors` и установить для параметра `upload_max_filesize` значение 10 МБ, в файле `.user.ini` будет указан следующий текст.

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Развертывание приложения.
3. Перезапустите приложение. (Перезапуск необходим, так как частота считывания PHP файлов `.user.ini` регулируется параметром `user_ini.cache_ttl`, который является параметром уровня системы; по умолчанию значение этого параметра составляет 300 секунд (5 минут). Перезапуск приложения заставляет PHP считать новые параметры из файла `.user.ini`.)

В качестве альтернативы использованию файла `.user.ini` можно использовать функцию [ini_set()] в скрипте для установки параметров конфигурации, не являющихся директивами системного уровня.

### <a name="changing-php_ini_system-configuration-settings"></a>Изменение параметров конфигурации PHP\_INI\_SYSTEM

1. Добавьте параметр приложения для приложения с помощью ключа `PHP_INI_SCAN_DIR` и значения `d:\home\site\ini`
1. Создание файл `settings.ini` с помощью консоли Kudu (http://&lt;имя_узла&gt;.scm.azurewebsite.net) в каталоге `d:\home\site\ini`.
1. Добавьте параметры конфигурации в файл `settings.ini`, используя тот же синтаксис, что и для файла `php.ini`. Например, если необходимо, чтобы параметр `curl.cainfo` указывал на файл `*.crt`, а параметр wincache.maxfilesize имел значение 512K, файл `settings.ini` должен содержать следующий текст:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Чтобы перезагрузить изменения, перезапустите приложение.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Практическое руководство. Включение расширений в среде выполнения PHP по умолчанию

Как было отмечено в предыдущем разделе, оптимальный способ увидеть версию PHP по умолчанию, конфигурацию по умолчанию и поддерживаемые расширения заключается в развертывании сценария, который вызывает функцию [phpinfo()]. Чтобы включить дополнительные расширения, выполните следующие действия.

### <a name="configure-via-ini-settings"></a>Настройка с помощью параметров ini

1. Добавьте каталог `ext` в каталог `d:\home\site`.
1. Поместите файлы с расширением `.dll` в каталог `ext` (например, `php_xdebug.dll`). Убедитесь, что расширения совместимы с версией PHP по умолчанию, а также совместимы с VC9 и непотокобезопасной технологией (nts).
1. Добавьте параметр приложения для приложения с помощью ключа `PHP_INI_SCAN_DIR` и значения `d:\home\site\ini`
1. Создайте файл `ini` в `d:\home\site\ini` с именем `extensions.ini`.
1. Добавьте параметры конфигурации в файл `extensions.ini`, используя тот же синтаксис, что и для файла `php.ini`. Например, чтобы включить расширения MongoDB и XDebug, файл `extensions.ini` должен содержать следующий текст:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Перезапустите приложение для загрузки изменений.

### <a name="configure-via-app-setting"></a>Настройка с помощью параметров приложения

1. Добавьте каталог `bin` в корневой каталог.
2. Поместите файлы с расширением `.dll` в каталог `bin` (например, `php_xdebug.dll`). Убедитесь, что расширения совместимы с версией PHP по умолчанию, а также совместимы с VC9 и непотокобезопасной технологией (nts).
3. Развертывание приложения.
4. Перейдите к приложению в портал Azure и щелкните **конфигурацию** , расположенную ниже раздела **Параметры** .
5. В колонке **Конфигурация** выберите **Параметры приложения**.
6. В разделе **Параметры приложения** щелкните **+ создать параметр приложения** и создайте ключ **PHP_EXTENSIONS** . Значением для этого ключа будет путь относительно корневого веб-сайта: **bin\ваш-внешний-файл**.
7. Нажмите кнопку **Обновить** внизу и нажмите кнопку **сохранить** над вкладкой **Параметры приложения** .

Расширения Zend также поддерживаются с помощью ключа **PHP_ZENDEXTENSIONS**. Чтобы включить несколько расширений, включите список разделенных запятыми файлов `.dll` для значения параметра приложения.

## <a name="how-to-use-a-custom-php-runtime"></a>Практическое руководство. Настраиваемая среда выполнения PHP

Вместо среды выполнения PHP по умолчанию служба приложений может использовать среду выполнения PHP, предоставляемую для выполнения скриптов PHP. Предоставляемую среду выполнения можно настроить с помощью файла `php.ini`, также предоставляемого пользователем. Чтобы использовать настраиваемую среду выполнения PHP со службой приложений, выполните следующие действия.

1. Получите версию PHP для Windows, совместимую с VC9 или VC11 и непотокобезопасной технологией (nts). Последние версии PHP для Windows можно найти здесь: [https://windows.php.net/download/]. Более старые версии можно найти в архиве, доступном здесь: [https://windows.php.net/downloads/releases/archives/].
2. Измените файл `php.ini` для среды выполнения. Все параметры конфигурации, являющиеся директивами только уровня системы, не будут учитываться службой приложений. (Дополнительные сведения о директивах только системного уровня см. в разделе [Список директив php.ini].)
3. При необходимости добавьте расширения для вашей среды выполнения PHP и включите их в файл `php.ini` .
4. Добавьте каталог `bin` в корневой каталог и поместите в него каталог, в котором содержится ваша среда выполнения PHP (например, `bin\php`).
5. Развертывание приложения.
6. Перейдите к приложению в портал Azure и щелкните колонку **Конфигурация** .
8. В колонке **Конфигурация** выберите **сопоставления путей**. 
9. Щелкните **+ создать обработчик** и добавьте `*.php` в поле расширения и добавьте путь к `php-cgi.exe` исполняемому файлу в **обработчике скриптов**. Если поместить среду выполнения PHP в каталог `bin` в корне приложения, путь будет выглядеть так: `D:\home\site\wwwroot\bin\php\php-cgi.exe`.
10. В нижней части щелкните **Обновить** , чтобы завершить Добавление сопоставления обработчика.
11. Щелкните **Сохранить**, чтобы сохранить изменения.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Практическое руководство. Включение автоматизации Composer в Azure

По умолчанию служба приложений не выполняет никаких действий с файлом composer.json, если он есть в проекте PHP. Если используется [развертывание Git](deploy-local-git.md), можно включить обработку composer.json во время операции `git push`, для этого активируйте расширение Composer.

> [!NOTE]
> [Здесь](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)вы можете отдать свой голос за первоклассную поддержку Composer в службе приложений!
>

1. На [портале Azure](https://portal.azure.com) в колонке вашего приложения PHP щелкните **Инструменты**  >  **Расширения**.

    ![Колонка параметров портала Azure для включения автоматизации Composer в Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Щелкните **Добавить**, а затем — **Composer**.

    ![Добавление расширения Composer для включения автоматизации Composer в Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Нажмите кнопку **ОК** , чтобы принять условия использования. Нажмите кнопку **ОК** еще раз, чтобы добавить расширение.

    В колонке **Установленные расширения** появится расширение Composer.
    ![Примите условия использования для включения автоматизации Composer в Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Теперь в окне терминала на локальном компьютере выполните команды `git add`, `git commit` и `git push` для приложения. Вы увидите, что расширение Composer устанавливает зависимости, определенные в файле composer.json.

    ![Развертывание Git с помощью автоматизации Composer в Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Дальнейшие действия

Дополнительную информацию можно найти в [Центре разработчика PHP](https://azure.microsoft.com/develop/php/).

[бесплатную пробную версию]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Список директив php.ini]: https://www.php.net/manual/en/ini.list.php
[.user.ini]: https://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: https://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[https://windows.php.net/download/]: https://windows.php.net/download/
[https://windows.php.net/downloads/releases/archives/]: https://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
