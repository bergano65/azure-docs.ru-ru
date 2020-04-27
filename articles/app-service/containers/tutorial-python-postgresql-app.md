---
title: Руководство по Развертывание Python (Django) с помощью Postgres
description: Узнайте, как создать приложение Python с использованием базы данных PostgreSQL и развернуть его в Службе приложений Azure в Linux. В этом руководстве для демонстрации используется пример приложения Django.
ms.devlang: python
ms.topic: tutorial
ms.date: 04/14/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 0c9329b46d096df1afab6f7e457d143f9c6504be
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085762"
---
# <a name="tutorial-deploy-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Руководство по развертыванию веб-приложения Python (Django) с PostgreSQL в Службе приложений Azure

В этом руководстве показано, как развернуть веб-приложение Python (Django) на основе данных в [Службе приложений Azure](app-service-linux-intro.md) и подключить его к Базе данных Azure для PostgreSQL. Служба приложений — служба веб-размещения с автоматической установкой исправлений и высоким уровнем масштабируемости.

![Развертывание веб-приложения Python Django в Службе приложений Azure](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

В этом руководстве описано следующее:

> [!div class="checklist"]
> * создание базы данных в службе Базы данных Azure для PostgreSQL;
> * развертывание кода в Службе приложений Azure и подключение к Postgres;
> * обновление кода и повторное развертывание;
> * просмотр журналов диагностики;
> * управление веб-приложением на портале Azure.

Инструкции из этой статьи вы можете выполнять в операционных системах macOS, Linux или Windows.

## <a name="install-dependencies"></a>Установка зависимостей

Перед началом работы с этим руководством сделайте следующее:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Установите [Azure CLI](/cli/azure/install-azure-cli).
- Установите [Git](https://git-scm.com/).
- Установите [Python 3](https://www.python.org/downloads/).

## <a name="clone-the-sample-app"></a>Клонирования примера приложения

В окне терминала выполните следующие команды, чтобы клонировать репозиторий с примером приложения и перейти в корневой каталог репозитория.

```
git clone https://github.com/Azure-Samples/djangoapp
cd djangoapp
```

Репозиторий djangoapp с примерами содержит управляемое данными приложение [Django](https://www.djangoproject.com/) для опросов, которое вы можете получить, выполнив [инструкции по созданию первого приложения](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) из документации по Django. Мы дублируем их здесь для удобства.

## <a name="prepare-app-for-app-service"></a>Подготовка приложения для Службы приложений

Как и многие другие веб-платформы Python, Django [требует внести определенные изменения для запуска на рабочем сервере](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/), в том числе для Службы приложений. Измените и добавьте некоторые параметры в стандартном файле *azuresite/settings.py*, чтобы приложение нормально работало после развертывания в Службе приложений. 

Взгляните на пример *azuresite/settings.py* с правильной конфигурацией для Службы приложений. В нем выполняются следующие действия:

- Наследуются все параметры из *azuresite/settings.py*.
- Добавляется полное доменное имя приложения Службы приложений в список разрешенных узлов. 
- Применяется [WhiteNoise](https://whitenoise.evans.io/en/stable/) для поддержки обслуживания статических файлов в рабочей среде, так как по умолчанию Django не обслуживает статические файлы в рабочей среде. Пакет WhiteNoise уже входит в *requirements.txt*.
- Добавляется конфигурация для базы данных PostgreSQL. По умолчанию Django использует базу данных Sqlite3, но она не подходит для рабочих приложений. Пакет [psycopg2-binary](https://pypi.org/project/psycopg2-binary/) уже включен в *requirements.txt*.
- В конфигурации Postgres используются переменные среды. Позже вы узнаете, как настроить переменные среды в Службе приложений.

*azuresite/production.py* для удобства включен в репозиторий, но пока не используется приложением. Чтобы параметры из этого файла применялись в Службе приложений, необходимо настроить доступ к нему в двух файлах: *manage.py* и *azuresite/wsgi.py*.

- Измените следующую строку в *manage.py*:

    <pre>
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    </pre>

    В следующий код:

    ```python
    if os.environ.get('DJANGO_ENV') == 'production':
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.production')
    else:
        os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'azuresite.settings')
    ```

    Переменную среды `DJANGO_ENV` вы укажете позже при настройке приложения Службы приложений.

- В *azuresite/wsgi.py* внесите те же изменения, что и выше.

    В Службе приложений файл *manage.py* отвечает за выполнение переноса баз данных, а файл *azuresite/wsgi.py* используется для запуска приложения Django в рабочей среде. Внеся изменение в оба файла, вы гарантируете применение правильных параметров рабочей среды в обоих сценариях.

## <a name="sign-in-to-azure-cli"></a>Вход в Azure CLI

У вас должен быть установлен Azure CLI. [Azure CLI](/cli/azure/what-is-azure-cli) позволяет работать с ресурсами Azure из терминала командной строки. 

Войдите в Azure с помощью команды [`az login`](/cli/azure/reference-index#az-login).

```azurecli
az login
```

Следуйте инструкциям в терминале, чтобы войти в учетную запись Azure. Когда все будет готово, в выходных данных терминала будут перечислены подписки в формате JSON.

## <a name="create-postgres-database-in-azure"></a>Создание базы данных Postgres в Azure

<!-- > [!NOTE]
> Before you create an Azure Database for PostgreSQL server, check which [compute generation](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores) is available in your region. If your region doesn't support Gen4 hardware, change *--sku-name* in the following command line to a value that's supported in your region, such as B_Gen4_1.  -->

В этом разделе описано, как создать сервер Базы данных Azure для PostgreSQL и саму базу данных. Для начала установите расширение `db-up`, выполнив следующую команду:

```azurecli
az extension add --name db-up
```

Создайте базу данных Postgres в Azure с помощью команды [`az postgres up`](/cli/azure/ext/db-up/postgres#ext-db-up-az-postgres-up), как показано в приведенном ниже примере. Замените заполнитель *\<postgresql-name>* *уникальным* именем (конечная точка сервера — *https://\<postgresql-name>.postgres.database.azure.com*). Для значений *\<admin-username>* и *\<admin-password>* укажите учетные данные, чтобы создать пользователя с правами администратора для сервера Postgres.

<!-- Issue: without --location -->
```azurecli
az postgres up --resource-group myResourceGroup --location westus2 --server-name <postgresql-name> --database-name pollsdb --admin-user <admin-username> --admin-password <admin-password> --ssl-enforcement Enabled
```

Эта команда может выполняться довольно долго, так как осуществляются такие задачи:

- Создается [группа ресурсов](../../azure-resource-manager/management/overview.md#terminology) с именем `myResourceGroup`, если она еще не существует. Каждый ресурс Azure должен находиться в одной из них. Аргумент `--resource-group` является необязательным.
- Создается сервер Postgres и пользователь с правами администратора.
- Создается база данных `pollsdb`.
- Разрешается доступ с локального IP-адреса.
- Разрешается доступ из служб Azure.
- Создается пользователь базы данных с доступом к базе данных `pollsdb`.

Все эти шаги можно выполнять отдельно с помощью других команд `az postgres` и `psql`, но `az postgres up` выполняет все эти действия за один шаг.

Когда команда будет выполнена, найдите в выходных данных строки, которые начинаются с `Ran Database Query:`. Здесь отображаются сведения о созданном пользователе базы данных: имя пользователя `root` и пароль `Pollsdb1`. Они пригодятся вам позже для подключения приложения к базе данных.

<!-- not all locations support az postgres up -->
> [!TIP]
> Чтобы указать расположение сервера Postgres, добавьте аргумент `--location <location-name>`, где `<location_name>` соответствует одному из [регионов Azure](https://azure.microsoft.com/global-infrastructure/regions/). Список регионов, доступных для вашей подписки, можно получить с помощью команды [`az account list-locations`](/cli/azure/account#az-account-list-locations).

## <a name="deploy-the-app-service-app"></a>Развертывание приложения Службы приложений

В этом разделе описано, как создать приложение Службы приложений. Вы подключите это приложение к созданной базе данных Postgres и развернете код.

### <a name="create-the-app-service-app"></a>Создание приложения Службы приложений

<!-- validation error: Parameter 'ResourceGroup.location' can not be None. -->
<!-- --resource-group is not respected at all -->

Обязательно перейдите в корень репозитория (`djangoapp`), так как приложение будет развернуто из этого каталога.

Создайте приложение Службы приложений с помощью команды [`az webapp up`](/cli/azure/webapp#az-webapp-up), как показано в приведенном ниже примере. Замените *\<app-name>* *уникальным* именем (конечная точка сервера — *https://\<app-name>.azurewebsites.net*). Для *\<app-name>* допускаются символы `A`-`Z`, `0`-`9` и `-`.

```azurecli
az webapp up --plan myAppServicePlan --sku B1 --name <app-name>
```
<!-- !!! without --sku creates PremiumV2 plan!! -->

Эта команда может выполняться довольно долго, так как осуществляются такие задачи:

<!-- - Create the resource group if it doesn't exist. `--resource-group` is optional. -->
<!-- No it doesn't. az webapp up doesn't respect --resource-group -->
- Автоматически создается [группу ресурсов](../../azure-resource-manager/management/overview.md#terminology).
- Создается [план службы приложений](../overview-hosting-plans.md) *myAppServicePlan* в ценовой категории "Базовый" (B1), если он еще не существует. Параметры `--plan` и `--sku` являются необязательными.
- Создается приложение службы приложений, если оно еще не существует.
- Для приложения включается ведение журнала по умолчанию, если оно еще не включено.
- Передается репозиторий через развертывание на основе ZIP-файла с включенной автоматизацией сборки.

Когда развертывание завершится, вы получите выходные данные в формате JSON следующего вида:

<pre>
{
  "URL": "http://&lt;app-name&gt;.azurewebsites.net",
  "appserviceplan": "myAppServicePlan",
  "location": "westus",
  "name": "&lt;app-name&gt;",
  "os": "Linux",
  "resourcegroup": "&lt;app-resource-group&gt;",
  "runtime_version": "python|3.7",
  "runtime_version_detected": "-",
  "sku": "BASIC",
  "src_path": "//var//lib//postgresql//djangoapp"
}
</pre>

Скопируйте значение *\<app-resource-goup>* . Оно вам потребуется позже для настройки приложения. 

> [!TIP]
> Вы можете применить ту же команду позже, чтобы развернуть изменения и немедленно включить ведение журналов диагностики:
> 
> ```azurecli
> az webapp up --name <app-name>
> ```

Теперь пример кода развернут, но приложение еще не подключено к базе данных Postgres в Azure. Это вы сделаете прямо сейчас.

### <a name="configure-environment-variables"></a>Настройка переменных среды

При локальном запуске приложения переменные среды можно задать в сеансе терминала. В Службе приложений для этого используются *параметры приложения*, которые устанавливаются командой [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set).

Выполните следующую команду, чтобы указать сведения о подключении к базе данных в параметрах приложения. Замените заполнители *\<app-name>* , *\<app-resource-group>* и *\<postgresql-name>* реальными значениями. Помните, что учетные данные пользователя `root` и `Pollsdb1` созданы с помощью `az postgres up`.

```azurecli
az webapp config appsettings set --name <app-name> --resource-group <app-resource-group> --settings DJANGO_ENV="production" DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="root@<postgresql-name>" DBPASS="Pollsdb1" DBNAME="pollsdb"
```

См. сведения о том, как [обращаться к переменным среды из кода приложения](how-to-configure-python.md#access-environment-variables).

### <a name="run-database-migrations"></a>Выполнение миграций баз данных

Чтобы выполнить миграцию баз данных в Службе приложений, откройте в браузере сеанс SSH, перейдя по адресу *https://\<app-name>.scm.azurewebsites.net/webssh/host*:

<!-- doesn't work when container not started -->
<!-- ```azurecli
az webapp ssh --resource-group myResourceGroup --name <app-name>
``` -->

Выполните следующие команды в сеансе SSH:

```bash
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Install requirements in environment
pip install -r requirements.txt
# Run database migrations
python manage.py migrate
# Create the super user (follow prompts)
python manage.py createsuperuser
```

### <a name="browse-to-the-azure-app"></a>Переход к приложению Azure

Откройте развернутое приложение в браузере по URL-адресу *http:\//\<app-name>.azurewebsites.net*. Должно отобразиться сообщение **No polls are available** (Доступных опросов нет). 

Откройте адрес *http:\//\<app-name>.azurewebsites.net/admin* и войдите с именем администратора, который был создан на предыдущем шаге. Щелкните **Add** (Добавить) возле поля **Questions** (Вопросы) и создайте вопрос с несколькими вариантами ответа.

Перейдите к развернутому приложению по URL-адресу *http:\//\<app-name>.azurewebsites.net/admin* и создайте несколько вопросов. Эти вопросы можно просмотреть по адресу *http:\//\<app-name>.azurewebsites.net/* . 

![Запуск приложения Python (Django) в службах приложений в Azure](./media/tutorial-python-postgresql-app/deploy-python-django-app-in-azure.png)

Снова перейдите к развернутому приложению по URL-адресу *http:\//\<app-name>.azurewebsites.net*, чтобы просмотреть вопросы и ответить на них.

В репозитории Служба приложений определяет проекты Django по файлу *wsgi.py* в каждом подкаталоге, который создается в `manage.py startproject` по умолчанию. Обнаружив такой файл, Служба приложений загружает веб-приложение Django. Дополнительные сведения о том, как загружаются приложения Python с помощью Службы приложений Azure, см. [здесь](how-to-configure-python.md).

**Поздравляем!** Вы запустили веб-приложение Python (Django) в Службе приложений Azure для Linux.

## <a name="develop-app-locally-and-redeploy"></a>Разработка приложения на локальном компьютере и повторное развертывание

В этом разделе показано, как внести изменения в приложение в локальной среде и повторно развернуть этот код в Службе приложений.

### <a name="set-up-locally-and-run"></a>Локальная настройка и выполнение

Чтобы настроить локальную среду разработки и в первый раз запустить пример приложения, выполните следующие команды:

# <a name="bash"></a>[bash](#tab/bash)

```bash
# Configure the Python virtual environment
python3 -m venv venv
source venv/bin/activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
# Configure the Python virtual environment
py -3 -m venv venv
Set-ExecutionPolicy -Scope CurrentUser -ExecutionPolicy RemoteSigned -Force
venv\scripts\activate

# Install packages
pip install -r requirements.txt
# Run Django migrations
python manage.py migrate
# Create Django superuser (follow prompts)
python manage.py createsuperuser
# Run the dev server
python manage.py runserver
```

# <a name="cmd"></a>[CMD](#tab/cmd)

```CMD
:: Configure the Python virtual environment
py -3 -m venv venv
venv\scripts\activate

:: Install packages
pip install -r requirements.txt
:: Run Django migrations
python manage.py migrate
:: Create Django superuser (follow prompts)
python manage.py createsuperuser
:: Run the dev server
python manage.py runserver
```
---

После полной загрузки веб-приложения Django вы получите примерно такое сообщение:

<pre>
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
</pre>

В браузере перейдите по адресу *http:\//localhost:8000*. Должно отобразиться сообщение **No polls are available** (Доступных опросов нет). 

Перейдите по адресу *http:\//localhost:8000/admin* и войдите с именем администратора, который был создан на предыдущем шаге. Щелкните **Add** (Добавить) возле поля **Questions** (Вопросы) и создайте вопрос с несколькими вариантами ответа.

![Запуск приложения Python (Django) в службах приложений локально](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Снова перейдите по адресу *http:\//localhost:8000*, чтобы просмотреть вопрос и ответить на него. Локальный пример приложения Django записывает данные пользователя и сохраняет их в локальной базе данных Sqlite3, так что вы не испортите рабочую базу данных. Чтобы среда разработки максимально соответствовала среде Azure, постарайтесь настроить локальную базу данных Postgres.

Чтобы остановить сервер Django, нажмите клавиши CTRL+C.

### <a name="update-the-app"></a>Обновление приложения

Чтобы увидеть процесс обновления приложения, внесите небольшое изменение в файл `polls/models.py`. Найдите следующую строку:

<pre>
choice_text = models.CharField(max_length=200)
</pre>

Замените ее на следующую строку:

```python
choice_text = models.CharField(max_length=100)
```

После изменения модели данных вам нужно создать новую миграцию Django. Для этого выполните следующую команду:

```
python manage.py makemigrations
```

Вы можете проверить изменения локально. Для этого выполните миграцию, запустите сервер разработки и перейдите в браузере по адресу *http:\//localhost:8000/admin*:

```
python manage.py migrate
python manage.py runserver
```

### <a name="redeploy-code-to-azure"></a>Повторное развертывание кода в Azure

Чтобы развернуть изменения, выполните следующую команду в корне репозитория:

```azurecli
az webapp up --name <app-name>
```

Служба приложений обнаружит, что приложение уже существует и просто развернет в нем код.

### <a name="rerun-migrations-in-azure"></a>Повторное выполнение миграций в Azure

Поскольку вы внесли изменения в модель данных, все миграции базы данных нужно повторить в Службе приложений. Откройте в браузере сеанс SSH, перейдя по адресу *https://\<app-name>.scm.azurewebsites.net/webssh/host*. Выполните следующие команды:

```
cd site/wwwroot

# Activate default virtual environment in App Service container
source /antenv/bin/activate
# Run database migrations
python manage.py migrate
```

### <a name="review-app-in-production"></a>Проверка приложения в рабочей среде

Перейдите по адресу *http:\//\<app-name>.azurewebsites.net* и убедитесь, что изменения применены в реальном рабочем приложении. 

## <a name="stream-diagnostic-logs"></a>Потоковая передача журналов диагностики

Можно получить доступ к журналам консоли, которые были созданы в контейнере.

> [!TIP]
> Команда `az webapp up` позволяет включить ведение журналов по умолчанию. Чтобы не ухудшать производительность, через некоторое время ведение журнала отключается, но включается повторно при каждом запуске `az webapp up`. Чтобы отключить его вручную, выполните следующую команду:
>
> ```azurecli
> az webapp log config --name <app-name> --resource-group <app-resource-group> --docker-container-logging filesystem
> ```

Выполните следующую команду Azure CLI, чтобы увидеть поток журнала:

```azurecli
az webapp log tail --name <app-name> --resource-group <app-resource-group>
```

Если журналы консоли не отображаются, проверьте еще раз через 30 секунд.

> [!NOTE]
> Вы также можете проверить файлы журнала в браузере на странице `https://<app-name>.scm.azurewebsites.net/api/logs/docker`.

Чтобы остановить потоковую передачу журналов, нажмите клавиши `Ctrl`+`C`.

## <a name="manage-your-app-in-the-azure-portal"></a>Управление приложением с помощью портала Azure

На [портале Azure](https://portal.azure.com) найдите и выберите созданное приложение.

![Переход к приложению Python (Django) на портале Azure](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

По умолчанию на портале отображается страница **Обзор** приложения. Здесь вы можете наблюдать за работой приложения. Вы можете выполнять здесь базовые задачи управления, например просмотр, остановку, перезагрузку и удаление. На вкладках в левой части страницы отображаются различные страницы конфигурации, которые можно открыть.

![Управление приложением Python (Django) на странице "Обзор" на портале Azure](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Если эти ресурсы вам не понадобятся в будущем, удалите группу ресурсов, выполнив следующие команды:

```azurecli
az group delete --name myResourceGroup
az group delete --name <app-resource-group>
```

## <a name="next-steps"></a>Дальнейшие действия

Из этого руководства вы узнали следующее:

> [!div class="checklist"]
> * создание базы данных в службе Базы данных Azure для PostgreSQL;
> * развертывание кода в Службе приложений Azure и подключение к Postgres;
> * обновление кода и повторное развертывание;
> * просмотр журналов диагностики;
> * управление веб-приложением на портале Azure.

Перейдите к следующему руководству, в котором описано, как сопоставлять пользовательские DNS-имена с приложением.

> [!div class="nextstepaction"]
> [Руководство. Сопоставление настраиваемого DNS-имени с приложением](../app-service-web-tutorial-custom-domain.md)

Также ознакомьтесь с другими ресурсами:

> [!div class="nextstepaction"]
> [Настройка приложения Python](how-to-configure-python.md)
