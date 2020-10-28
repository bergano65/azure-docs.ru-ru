---
title: Настройка приложений Python в Linux
description: Узнайте, как с помощью портала Azure и Azure CLI настроить контейнер Python для выполнения веб-приложений.
ms.topic: quickstart
ms.date: 10/06/2020
ms.reviewer: astay; kraigb
ms.custom: mvc, seodec18, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 935baef209811146d0b60f4fc02986818fd103a7
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92743780"
---
# <a name="configure-a-linux-python-app-for-azure-app-service"></a>Настройка приложения Python в Linux для Службы приложений Azure

В этой статье описывается, как [Служба приложений Azure](overview.md) запускает приложения Python, а также как вы можете настроить поведение Службы приложений при необходимости. Приложения Python нужно развертывать со всеми необходимыми модулями [pip](https://pypi.org/project/pip/).

Механизм развертывания Службы приложений автоматически активирует виртуальное окружение и запускает команду `pip install -r requirements.txt` при развертывании [репозитория Git](deploy-local-git.md) или [ZIP-пакета](deploy-zip.md).

Это руководство содержит основные понятия и инструкции для разработчиков Python, которые используют встроенный контейнер Linux в Службе приложений. Если вы раньше не использовали Службу приложений Azure, сначала ознакомьтесь с [кратким руководством по Python](quickstart-python.md) и [учебником по использованию Python с PostgreSQL](tutorial-python-postgresql-app.md).

Для настройки можно использовать [портал Azure](https://portal.azure.com) или Azure CLI.

- На **портале Azure** используйте страницу **Параметры** > **Конфигурация** , как описано в статье [Настройка приложения Службы приложений на портале Azure](configure-common.md).

- В **Azure CLI** можно действовать двумя способами:

    - запустить выполнение команд в службе [Azure Cloud Shell](../cloud-shell/overview.md), которую можно открыть с помощью кнопки **Попробовать** в правом верхнем углу блоков кода;
    - запустить выполнение команд локально, установив последнюю версию [Azure CLI](/cli/azure/install-azure-cli), а затем войти в Azure с помощью команды [az login](/cli/azure/reference-index#az-login).
    
> [!NOTE]
> Сейчас для запуска приложения Python в Службе приложений рекомендуется использовать Linux. Возможности использования Windows описаны в статье [Работа с Python в Службе приложений Azure (Windows)](/visualstudio/python/managing-python-on-azure-app-service).

## <a name="configure-python-version"></a>Настройка версии Python

- **Портал Azure.** Воспользуйтесь вкладкой **Общие параметры** на странице **Конфигурация** , как описано в разделе [Настройка общих параметров](configure-common.md#configure-general-settings) для контейнеров Linux.

- **Azure CLI.**

    -  Отобразите текущую версию Python с помощью команды [az webapp config show](/cli/azure/webapp/config#az_webapp_config_show).
    
        ```azurecli-interactive
        az webapp config show --resource-group <resource-group-name> --name <app-name> --query linuxFxVersion
        ```
        
        Замените `<resource-group-name>` и `<app-name>` именами, подходящими для вашего веб-приложения.
    
    - Задайте версию Python с помощью команды [az webapp config set](/cli/azure/webapp/config#az_webapp_config_set).
        
        ```azurecli-interactive
        az webapp config set --resource-group <resource-group-name> --name <app-name> --linux-fx-version "PYTHON|3.7"
        ```
    
    - Отобразите все версии Python, поддерживаемые в Службе приложений Azure, с помощью команды [az webapp list-runtimes](/cli/azure/webapp#az_webapp_list_runtimes).
    
        ```azurecli-interactive
        az webapp list-runtimes --linux | grep PYTHON
        ```
    
Вы можете запустить неподдерживаемую версию Python, выполнив сборку собственного образа контейнера. Дополнительные сведения см. в статье об [использовании пользовательского образа Docker](tutorial-custom-container.md?pivots=container-linux).

<!-- <a> element here to preserve external links-->
<a name="access-environment-variables"></a>

## <a name="customize-build-automation"></a>Настройка автоматизации сборки

При развертывании приложения с помощью пакетов Git или ZIP система сборки Службы приложений, именуемая Oryx, выполняет следующие действия:

1. Запускает пользовательский скрипт предварительной сборки, если он задан с помощью параметра `PRE_BUILD_COMMAND`.
1. Выполните `pip install -r requirements.txt`. В корневой папке проекта должен присутствовать файл *requirements.txt* . В противном случае в процессе сборки будет выведено сообщение об ошибке: Could not find setup.py or requirements.txt; Not running pip install (Не удалось найти setup.py или requirements.txt. Не запущена установка pip).
1. Если *manage.py* находится в корне репозитория (что характерно для приложения Django), запускает *manage.py collectstatic* . Однако если для параметра `DISABLE_COLLECTSTATIC` задано значение `true`, этот шаг пропускается.
1. Запускает пользовательский скрипт последующей сборки, если он задан с помощью параметра `POST_BUILD_COMMAND`.

По умолчанию параметры `PRE_BUILD_COMMAND`, `POST_BUILD_COMMAND` и `DISABLE_COLLECTSTATIC` пусты. 

- Чтобы отключить выполнение collectstatic при создании приложений Django, установите для параметра `DISABLE_COLLECTSTATIC` значение true.

- Чтобы выполнить команды перед сборкой, включите в параметр `PRE_BUILD_COMMAND` такую команду, как `echo Pre-build command`, или путь к файлу скрипта относительно корневого каталога проекта, например `scripts/prebuild.sh`. Во всех командах должны использоваться относительные пути к корневой папке проекта.

- Чтобы выполнить команды после сборки, включите в параметр `POST_BUILD_COMMAND` такую команду, как `echo Post-build command`, или путь к файлу скрипта относительно корневого каталога проекта, например `scripts/postbuild.sh`. Во всех командах должны использоваться относительные пути к корневой папке проекта.

Дополнительные параметры для настройки автоматизации сборки см. в статье [Конфигурация Oryx](https://github.com/microsoft/Oryx/blob/master/doc/configuration.md). 

Дополнительные сведения о том, как Служба приложений выполняет и создает приложения Python в Linux, см. в статье о том, как [Oryx выявляет и создает приложения Python](https://github.com/microsoft/Oryx/blob/master/doc/runtimes/python.md).

> [!NOTE]
> Параметры `PRE_BUILD_SCRIPT_PATH` и `POST_BUILD_SCRIPT_PATH` идентичны `PRE_BUILD_COMMAND` и `POST_BUILD_COMMAND` и поддерживаются из соображений совместимости с прежними версиями.
> 
> Если параметр с именем `SCM_DO_BUILD_DURING_DEPLOYMENT` содержит значение `true` или 1, он запускает сборку в Oryx во время развертывания. Этот параметр имеет значение true при развертывании с помощью Git, команды Azure CLI, `az webapp up` и Visual Studio Code.

> [!NOTE]
> Во всех скриптах, выполняемых до и после сборки, следует всегда использовать относительные пути, поскольку контейнер сборки, в котором выполняется Oryx, отличается от контейнера среды выполнения, в котором выполняется приложение. Никогда не полагайтесь на точное размещение папки проекта приложения в контейнере (например, размещение в папке *site/wwwroot* ).

## <a name="production-settings-for-django-apps"></a>Параметры рабочей среды для приложений Django

В такой рабочей среде, как Служба приложений Azure, для приложений Django необходимо придерживаться [контрольного списка развертывания](https://docs.djangoproject.com/en/3.1/howto/deployment/checklist/) (djangoproject.com).

В следующей таблице описаны параметры рабочей среды, относящиеся к Azure. Эти параметры определяются в файле *setting.py* приложения.

| Настройка Django | Инструкции для Azure |
| --- | --- |
| `SECRET_KEY` | Сохраните значение в настройках Службы приложений, как описано в разделе [Доступ к параметрам приложения в виде переменных среды](#access-app-settings-as-environment-variables). Можно также [сохранить значение в качестве секрета в Azure Key Vault](/azure/key-vault/secrets/quick-create-python). |
| `DEBUG` | Создайте параметр `DEBUG` в Службе приложений со значением 0 (false), а затем загрузите значение как переменную среды. В среде разработки создайте переменную среды `DEBUG` со значением 1 (true). |
| `ALLOWED_HOSTS` | В рабочей среде для Django требуется включить URL-адрес приложения в массив `ALLOWED_HOSTS` в *settings.py* . Этот URL-адрес можно получить во время выполнения с помощью кода `os.environ['WEBSITE_HOSTNAME']`. Служба приложений автоматически задает в качестве значения переменной среды `WEBSITE_HOSTNAME` URL-адрес приложения. |
| `DATABASES` | Определите параметры в Службе приложений для подключения к базе данных и загрузите их в виде переменных среды, чтобы заполнить словарь [`DATABASES`](https://docs.djangoproject.com/en/3.1/ref/settings/#std:setting-DATABASES). Можно также сохранить значения (особенно имя пользователя и пароль) в виде [секретов Azure Key Vault](/azure/key-vault/secrets/quick-create-python). |

## <a name="container-characteristics"></a>Характеристики контейнера

При развертывании в Службе приложений приложения Python выполняются в контейнере Linux Docker, который определен в [этом репозитории GitHub](https://github.com/Azure-App-Service/python). Конфигурации образов можно найти в каталогах для конкретных версий.

Этот контейнер отличается следующими характеристиками.

- Приложения запускаются с помощью [HTTP-сервера Gunicorn WSGI](https://gunicorn.org/), используя дополнительные аргументы `--bind=0.0.0.0 --timeout 600`.
    - Вы можете задать параметры конфигурации для Gunicorn с помощью файла *gunicorn.conf.py* в корневом каталоге проекта, как описано в [обзоре конфигурации Gunicorn](https://docs.gunicorn.org/en/stable/configure.html#configuration-file) (docs.gunicorn.org). Кроме того, можно [настроить команду запуска](#customize-startup-command).

    - Чтобы защитить веб-приложение от случайных или намеренных атак DDoS, Gunicorn запускается за обратным прокси-сервером Nginx, как описано в [документации по развертыванию Gunicorn](https://docs.gunicorn.org/en/latest/deploy.html) (docs.gunicorn.org).

- По умолчанию базовый образ контейнера включает в себя только веб-платформу Flask, но контейнер также поддерживает другие платформы, совместимые с WSGI и Python 3.6 и более новых версий, например Django.

- Чтобы установить дополнительные пакеты, такие как Django, создайте файл [*requirements.txt*](https://pip.pypa.io/en/stable/user_guide/#requirements-files) в корневой папке проекта, в котором будут заданы прямые зависимости. Тогда Служба приложений автоматически установит эти зависимости при развертывании проекта.

    Для установки зависимостей файл *requirements.txt* *должен* находиться в корневой папке проекта. В противном случае в процессе сборки будет выведено сообщение об ошибке: Could not find setup.py or requirements.txt; Not running pip install (Не удалось найти setup.py или requirements.txt. Не запущена установка pip). В случае этой ошибки проверьте расположение файла требований.

- Служба приложений автоматически определяет переменную среды с именем `WEBSITE_HOSTNAME`, используя URL-адрес веб-приложения, например `msdocs-hello-world.azurewebsites.net`. Она также определяет `WEBSITE_SITE_NAME`, используя имя приложения, например `msdocs-hello-world`. 
   
## <a name="container-startup-process"></a>Процесс запуска контейнера

Во время запуска служба приложений под управлением контейнера Linux выполнит следующие действия.

1. Используйте [пользовательскую команду запуска](#customize-startup-command), если предоставлена такая возможность.
2. Проверьте наличие [приложения Django](#django-app) и запустите для него сервер Gunicorn, если он обнаружен.
3. Проверьте наличие [приложения Flask](#flask-app) и запустите для него сервер Gunicorn, если он обнаружено.
4. Если другие приложения не найдены, запускается приложение по умолчанию, встроенное в контейнер.

В следующих разделах приведены дополнительные сведения о каждом параметре.

### <a name="django-app"></a>Приложение Django

Для приложений Django служба приложений выполняет поиск файла с именем `wsgi.py` в вашем коде приложения, а затем запускает Gunicorn, используя следующую команду:

```bash
# <module> is the name of the folder that contains wsgi.py
gunicorn --bind=0.0.0.0 --timeout 600 <module>.wsgi
```

Если необходим более точный контроль над командой запуска, используйте [пользовательские команды запуска](#customize-startup-command), замените `<module>` именем папки, содержащей *wsgi.py* , и добавьте аргумент `--chdir`, если этот модуль расположен не в корневой папке проекта. Например, если *wsgi.py* находится во вложенной папке *knboard/backend/config* корневой папки проекта, используйте аргументы `--chdir knboard/backend config.wsgi`.

Чтобы включить ведение журнала в рабочей среде, добавьте параметры `--access-logfile` и `--error-logfile`, как показано в примерах [пользовательских команд запуска](#customize-startup-command).

### <a name="flask-app"></a>Приложение Flask

Для Flask Служба приложений выполняет поиск файла с именем *application.py* или *app.py* и запускает Gunicorn следующим образом:

```bash
# If application.py
gunicorn --bind=0.0.0.0 --timeout 600 application:app

# If app.py
gunicorn --bind=0.0.0.0 --timeout 600 app:app
```

Если модуль основного приложения содержится в другом файле, используйте другое имя для объекта приложения или, если вы хотите указать дополнительные аргументы для Gunicorn, используйте [пользовательские команды запуска](#customize-startup-command).

### <a name="default-behavior"></a>Поведение по умолчанию

Если в службе приложений не найдена пользовательская команда, приложение Django или Flask, тогда она запускает приложение по умолчанию с разрешением только для чтения, расположенное в папке _opt/defaultsite_ . Приложение по умолчанию выглядит следующим образом:

![Служба приложений по умолчанию на веб-странице Linux](media/configure-language-python/default-python-app.png)

## <a name="customize-startup-command"></a>Настройка команды запуска

Как упоминалось ранее в этой статье, вы можете задать параметры конфигурации для Gunicorn с помощью файла *gunicorn.conf.py* в корневом каталоге проекта, как описано в [обзоре конфигурации Gunicorn](https://docs.gunicorn.org/en/stable/configure.html#configuration-file).

Если такой конфигурации недостаточно, для управления поведением при запуске контейнера можно указать в файле команд запуска пользовательскую команду запуска или несколько команд. Для файла команд запуска можно использовать любое выбранное имя, например *startup.sh* , *startup.cmd* , *startup.txt* и т. д.

Во всех командах должны использоваться относительные пути к корневой папке проекта.

Чтобы указать команду или файл команд запуска, выполните следующие действия.

- **Портал Azure.** Перейдите на страницу **Конфигурация** приложения, а затем выберите **Общие параметры** . В поле **Команда запуска** вставьте полный текст команды запуска либо укажите имя файла команд запуска. Затем нажмите кнопку **Сохранить** , чтобы применить изменения. Сведения о контейнерах Linux см. в разделе [Настройка общих параметров](configure-common.md#configure-general-settings).

- **Azure CLI.** Используйте команду [az webapp config set](/cli/azure/webapp/config#az_webapp_config_set) с параметром `--startup-file`, чтобы задать команду или файл запуска.

    ```azurecli-interactive
    az webapp config set --resource-group <resource-group-name> --name <app-name> --startup-file "<custom-command>"
    ```
        
    Замените `<custom-command>` полным текстом команды запуска или именем файла команд запуска.
        
Служба приложений игнорирует все ошибки, происходящие при обработке пользовательской команды или файла запуска, и продолжает процесс запуска путем поиска приложений Django и Flask. Если поведение отличается от ожидаемого, проверьте, нет ли ошибок в команде или файле запуска, и убедитесь, что файл команд запуска развернут в Службе приложений вместе с кодом вашего приложения. Дополнительные сведения можно также почерпнуть из [журналов диагностики](#access-diagnostic-logs). Просмотрите также страницу **Диагностика и решение проблем** на [портале Azure](https://portal.azure.com).

### <a name="example-startup-commands"></a>Примеры команд запуска

- **Добавление аргументов Gunicorn** . В следующем примере в командную строку Gunicorn для запуска приложения Django добавляется `--workers=4`. 

    ```bash
    # <module-path> is the relative path to the folder that contains the module
    # that contains wsgi.py; <module> is the name of the folder containing wsgi.py.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi
    ```    

    Дополнительные сведения см. в статье [Running Gunicorn](https://docs.gunicorn.org/en/stable/run.html) (Запуск Gunicorn) (docs.gunicorn.org).

- **Включение ведения журнала для Django в рабочей среде** . Добавьте в командную строку аргументы `--access-logfile '-'` и `--error-logfile '-'`.

    ```bash    
    # '-' for the log files means stdout for --access-logfile and stderr for --error-logfile.
    gunicorn --bind=0.0.0.0 --timeout 600 --workers=4 --chdir <module_path> <module>.wsgi --access-logfile '-' --error-logfile '-'
    ```    

    Эти журналы будут отображаться в [потоке журналов Службы приложений](#access-diagnostic-logs).

    Дополнительные сведения см. в разделе о [ведении журналов Gunicorn](https://docs.gunicorn.org/en/stable/settings.html#logging) (docs.gunicorn.org).
    
- **Настраиваемый основной модуль Flask** . По умолчанию Служба приложений предполагает, что основным модулем приложения Flask является *application.py* или *app.py* . Если у основного модуля другое имя, необходимо настроить команду запуска. Например, если вы используете приложение Flask, основным модулем которого является *hello.py* , а объект приложения Flask в этом файле имеет имя `myapp`, команда будет выглядеть следующим образом:

    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 hello:myapp
    ```
    
    Если главный модуль находится в подпапке, например `website`, укажите эту подпапку с помощью аргумента `--chdir`:
    
    ```bash
    gunicorn --bind=0.0.0.0 --timeout 600 --chdir website hello:myapp
    ```
    
- **Использование сервера, отличного от Gunicorn** . Чтобы использовать другой веб-сервер, например [aiohttp](https://aiohttp.readthedocs.io/en/stable/web_quickstart.html), используйте соответствующую команду в качестве команды запуска или в файле команд запуска.

    ```bash
    python3.7 -m aiohttp.web -H localhost -P 8080 package.module:init_func
    ```

## <a name="access-app-settings-as-environment-variables"></a>Доступ к параметрам приложения в виде переменных среды

Параметры приложения — это значения, хранящиеся в облаке специально для вашего приложения, как описано в разделе [Настройка параметров приложения](configure-common.md#configure-app-settings). Эти параметры доступны для кода приложения в виде переменных среды и вызываются с помощью стандартного шаблона [os.environ](https://docs.python.org/3/library/os.html#os.environ).

Например, если вы создали параметр приложения с именем `DATABASE_SERVER`, следующий код извлечет значение этого параметра.

```python
db_server = os.environ['DATABASE_SERVER']
```
    
## <a name="detect-https-session"></a>Обнаружение сеанса HTTPS

В Службе приложений [завершение SSL-запросов](https://wikipedia.org/wiki/TLS_termination_proxy) (wikipedia.org) происходит в подсистеме балансировки нагрузки сети, поэтому все HTTPS-запросы достигают вашего приложения в виде незашифрованных HTTP-запросов. Если логика вашего приложения проверяет, зашифрованы ли пользовательские запросы, проверяйте заголовок `X-Forwarded-Proto`.

```python
if 'X-Forwarded-Proto' in request.headers and request.headers['X-Forwarded-Proto'] == 'https':
# Do something when HTTPS is used
```

Популярные веб-платформы позволяют получить доступ к информации `X-Forwarded-*` в стандартном шаблоне приложения. В [CodeIgniter](https://codeigniter.com/) функция [is_https()](https://github.com/bcit-ci/CodeIgniter/blob/master/system/core/Common.php#L338-L365) по умолчанию проверяет значение `X_FORWARDED_PROTO`.

## <a name="access-diagnostic-logs"></a>Доступ к журналам диагностики

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

Чтобы получить доступ к журналам с помощью портала Azure, в приложении в меню слева выберите **Мониторинг** > **Поток журналов** .

## <a name="open-ssh-session-in-browser"></a>Открытие сеанса SSH в браузере

[!INCLUDE [Open SSH session in browser](../../includes/app-service-web-ssh-connect-builtin-no-h.md)]

## <a name="troubleshooting"></a>Устранение неполадок

- **После развертывания кода приложения отображается приложение по умолчанию.** Приложение по умолчанию отображается, потому что вы либо не развертывали код приложения в Службу приложений, либо она не смогла найти ваш код приложения и вместо этого использовала приложение по умолчанию.

    - Перезапустите службу приложений, подождите 15–20 секунд и снова проверьте приложение.
    
    - Убедитесь, что вы используете службу приложений для Linux, а не экземпляр для Windows. В Azure CLI выполните команду `az webapp show --resource-group <resource-group-name> --name <app-name> --query kind`, заменив `<resource-group-name>` и `<app-service-name>` соответствующими значениями. Команда должна вывести строку `app,linux`. Если этого не произошло, заново создайте службу приложений и выберите Linux.
    
    - Используйте SSH или консоль Kudu для подключения непосредственно к службе приложений и убедитесь, что файлы существуют в каталоге *site/wwwroot* . Если файлов нет, проверьте процесс развертывания и повторно разверните приложение.
    
    - Если файлы имеются, значит службе приложений не удалось определить конкретный загрузочный файл. Убедитесь, что ваше приложение структурировано, так как служба приложений ожидает [Djangо](#django-app) или [Flask](#flask-app) или использует [пользовательскую команду запуска](#customize-startup-command).

- **В браузере появилось сообщение "Служба недоступна".** Истекло время ожидания браузером ответа от службы приложений, что указывает, что она запустила сервер Gunicorn, но аргументы, указывающие код приложения, неверные.

    - Обновите браузер, особенно если вы используете самую низкую ценовую категорию в плане службы приложений. Приложению может потребоваться больше времени для запуска, например при использовании бесплатных уровней, и оно начнет отвечать после обновления браузера.

    - Убедитесь, что ваше приложение структурировано, так как служба приложений ожидает [Djangо](#django-app) или [Flask](#flask-app) или использует [пользовательскую команду запуска](#customize-startup-command).

    - Проверьте, нет ли в [потоке журналов](#access-diagnostic-logs) сообщений об ошибках.

- **В потоке журналов отображается сообщение Could not find setup.py or requirements.txt; Not running pip install (Не удалось найти setup.py или requirements.txt. Не запущена установка pip)** . Процессу сборки Oryx не удалось найти файл *requirements.txt* .

    - Используйте SSH или консоль Kudu для подключения непосредственно к Службе приложений и убедитесь, что файл *requirements.txt* находится непосредственно в папке *site/wwwroot* . Если он там отсутствует, убедитесь, что он существует в репозитории и включен в развертывание. Если он находится в отдельной папке, переместите его в корневую папку.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Руководство. по использованию приложения Python с PostgreSQL](tutorial-python-postgresql-app.md)

> [!div class="nextstepaction"]
> [Руководство. по развертыванию из частного репозитория контейнеров](tutorial-custom-container.md?pivots=container-linux)

> [!div class="nextstepaction"]
> [Служба приложений под управлением Linux: вопросы и ответы](faq-app-service-linux.md)
