---
title: Руководство по использованию приложения Python в Linux с PostgreSQL
description: Узнайте, как создать приложение Python в Linux, работающее в Службе приложений Azure, с подключением к базе данных PostgreSQL в Azure. В этом учебнике используется Django.
ms.devlang: python
ms.topic: tutorial
ms.date: 01/23/2020
ms.custom:
- mvc
- seodec18
- seo-python-october2019
- cli-validate
ms.openlocfilehash: 13431b62e64774a4c31cf95200def3ba77f973d7
ms.sourcegitcommit: 3c8fbce6989174b6c3cdbb6fea38974b46197ebe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2020
ms.locfileid: "77523943"
---
# <a name="tutorial-run-a-python-django-web-app-with-postgresql-in-azure-app-service"></a>Руководство по Запуск веб-приложения Python (Django) с PostgreSQL в Службе приложений Azure

[Служба приложений Azure](app-service-linux-intro.md) — это служба веб-размещения с самостоятельной установкой исправлений и высоким уровнем масштабируемости. Узнайте, как подключить управляемое данными веб-приложение Python (Django) к Базе данных Azure для PostgreSQL, а также как развернуть и запустить приложение в Службе приложений Azure.

![Веб-приложение Python Django в Службе приложений Azure](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

В этом руководстве описано следующее:

> [!div class="checklist"]
> * создание Базы данных Azure для PostgreSQL и подключение к ней веб-приложения;
> * развертывание веб-приложения в Службе приложений Azure;
> * просмотр журналов диагностики;
> * управление веб-приложением на портале Azure.

Инструкции из этой статьи вы можете выполнять в операционных системах macOS, Linux или Windows. Большинство действий выполняются одинаково, но есть небольшие отличия, которые не описаны в этом руководстве. В большинстве приведенных ниже примеров используется окно терминала `bash` в ОС Linux. 

## <a name="prerequisites"></a>Предварительные требования

Перед началом работы с этим руководством сделайте следующее:

- [!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]
- Установите [Git](https://git-scm.com/).
- Установите [Python 3](https://www.python.org/downloads/).
- Установите и запустите [PostgreSQL](https://www.postgresql.org/download/).

## <a name="test-postgresql-installation-and-create-a-database"></a>Проверка установки PostgreSQL и создание базы данных

Подключитесь к локальному серверу PostgreSQL и создайте базу данных. 

В окне локального терминала выполните команду `psql`, чтобы подключиться к локальному серверу PostgreSQL с использованием встроенной учетной записи пользователя `postgres`.

```bash
sudo su - postgres
psql
```
или диспетчер конфигурации служб
```PowerShell
psql -U postgres
```

Если подключение успешно установлено, это означает, что база данных PostgreSQL запущена. В противном случае запустите локальную базу данных PostgreSQL, выполнив инструкции по [скачиванию ядра PostgreSQL](https://www.postgresql.org/download/) для используемой операционной системы.

Создайте базу данных с именем *pollsdb* и настройте пользователя базы данных с именем *manager* и паролем *supersecretpass*.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

Введите `\q`, чтобы выйти из клиента PostgreSQL.

<a name="step2"></a>
## <a name="create-and-run-the-local-python-app"></a>Создание и запуск локального приложения Python

Теперь создайте и запустите пример веб-приложения Python (Django).

Пример репозитория [djangoapp](https://github.com/Azure-Samples/djangoapp) содержит управляемое данными приложение [Django](https://www.djangoproject.com/) для опросов, которое вы можете получить, выполнив инструкции по [созданию первого приложения](https://docs.djangoproject.com/en/2.1/intro/tutorial01/) из документации по Django.

### <a name="clone-the-sample-app"></a>Клонирования примера приложения

В окне терминала выполните следующую команду, чтобы клонировать репозиторий с примером приложения и перейти в его рабочий каталог.

```bash
git clone https://github.com/Azure-Samples/djangoapp.git
cd djangoapp
```

### <a name="configure-the-python-virtual-environment"></a>Настройка виртуальной среды Python

Создайте и активируйте виртуальную среду Python для выполнения приложения.

```bash
python3 -m venv venv
source venv/bin/activate
```
или диспетчер конфигурации служб
```PowerShell
py -3 -m venv venv
venv\scripts\activate
```

В среде `venv` запустите файл *env.sh* или *env.ps1*, чтобы задать переменные среды для параметров подключения к базе данных, используемых в *azuresite/settings.py*.

```bash
source ./env.sh
```
или диспетчер конфигурации служб
```PowerShell
.\env.ps1
```

Установите необходимые пакеты, которые перечислены в файле *requirements.txt*, а затем выполните [миграции Django](https://docs.djangoproject.com/en/2.1/topics/migrations/) и [создайте администратора](https://docs.djangoproject.com/en/2.1/intro/tutorial02/#creating-an-admin-user).

```bash
pip install -r requirements.txt
python manage.py migrate
python manage.py createsuperuser
```

### <a name="run-the-web-app"></a>Запуск веб-приложения

Создав администратора, запустите сервер Django.

```bash
python manage.py runserver
```

После полной загрузки веб-приложения Django вы получите примерно такое сообщение:

```bash
Performing system checks...

System check identified no issues (0 silenced).
December 13, 2019 - 10:54:59
Django version 2.1.2, using settings 'azuresite.settings'
Starting development server at http://127.0.0.1:8000/
Quit the server with CONTROL-C.
```

В браузере перейдите по адресу *http:\//localhost:8000*. Должно отобразиться сообщение **No polls are available** (Доступных опросов нет). 

Перейдите по адресу *http:\//localhost:8000/admin* и войдите с именем администратора, который был создан на предыдущем шаге. Щелкните **Add** (Добавить) возле поля **Questions** (Вопросы) и создайте вопрос с несколькими вариантами ответа.

![Запуск приложения Python (Django) в службах приложений локально](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Снова перейдите по адресу *http:\//localhost:8000*, чтобы просмотреть вопрос и ответить на него. Пример локального приложения Django собирает введенные пользователем данные пользователя и сохраняет их в локальной базе данных PostgreSQL.

Вы можете в любой момент остановить сервер Django, нажав клавиши CTRL+C в окне терминала.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

На большинстве остальных шагов в этом руководстве используются команды Azure CLI, которые выполняются в Azure Cloud Shell. 

## <a name="create-and-connect-to-azure-database-for-postgresql"></a>Создание Базы данных Azure для PostgreSQL и подключение к ней

В этом разделе описано, как создать сервер Базы данных Azure для PostgreSQL и саму базу данных, а затем подключить к ней веб-приложение. Веб-приложение, которое вы будете развертывать в Службе приложений Azure, использует эту облачную базу данных. 

### <a name="create-a-resource-group"></a>Создание группы ресурсов

Для сервера Базы данных Azure для PostgreSQL можно создать новую группу ресурсов или использовать существующую. 

[!INCLUDE [Create resource group](../../../includes/app-service-web-create-resource-group-linux-no-h.md)]

### <a name="create-an-azure-database-for-postgresql-server"></a>Создание сервера Базы данных Azure для PostgreSQL

Для создания сервера PostgreSQL используется команда [az postgres server create](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-create) в оболочке Cloud Shell.

> [!NOTE]
> Прежде чем создавать сервер Базы данных Azure для PostgreSQL, проверьте [поколение вычислительных ресурсов](/azure/postgresql/concepts-pricing-tiers#compute-generations-and-vcores), доступное в вашем регионе. Если этот регион не поддерживает оборудование 4-го поколения (Gen4), измените значение *--sku-name* в следующей командной строке на другое, которое поддерживается в вашем регионе, например Gen5 (5-е поколение). 

В следующей команде замените *\<postgresql-name>* уникальным именем сервера. Это имя используется как часть конечной точки PostgreSQL (*https://\<postgresql-name>.postgres.database.azure.com*), поэтому оно должно быть уникальным на всех серверах в Azure. 

Замените *\<resourcegroup-name>* и *\<region>* именем используемой группы ресурсов и регионом, в котором она расположена. Для значений *\<admin-username>* и *\<admin-password>* создайте учетные данные пользователя с правами администратора базы данных. Запишите значения *\<admin-username>* и *\<admin-password>* . Они потребуются позже для входа на сервер PostgreSQL и в базы данных.

```azurecli-interactive
az postgres server create --resource-group <resourcegroup-name> --name <postgresql-name> --location "<region>" --admin-user <admin-username> --admin-password <admin-password> --sku-name B_Gen5_1
```

После создания сервера Базы данных Azure для PostgreSQL в Azure CLI отображается код JSON примерно такого содержания:

```json
{
  "administratorLogin": "myusername",
  "earliestRestoreDate": "2020-01-22T19:02:15.727000+00:00",
  "fullyQualifiedDomainName": "myservername.postgres.database.azure.com",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myresourcegroup/providers/Microsoft.DBforPostgreSQL/servers/myservername",
  "location": "westeurope",
  "masterServerId": "",
  "name": "myservername",
  "replicaCapacity": 5,
  "replicationRole": "None",
  "resourceGroup": "myresourcegroup",
  "sku": {
    "capacity": 1,
    "family": "Gen5",
    "name": "B_Gen5_1",
    "size": null,
    "tier": "Basic"
  },
  < JSON data removed for brevity. >
}
```

### <a name="create-firewall-rules-for-the-azure-database-for-postgresql-server"></a>Создание правил брандмауэра для сервера Базы данных Azure для PostgreSQL

Чтобы разрешить доступ к базе данных для ресурсов Azure, выполните команду [az postgres server firewall-rule create](/cli/azure/postgres/server/firewall-rule#az-postgres-server-firewall-rule-create). Замените заполнители *\<postgresql-name>* и *\<resourcegroup-name>* реальными значениями.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=0.0.0.0 --end-ip-address=0.0.0.0 --name AllowAllAzureIPs
```

> [!NOTE]
> Приведенный выше параметр разрешает устанавливать сетевые подключения со всех IP-адресов в сети Azure. Для рабочей среды попробуйте настроить как можно более строгие правила брандмауэра, [разрешив только исходящие IP-адреса, используемые вашим приложением](../overview-inbound-outbound-ips.md?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json#find-outbound-ips).

Выполните команду `firewall-rule create` еще раз, чтобы разрешить доступ с локального компьютера. Замените *\<your-ip-address>* значением [локального IP-адреса IPv4](https://www.whatsmyip.org/). Замените заполнители *\<postgresql-name>* и *\<resourcegroup-name>* реальными значениями.

```azurecli-interactive
az postgres server firewall-rule create --resource-group <resourcegroup-name> --server-name <postgresql-name> --start-ip-address=<your-ip-address> --end-ip-address=<your-ip-address> --name AllowLocalClient
```

### <a name="create-and-connect-to-the-azure-database-for-postgresql-database"></a>Создание базы данных в Базе данных Azure для PostgreSQL и подключение к ней

Подключитесь к серверу Базы данных Azure для PostgreSQL, выполнив следующую команду. Используйте фактические значения *\<postgresql-name>* и *\<admin-username>* , а затем выполните вход с созданным ранее паролем.

```bash
psql -h <postgresql-name>.postgres.database.azure.com -U <admin-username>@<postgresql-name> postgres
```

Точно так же, как и на локальном сервере PostgreSQL, создайте базу данных и пользователя на сервере Базы данных Azure для PostgreSQL.

```sql
CREATE DATABASE pollsdb;
CREATE USER manager WITH PASSWORD 'supersecretpass';
GRANT ALL PRIVILEGES ON DATABASE pollsdb TO manager;
```

> [!NOTE]
> Мы рекомендуем создать пользователей базы данных с разрешениями только для конкретных приложений, а не использовать пользователя с правами администратора. В этом примере пользователь `manager` имеет все права *только* для базы данных `pollsdb`.

Введите `\q`, чтобы выйти из клиента PostgreSQL.

### <a name="test-app-connectivity-to-the-azure-postgresql-database"></a>Тестирование подключения приложения к Базе данных Azure PostgreSQL

Измените локальный файл *env.sh* или *env.ps1*, чтобы указать в нем облачную базу данных PostgreSQL, заменив *\<postgresql-name>* реальным именем сервера Базы данных Azure для PostgreSQL.

```bash
export DBHOST="<postgresql-name>.postgres.database.azure.com"
export DBUSER="manager@<postgresql-name>"
export DBNAME="pollsdb"
export DBPASS="supersecretpass"
```
или диспетчер конфигурации служб
```powershell
$Env:DBHOST = "<postgresql-name>.postgres.database.azure.com"
$Env:DBUSER = "manager@<postgresql-name>"
$Env:DBNAME = "pollsdb"
$Env:DBPASS = "supersecretpass"
```

В среде `venv` в локальном окне терминала запустите измененный файл *env.sh* или *env.ps1*. 
```bash
source ./env.sh
```
или диспетчер конфигурации служб
```PowerShell
.\env.ps1
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

Перейдите в браузере по адресу *http:\//localhost:8000*. Снова должно снова отобразиться сообщение **No polls are available** (Доступных опросов нет). 

Перейдите по адресу *http:\//localhost:8000/admin* и выполните вход с данными созданного администратора, а затем снова создайте вопрос.

![Запуск приложения Python (Django) в службах приложений локально](./media/tutorial-python-postgresql-app/run-python-django-app-locally.png)

Снова перейдите по адресу *http:\//localhost:8000*. Отобразится вопрос. Итак, ваше приложение сохраняет данные в базу данных в Базе данных Azure для PostgreSQL.

Вы можете в любой момент остановить сервер Django, нажав клавиши CTRL+C в окне терминала.

## <a name="deploy-the-web-app-to-azure-app-service"></a>Развертывание веб-приложения в Службе приложений Azure

На этом шаге вы развернете в Службе приложений Azure приложение Python, подключенное к базе данных в Базе данных Azure для PostgreSQL.

### <a name="configure-repository"></a>Настройка репозитория

Так как в этом руководстве используется пример Django, необходимо изменить и добавить некоторые параметры в файле *djangoapp/azuresite/settings.py* для работы со Службой приложений Azure. 

1. Во входящих запросах Django проверяет заголовок `HTTP_HOST`. Чтобы веб-приложение Django выполнялось в Службе приложений, необходимо добавить полное доменное имя приложения в список разрешенных узлов. 
   
   В файле *azuresite/settings.py* измените строку `ALLOWED_HOSTS` следующим образом:
   
   ```python
   ALLOWED_HOSTS = [os.environ['WEBSITE_SITE_NAME'] + '.azurewebsites.net', '127.0.0.1'] if 'WEBSITE_SITE_NAME' in os.environ else []
   ```
   
1. Django не поддерживает [обработку статических файлов в рабочей среде](https://docs.djangoproject.com/en/2.1/howto/static-files/deployment/). В этом руководстве мы используем для обработки таких файлов [WhiteNoise](https://whitenoise.evans.io/en/stable/). Пакет WhiteNoise уже установлен вместе из файла *requirements.txt*. 
   
   Чтобы настроить использование WhiteNoise в Django, найдите в файле *azuresite/settings.py* параметр `MIDDLEWARE` и добавьте в список `whitenoise.middleware.WhiteNoiseMiddleware` сразу после строки `django.middleware.security.SecurityMiddleware`. Параметр `MIDDLEWARE` должен выглядеть следующим образом:
   
   ```python
   MIDDLEWARE = [
       'django.middleware.security.SecurityMiddleware',
       'whitenoise.middleware.WhiteNoiseMiddleware',
       ...
   ]
   ```
   
1. В конец файла *azuresite/settings.py* добавьте следующие строки.
   
   ```python
   STATICFILES_STORAGE = 'whitenoise.storage.CompressedManifestStaticFilesStorage'
   STATIC_ROOT = os.path.join(BASE_DIR, 'staticfiles')
   ```
   
   См. сведения о [настройке WhiteNoise](https://whitenoise.evans.io/en/stable/).

> [!IMPORTANT]
> Раздел параметров базы данных уже соответствует рекомендациям по безопасности использования переменных среды. См. [полные рекомендации по развертыванию](https://docs.djangoproject.com/en/2.1/howto/deployment/checklist/) в документации по Django.

Зафиксируйте изменения в локальной вилке репозитория *djangoapp*:

```bash
git commit -am "configure for App Service"
```

### <a name="configure-a-deployment-user"></a>Настойка пользователя развертывания

[!INCLUDE [Configure deployment user](../../../includes/configure-deployment-user-no-h.md)]

### <a name="create-app-service-plan"></a>Создание плана службы приложений

[!INCLUDE [Create app service plan](../../../includes/app-service-web-create-app-service-plan-linux-no-h.md)]

### <a name="create-a-web-app"></a>Создание веб-приложения

[!INCLUDE [Create web app](../../../includes/app-service-web-create-web-app-python-linux-no-h.md)]

### <a name="configure-environment-variables"></a>Настройка переменных среды

Ранее в этом руководстве вы определили переменные среды для подключения к базе данных PostgreSQL.

В Службе приложений Azure переменные среды устанавливаются как *параметры приложения* с помощью команды [az webapp config appsettings set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set).

Выполните в Azure Cloud Shell следующую команду, чтобы указать сведения о подключении к базе данных в качестве параметров приложения. Замените заполнители *\<app-name>* , *\<resourcegroup-name>* и *\<postgresql-name>* реальными значениями.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resourcegroup-name> --settings DBHOST="<postgresql-name>.postgres.database.azure.com" DBUSER="manager@<postgresql-name>" DBPASS="supersecretpass" DBNAME="pollsdb"
```

См. сведения о том, как [обращаться к переменным среды из кода приложения](how-to-configure-python.md#access-environment-variables).

### <a name="push-to-azure-from-git"></a>Публикация в Azure из Git

[!INCLUDE [app-service-plan-no-h](../../../includes/app-service-web-git-push-to-azure-no-h.md)]

```bash 
Counting objects: 60, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (51/51), done.
Writing objects: 100% (60/60), 15.37 KiB | 749.00 KiB/s, done.
Total 60 (delta 9), reused 0 (delta 0)
remote: Deploy Async
remote: Updating branch 'master'.
remote: Updating submodules.
remote: Preparing deployment for commit id '06f3f7c0cb'.
remote: Repository path is /home/site/repository
remote: Running oryx build...
remote: Build orchestrated by Microsoft Oryx, https://github.com/Microsoft/Oryx
remote: You can report issues at https://github.com/Microsoft/Oryx/issues
. 
. 
. 
remote: Done in 100 sec(s).
remote: Running post deployment command(s)...
remote: Triggering recycle (preview mode disabled).
remote: Deployment successful.
remote: Deployment Logs : 'https://<app-name>.scm.azurewebsites.net/newui/jsonviewer?view_url=/api/deployments/06f3f7c0cb52ce3b4aff85c2b5099fbacb65ab94/log'
To https://<app-name>.scm.azurewebsites.net/<app-name>.git 
 * [new branch]      master -> master
```  

Сервер развертывания Службы приложений в корневой папке репозитория видит *requirements.txt* и, выполнив `git push`, запускает автоматическое управление пакетами Python.

### <a name="browse-to-the-azure-app"></a>Переход к приложению Azure

Перейдите к развернутому приложению по URL-адресу *http:\//\<app-name>.azurewebsites.net*. На его запуск потребуется некоторое время, так как при первом обращении к приложению нужно скачать и запустить контейнер. Если истекает время ожидания страницы или отображается сообщение об ошибке, подождите несколько минут и обновите страницу.

Вы должны увидеть созданные ранее вопросы. 

В репозитории Служба приложений определяет проекты Django по файлу *wsgi.py* в каждом подкаталоге, который создается в `manage.py startproject` по умолчанию. Обнаружив такой файл, Служба приложений загружает веб-приложение Django. Дополнительные сведения о том, как загружаются приложения Python с помощью Службы приложений Azure, см. [здесь](how-to-configure-python.md).

Откройте адрес *http:\//\<app-name>.azurewebsites.net/admin* и войдите с именем администратора, который был создан ранее. При желании создайте еще несколько вопросов.

![Запуск приложения Python (Django) в службах приложений в Azure](./media/tutorial-python-postgresql-app/run-python-django-app-in-azure.png)

**Поздравляем!** Вы запустили веб-приложение Python (Django) в Службе приложений Azure для Linux.

## <a name="stream-diagnostic-logs"></a>Потоковая передача журналов диагностики

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="manage-your-app-in-the-azure-portal"></a>Управление приложением с помощью портала Azure

На [портале Azure](https://portal.azure.com) найдите и выберите созданное приложение.

![Переход к приложению Python (Django) на портале Azure](./media/tutorial-python-postgresql-app/navigate-to-django-app-in-app-services-in-the-azure-portal.png)

По умолчанию на портале отображается страница **Обзор** приложения. Здесь вы можете наблюдать за работой приложения. Вы можете выполнять здесь базовые задачи управления, например просмотр, остановку, перезагрузку и удаление. На вкладках в левой части страницы отображаются различные страницы конфигурации, которые можно открыть.

![Управление приложением Python (Django) на странице "Обзор" на портале Azure](./media/tutorial-python-postgresql-app/manage-django-app-in-app-services-in-the-azure-portal.png)

[!INCLUDE [cli-samples-clean-up](../../../includes/cli-samples-clean-up.md)]

## <a name="next-steps"></a>Дальнейшие действия

Перейдите к следующему руководству, в котором описано, как сопоставлять пользовательские DNS-имена с приложением.

> [!div class="nextstepaction"]
> [Руководство. Сопоставление настраиваемого DNS-имени с приложением](../app-service-web-tutorial-custom-domain.md)

Также ознакомьтесь с другими ресурсами:

> [!div class="nextstepaction"]
> [Настройка приложения Python](how-to-configure-python.md)
