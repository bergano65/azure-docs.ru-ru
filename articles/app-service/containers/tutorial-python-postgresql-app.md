---
title: Руководство по использованию приложения Python в Linux с PostgreSQL
description: Узнайте, как создать приложение Python в Linux, работающее в Службе приложений Azure, с подключением к базе данных PostgreSQL в Azure. В этом учебнике используется Django.
ms.devlang: python
ms.topic: tutorial
ms.date: 03/27/2019
ms.custom:
- mvc
- seodec18
- seo-python-october2019
ms.openlocfilehash: d23097c9674d2b7e60e779304a2d08c734bd614d
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688879"
---
# <a name="tutorial-build-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Руководство по Создание веб-приложения Python (Django) с помощью PostgreSQL в Службе приложений Azure

[Служба приложений на платформе Linux](app-service-linux-intro.md) — это служба веб-размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости. Это руководство демонстрирует создание управляемого данными веб-приложения Python (Django), используя в качестве серверной части базу данных PostgreSQL. После выполнения всех действий у вас будет приложение Django, работающее в Azure в Linux.

![Запуск веб-приложения Python Django в Службе приложений Azure в Linux](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Создание базы данных PostgreSQL в Azure
> * подключение веб-приложения Python к PostgreSQL;
> * развертывание веб-приложения Python в Azure;
> * просмотр журналов диагностики;
> * управление веб-приложением Python на портале Azure.

> [!NOTE]
> Прежде чем создавать базу данных Azure для PostgreSQL, проверьте [поколения вычислительных ресурсов, доступные в вашем регионе](https://docs.microsoft.com/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores).

Вы можете выполнить эти инструкции в macOS, Linux и Windows, так как они по большей части совпадают, но существующие различия не описаны в этом руководстве.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством:

1. [установите Git](https://git-scm.com/);
2. [установите Python](https://www.python.org/downloads/).
3. [установите и запустите PostgreSQL](https://www.postgresql.org/download/);

## <a name="test-local-postgresql-installation-and-create-a-database"></a>Проверка локальной установки PostgreSQL и создание базы данных

В окне локального терминала выполните команду `psql` для подключения к локальному серверу PostgreSQL.

```bash
sudo -u postgres psql postgres
```

Если вы видите сообщение об ошибке вида `unknown user: postgres`, возможно, для установленного экземпляра PostgreSQL настроено ваше имя пользователя, использованное для входа. Введите приведенную ниже команду.

```bash
psql postgres
```

Если подключение успешно установлено, это означает, что база данных PostgreSQL запущена. В противном случае запустите локальную базу данных PostgreSQL, выполнив инструкции по [скачиванию ядра PostgreSQL](https://www.postgresql.org/download/) для используемой операционной системы.

Создайте базу данных *pollsdb* и настройте отдельного пользователя базы данных *manager* с паролем *supersecretpass*.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Введите `\q`, чтобы выйти из клиента PostgreSQL.

<a name="step2"></a>

## <a name="create-local-python-app"></a>Создание локального приложения Python

На этом шаге вы настроите локальный проект Python Django.

### <a name="clone-the-sample-app"></a>Клонирования примера приложения

Откройте окно терминала и c помощью команды `CD` перейдите в рабочий каталог.

Затем выполните следующие команды, чтобы клонировать репозиторий с примером.

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

Этот пример репозитория содержит приложение [Django](https://www.djangoproject.com/). Точно такое управляемое данными приложение можно получить, следуя руководству по началу работы [в документации по Django](https://docs.djangoproject.com/en/2.1/intro/tutorial01/). Это руководство не обучает вас работе с Django, но в нем показано, как развертывать и запускать веб-приложение Django (или другое управляемое данными приложение Python) в Службе приложений Azure.

### <a name="configure-environment"></a>Настройка среды

Создайте виртуальную среду Python и используйте сценарий для параметров подключения к базе данных.

```bash
# Bash
python3 -m venv venv
source venv/bin/activate
source ./env.sh

# PowerShell
py -3 -m venv venv
venv\scripts\activate
.\env.ps1
```

Определенные в *env.sh* и *env.ps1* переменные среды используются в _azuresite/settings.py_ для определения параметров базы данных.

### <a name="run-app-locally"></a>Локальный запуск приложения

Установите необходимые пакеты, чтобы [выполнить миграцию Django](https://docs.djangoproject.com/en/2.1/topics/migrations/) и [создать администратора](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user).

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

После создания администратора запустите сервер Django.

```bash
python manage.py runserver
```

После полной загрузки веб-приложения Django вы увидите следующее сообщение:

```bash
Performing system checks...

System check identified no issues (0 silenced).
October 26, 2018 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

Перейдите к `http://localhost:8000` в браузере. Отобразится сообщение `No polls are available.`. 

Перейдите к `http://localhost:8000/admin` и войдите с именем администратора, который был создан на предыдущем шаге. Щелкните **Добавить** возле поля **Вопросы** и создайте вопрос опроса с несколькими вариантами.

![Запуск приложения Python (Django) в службах приложений локально](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Перейдите к `http://localhost:8000`, и отобразится вопрос опроса.

Пример приложения Django хранит данные пользователя в базе данных. Если добавление вопроса опроса прошло успешно, значит ваше приложение записывает данные в локальную базу данных PostgreSQL.

Чтобы остановить сервер Django в любое время, введите Ctrl+C в окне терминала.

## <a name="create-a-production-postgresql-database"></a>Создание рабочей базы данных PostgreSQL

На этом шаге вы создадите базу данных PostgreSQL в Azure. При развертывании приложения в Azure используется эта облачная база данных.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

### <a name="create-a-resource-group"></a>Создание группы ресурсов

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Создание сервера Базы данных Azure для PostgreSQL

Создайте сервер PostgreSQL с помощью команды [`az postgres server create`](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) в Cloud Shell.

В следующем примере команды замените *\<postgresql-name>* уникальным именем сервера, а *\<admin-username>* и *\<admin-password>* замените учетными данными нужного пользователя. Указываются данные учетной записи администратора базы данных. Это имя используется как часть конечной точки PostgreSQL (`https://<postgresql-name>.postgres.database.azure.com`), поэтому оно должно быть уникальным на всех серверах в Azure.

```azurecli-interactive
az postgres server create --resource-group myResourceGroup --name <postgresql-name> --location "West Europe" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen4_1
```

После создания сервера базы данных Azure для PostgreSQL в Azure CLI отображаются следующие сведения.

```json
{
  "administratorLogin": "<admin-username>",
  "fullyQualifiedDomainName": "<postgresql-name>.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.DBforPostgreSQL/servers/<postgresql-name>",
  "location": "westus",
  "name": "<postgresql-name>",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "capacity": 1,
    "family": "Gen4",
    "name": "B_Gen4_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

> [!NOTE]
> Запомните значения \<admin-username> и \<admin-password> для использования в будущем. Они понадобятся для входа на сервер Postgre и его базы данных.

### <a name="create-firewall-rules-for-the-postgresql-server"></a>Создание правил брандмауэра для сервера PostgreSQL

Чтобы разрешить доступ к базе данных из ресурсов Azure, в Cloud Shell выполните следующие команды Azure CLI.

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Этот параметр разрешает сетевые подключения со всех IP-адресов в сети Azure. Для рабочей среды попробуйте настроить как можно более строгие правила брандмауэра, [указав только исходящие IP-адреса, используемые вашим приложением](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

В Cloud Shell повторно выполните соответствующую команду, чтобы разрешить доступ с локального компьютера, заменив *\<your-ip-address >* [локальным IPv4-адресом](https://www.whatsmyip.org/).

```azurecli-interactive
az postgres server firewall-rule create --resource-group myResourceGroup --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

## <a name="connect-python-app-to-production-database"></a>Подключение приложения Python к рабочей базе данных

На этом шаге веб-приложение Django подключается к серверу созданной базы данных Azure для PostgreSQL.

### <a name="create-empty-database-and-user-access"></a>Создание пустой базы данных и доступа пользователя

В Cloud Shell подключитесь к базе данных, выполнив команду, приведенную ниже. При появлении запроса введите пароль администратора. Используйте тот же пароль, который был указан в разделе [Создание сервера базы данных Azure для PostgreSQL](#create-an-azure-database-for-postgresql-server).

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Точно так же, как и на локальном сервере Postgres, создайте базу данных и пользователя на сервере Azure Postgres.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Введите `\q`, чтобы выйти из клиента PostgreSQL.

> [!NOTE]
> Рекомендуется создать пользователей базы данных с разрешениями только для конкретных приложений, а не с правами администратора. В этом примере пользователь `manager` имеет все привилегии _только_ для базы данных `pollsdb`.

### <a name="test-app-connectivity-to-production-database"></a>Тестирование подключения приложения к рабочей базе данных

В локальном окне терминала измените переменные среды базы данных (которые вы настроили ранее, выполнив *env.sh* или *env.ps1*).

```bash
# Bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"

# PowerShell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

Выполните миграцию Django в базу данных Azure и создайте администратора.

```bash
python manage.py migrate
python manage.py createsuperuser
```

После создания администратора запустите сервер Django.

```bash
python manage.py runserver
```

Выполните вход в `http://localhost:8000` повторно. Отобразится сообщение `No polls are available.`. 

Перейдите к `http://localhost:8000/admin`, и выполните вход с данными созданного администратора, и создайте вопрос опроса, как это делали ранее.

![Запуск приложения Python (Django) в службах приложений локально](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Перейдите к `http://localhost:8000`, и отобразится вопрос опроса. Теперь приложение записывает данные в базу данных на портале Azure.

## <a name="deploy-to-azure"></a>Развертывание в Azure

На этом шаге вы развернете приложение Python, подключенное к базе данных Postgres, в Службе приложений Azure.

### <a name="configure-repository"></a>Настройка репозитория

Во входящих запросах Django проверяет заголовок `HTTP_HOST`. Чтобы веб-приложение Django выполнялось в Службе приложений Azure, необходимо добавить полное доменное имя приложения для разрешенных узлов. Откройте _azuresite/settings.py_ и найдите параметр `ALLOWED_HOSTS`. Измените строку следующим образом:

```python
ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
```

Далее, Django не поддерживает [обработку статических файлов в рабочей среде](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/), поэтому необходимо включить эту функцию вручную. В этом учебнике используется [WhiteNoise](https://whitenoise.evans.io/en/stable/). Пакет WhiteNoise уже входит в _requirements.txt_. Чтобы использовать Django, его необходимо настроить. 

В _azuresite/settings.py_ найдите параметр `MIDDLEWARE` и добавьте ПО промежуточного слоя `whitenoise.middleware.WhiteNoiseMiddleware` к списку прямо под ПО промежуточного слоя `django.middleware.security.SecurityMiddleware`. Параметр `MIDDLEWARE` должен выглядеть следующим образом:

```python
MIDDLEWARE = [
    'django.middleware.security.SecurityMiddleware',
    'whitenoise.middleware.WhiteNoiseMiddleware',
    ...
]
```

В конце _azuresite/settings.py_ добавьте следующие строки.

```python
STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'

STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
```

Дополнительные сведения о настройке WhiteNoise см. [здесь](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> Раздел параметров базы данных уже соответствует рекомендациям по безопасности использования переменных среды. Рекомендации по полному развертыванию см. в документации о Django [здесь](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/).

Зафиксируйте изменения в репозитории.

```bash
git commit -am "configure for App Service"
```

### <a name="configure-deployment-user"></a>Настройка пользователя развертывания

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>Создание плана службы приложений

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Создание веб-приложения

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Настройка переменных среды

Ранее в этом руководстве вы определили переменные среды для подключения к базе данных PostgreSQL.

В службе приложений переменные среды задаются в качестве _параметров приложения_ с помощью команды [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) в Cloud Shell.

Код ниже указывает сведения о подключении к базе данных как параметры приложения. 

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

Дополнительные сведения о доступе к переменным среды в Службе приложений см. в разделе [Доступ к переменным среды](how-to-configure-python.md#access-environment-variables).

### <a name="push-to-azure-from-git"></a>Публикация в Azure из Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 7, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (7/7), done.
Writing objects: 100% (7/7), 775 bytes | 0 bytes/s, done.
Total 7 (delta 4), reused 0 (delta 0)
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '6520eeafcc'.
remote: Generating deployment script.
remote: Running deployment command...
remote: Python deployment.
remote: Kudu sync from: '/home/site/repository' to: '/home/site/wwwroot'
. 
. 
. 
remote: Deployment successful.
remote: App container will begin restart within 10 seconds.
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
   06b6df4..6520eea  master -> master
```  

Сервер развертывания Службы приложений в корневой папке репозитория видит _requirements.txt_ и, выполнив `git push`, запускает автоматическое управление пакетами Python.

### <a name="browse-to-the-azure-app"></a>Переход к приложению Azure

Перейдите в развернутое приложение. Запуск занимает некоторое время, так как при первом запросе приложения контейнер должен быть скачан и запущен. Если истекает время ожидания страницы или отображается сообщение об ошибке, подождите несколько минут и обновите страницу.

```bash
http://<app-name>.azurewebsites.net
```

Вы должны увидеть созданный ранее вопрос опроса. 

В репозитории Служба приложений Azure обнаруживает проект Django во время поиска _wsgi.py_ в каждом подкаталоге, который создается по умолчанию с помощью `manage.py startproject`. С помощью найденного файла загружается веб-приложение Django. Дополнительные сведения о том, как загружаются приложения Python с помощью Службы приложений Azure, см. [здесь](how-to-configure-python.md).

Перейдите к `<app-name>.azurewebsites.net` и войдите с тем же именем созданного администратора. При желании попробуйте создать некоторые дополнительные вопросы опроса.

![Запуск приложения Python (Django) в службах приложений в Azure](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

**Поздравляем!** Вы запустили веб-приложение Python (Django) в Службе приложений Azure для Linux.

## <a name="stream-diagnostic-logs"></a>Потоковая передача журналов диагностики

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Управление приложением с помощью портала Azure

Перейдите на [портал Azure](https://portal.azure.com), чтобы увидеть созданное приложение.

В меню слева выберите **Службы приложений** и щелкните имя своего приложения Azure.

![Переход к приложению Python (Django) на портале Azure](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

По умолчанию на портале отображается страница **Обзор** приложения. Здесь вы можете наблюдать за работой приложения. Вы также можете выполнять базовые задачи управления: обзор, завершение, запуск, перезагрузку и удаление. На вкладках в левой части страницы отображаются различные страницы конфигурации, которые можно открыть.

![Управление приложением Python (Django) на странице "Обзор" на портале Azure](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как выполнить следующие задачи:

> [!div class="checklist"]
> * Создание базы данных PostgreSQL в Azure
> * подключение веб-приложения Python к PostgreSQL;
> * развертывание веб-приложения Python в Azure;
> * просмотр журналов диагностики;
> * управление веб-приложением Python на портале Azure.

Перейдите к следующему руководству, чтобы научиться сопоставлять пользовательские DNS-имена с приложением.

> [!div class="nextstepaction"]
> [Руководство. Сопоставление настраиваемого DNS-имени с приложением](../app-service-web-tutorial-custom-domain.md)

Также ознакомьтесь с другими ресурсами:

> [!div class="nextstepaction"]
> [Настройка приложения Python](how-to-configure-python.md)
