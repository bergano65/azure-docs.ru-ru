---
title: Создание веб-приложения Python Flask с использованием API Azure Cosmos DB для MongoDB
description: В этой статье представлен пример кода Python Flask, который можно использовать для подключения и выполнения запросов c помощью API Azure Cosmos DB для MongoDB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.custom: devx-track-python
ms.openlocfilehash: 58f22a335f4c619a6348e9e127e60f5a79f658b2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95994570"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Краткое руководство. Создание приложения Python с использованием API Azure Cosmos DB для MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-go.md)
>  

В этом кратком руководстве вы используете Azure Cosmos DB для учетной записи API Mongo DB или эмулятора Azure Cosmos DB, чтобы выполнить клонирование веб-приложения Flask To-Do Python из GitHub. Azure Cosmos DB — это служба многомодельной базы данных, позволяющая быстро создавать и запрашивать документы, таблицы, пары "ключ-значение" и графовые базы данных, используя возможности глобального распределения и горизонтального масштабирования.

## <a name="prerequisites"></a>Предварительные требования

- Учетная запись Azure с активной подпиской. [Создайте бесплатно](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio). Или [воспользуйтесь пробной версией Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) без подписки Azure. Вы также можете применить [эмулятор Azure Cosmos DB](local-emulator.md). 
- [Python 3.6+](https://www.python.org/downloads/)
- [Visual Studio Code](https://code.visualstudio.com/Download) с [расширением Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python).

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь необходимо клонировать приложение Flask-MongoDB с GitHub. Задайте строку подключения и выполните ее. Вы узнаете, как можно упростить работу с данными программным способом.

1. Откройте командную строку, создайте папку git-samples, а затем закройте окно командной строки.

    ```bash
    md "C:\git-samples"
    ```

2. Откройте окно терминала git, например git bash, и выполните команду `cd`, чтобы перейти в новую папку для установки примера приложения.

    ```bash
    cd "C:\git-samples"
    ```

3. Выполните команду ниже, чтобы клонировать репозиторий с примером. Эта команда создает копию примера приложения на локальном компьютере.

    ```bash
    git clone https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample.git
    ```
3. Для установки модулей python выполните следующую команду.

    ```bash 
    pip install -r .\requirements.txt
    ```
4. Откройте папку в Visual Studio Code.

## <a name="review-the-code"></a>Просмотр кода

Это необязательный шаг. Если вы хотите узнать, как создать в коде ресурсы базы данных, изучите приведенные ниже фрагменты кода. Или вы можете сразу перейти к [запуску приложения](#run-the-web-app). 

Следующие фрагменты кода взяты из файла *app.py*. В них используется строка подключения для локального эмулятора Azure Cosmos DB. Как показано ниже, пароль необходимо разделить, чтобы адаптироваться к косым чертам, которые невозможно проанализировать иным образом.

* Запустите клиент MongoDB, извлеките базу данных и пройдите проверку подлинности.

    ```python
    client = MongoClient("mongodb://127.0.0.1:10250/?ssl=true") #host uri
    db = client.test    #Select the database
    db.authenticate(name="localhost",password='C2y6yDjf5' + r'/R' + '+ob0N8A7Cgv30VRDJIWEHLM+4QDU5DE2nQ9nDuVTqobD4b8mGGyPMbIZnqyMsEcaGQy67XIw' + r'/Jw==')
    ```

* Получите коллекцию или создайте ее, если ее еще не существует.

    ```python
    todos = db.todo #Select the collection
    ```

* Создайте приложение

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>Запуск веб-приложения

1. Убедитесь, что эмулятор Azure Cosmos DB работает.

2. Откройте окно терминала и подключитесь (`cd`) к каталогу, в котором будет сохранено приложение.

3. Затем задайте переменную среды, используя `set FLASK_APP=app.py` для приложения Flask, `$env:FLASK_APP = app.py` для редакторов PowerShell или `export FLASK_APP=app.py` для Mac. 

4. Запустите приложение с `flask run` и перейдите по адресу *http:\//127.0.0.1:5000/* .

5. Добавьте и удалите задачи в коллекции и отследите их добавление и изменение в коллекции.

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

Если вы хотите проверить код в работающей учетной записи Azure Cosmos DB, тогда перейдите на портал Azure, чтобы создать учетную запись.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Обновление строки подключения

Получите сведения о строке подключения, чтобы проверить код в работающей учетной записи Azure Cosmos DB. Затем скопируйте их в приложение.

1. В учетной записи Azure Cosmos DB на портале Azure, на панели навигации слева выберите **Строка подключения**, а затем выберите **Ключи для чтения и записи**. В следующих шагах используйте кнопки копирования в правой части экрана, чтобы скопировать имя пользователя, строку подключения и пароль. 

2. Откройте в корневом каталоге файл *app.py*.

3. Скопируйте **имя пользователя** c портала (с помощью кнопки копирования) и добавьте его в качестве значения параметра **name** в файле *app.py*.

4. Затем скопируйте **строку подключения** с портала и настройте ее в качестве значения **MongoClient** в файле *app.py*.

5. И наконец, скопируйте **пароль** с портала и добавьте его в качестве значения параметра **password** в файле *app.py*.

Теперь приложение со всеми сведениями, необходимыми для взаимодействия с Azure Cosmos DB, обновлено. Это приложение можно запустить так же, как и прежде.

## <a name="deploy-to-azure"></a>Развертывание в Azure

Чтобы развернуть это приложение, можно создать веб-приложение в Azure и включить непрерывное развертывание с ветвления в репозитории GitHub. Для настройки непрерывного развертывания с GitHub в Azure используйте руководство [Непрерывное развертывание в службе приложений Azure](../app-service/deploy-continuous-deployment.md).

При развертывании в Azure следует удалить ключи приложения и убедиться, что приведенный ниже раздел не закомментирован:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Затем к параметрам приложения необходимо добавить MONGOURL, MONGO_PASSWORD и MONGO_USERNAME. Дополнительные сведения о параметрах приложений в веб-приложениях Azure см. в разделе [Параметры приложения](../app-service/configure-common.md#configure-app-settings).

Если вы не хотите создавать вилку этого репозитория, можете нажать кнопку **Развертывание в Azure** ниже. Затем перейдите в Azure и настройте параметры приложения с информацией о своей учетной записи Azure Cosmos DB.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Если вы планируете хранить в GitHub код или другие параметры системы управления версиями, удалите строки подключения из кода. Их можно задать с помощью параметров приложения вместо веб-приложения.

## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Дальнейшие действия

В этом кратком руководстве вы узнали, как создавать Azure Cosmos DB для учетной записи API Mongo DB и эмулятора Azure Cosmos DB, чтобы выполнить клонирование веб-приложения Flask To-Do Python из GitHub. Теперь можно импортировать дополнительные данные в учетную запись Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Перенос данных MongoDB в Azure Cosmos DB](../dms/tutorial-mongodb-cosmos-db.md?toc=%252fazure%252fcosmos-db%252ftoc.json%253ftoc%253d%252fazure%252fcosmos-db%252ftoc.json)