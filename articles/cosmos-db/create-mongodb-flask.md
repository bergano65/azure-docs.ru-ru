---
title: Создание веб-приложения Flask с использованием API Azure Cosmos DB для MongoDB и пакета SDK для Python
description: В этой статье представлен пример кода Python Flask, который можно использовать для подключения и выполнения запросов c помощью API Azure Cosmos DB для MongoDB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: python
ms.topic: quickstart
ms.date: 12/26/2018
ms.openlocfilehash: 24c47acd028aa767d1f02dd7c4119692eeac479a
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56587027"
---
# <a name="quickstart-build-a-python-app-using-azure-cosmos-dbs-api-for-mongodb"></a>Краткое руководство. Создание приложения Python с использованием API Azure Cosmos DB для MongoDB

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ-значение" и графовые базы данных, используя преимущества глобального распределения и горизонтального масштабирования Cosmos DB.

В этом кратком руководстве используется следующий [пример Flask](https://github.com/Azure-Samples/CosmosDB-Flask-Mongo-Sample) и демонстрируется, как создать простое приложение Flask с помощью [эмулятора Azure Cosmos DB](local-emulator.md) и API Azure Cosmos DB для MongoDB.

## <a name="prerequisites"></a>Предварительные требования

- Скачайте [эмулятор Azure Cosmos DB](local-emulator.md). Сейчас эмулятор поддерживается только для Windows. В примере показано, как использовать образец с рабочим ключом от Azure, и это можно сделать на любой платформе.

- Если у вас еще не установлено средство Visual Studio Code, [его](https://code.visualstudio.com/Download) можно быстро установить для вашей платформы (Windows, Mac, Linux).

- Не забудьте добавить поддержку языка Python, установив одно из распространенных расширений Python.
    1. Выберите расширение.
    2. Установите расширение, введя `ext install` в палитре команд (`Ctrl+Shift+P`).

    Примеры из этого документа используют все возможности популярного [расширения Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python) от пользователя DonJayamanne.

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

Этот шаг не является обязательным. Если вы хотите узнать, как создать в коде ресурсы базы данных, изучите приведенные ниже фрагменты кода. Или вы можете сразу перейти к [запуску приложения](#run-the-web-app). 

Следующие фрагменты кода взяты из файла app.py. В них используется строка подключения для локального эмулятора Azure Cosmos DB. Как показано ниже, пароль необходимо разделить, чтобы адаптироваться к косым чертам, которые невозможно проанализировать иным образом.

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

* Создание приложения

    ```Python
    app = Flask(__name__)
    title = "TODO with Flask"
    heading = "ToDo Reminder"
    ```
    
## <a name="run-the-web-app"></a>Запуск веб-приложения

1. Убедитесь, что эмулятор Azure Cosmos DB работает.

2. Откройте окно терминала и подключитесь (`cd`) к каталогу, в котором будет сохранено приложение.

3. Если вы используете Mac, задайте переменную среды для приложения Flask `set FLASK_APP=app.py` или `export FLASK_APP=app.py`.

4. Запустите приложение с `flask run` и перейдите по адресу [http://127.0.0.1:5000/](http://127.0.0.1:5000/).

5. Добавьте и удалите задачи в коллекции и отследите их добавление и изменение в коллекции.

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="update-your-connection-string"></a>Обновление строки подключения

Если вы хотите проверить код в работающей учетной записи Cosmos, тогда перейдите на портал Azure, чтобы создать учетную запись и получить данные строк подключения. Затем скопируйте их в приложение.

1. На [портале Azure](https://portal.azure.com/) перейдите к учетной записи базы данных Cosmos и на левой панели навигации щелкните **Строка подключения**, а затем щелкните **Ключи записи-чтения**. На следующем шаге используйте кнопку копирования в правой части экрана, чтобы скопировать имя пользователя, пароль и узел в файл Dal.cs.

2. Откройте в корневом каталоге файл**app.py**.

3. Скопируйте **имя пользователя** c портала (с помощью кнопки копирования) и добавьте его в качестве значения параметра **name** в файле **app.py**.

4. Затем скопируйте **строку подключения** с портала и настройте ее в качестве значения MongoClient в файле **app.py**.

5. И наконец, скопируйте **пароль** с портала и добавьте его в качестве значения параметра **password** в файле **app.py**.

Теперь приложение со всеми сведениями, необходимыми для взаимодействия с Cosmos DB, обновлено. Это приложение можно запустить так же, как и прежде.

## <a name="deploy-to-azure"></a>Развернуть в Azure

Чтобы развернуть это приложение, можно создать веб-приложение в Azure и включить непрерывное развертывание с ветвления в репозитории GitHub. Для настройки непрерывного развертывания с GitHub в Azure используйте руководство [Непрерывное развертывание в службе приложений Azure](https://docs.microsoft.com/azure/app-service/deploy-continuous-deployment).

При развертывании в Azure следует удалить ключи приложения и убедиться, что приведенный ниже раздел не закомментирован:

```python
    client = MongoClient(os.getenv("MONGOURL"))
    db = client.test    #Select the database
    db.authenticate(name=os.getenv("MONGO_USERNAME"),password=os.getenv("MONGO_PASSWORD"))
```

Затем к параметрам приложения необходимо добавить MONGOURL, MONGO_PASSWORD и MONGO_USERNAME. Дополнительные сведения о параметрах приложений в веб-приложениях Azure см. в разделе [Параметры приложения](https://docs.microsoft.com/azure/app-service-web/web-sites-configure#application-settings).

Если вы не хотите создавать вилку этого репозитория, можете нажать кнопку "Развертывание в Azure" ниже. Затем перейдите в Azure и настройте параметры приложения с информацией о своей учетной записи Cosmos DB.

<a href="https://deploy.azure.com/?repository=https://github.com/heatherbshapiro/To-Do-List---Flask-MongoDB-Example" target="_blank">
<img src="https://azuredeploy.net/deploybutton.png" alt="Click to Deploy to Azure">
</a>

> [!NOTE]
> Если вы планируете хранить в GitHub код или другие параметры системы управления версиями, удалите строки подключения из кода. Их можно задать с помощью параметров приложения вместо веб-приложения.

## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы узнали, как создать учетную запись Cosmos и запустить приложение Flask. Теперь вы можете импортировать дополнительные данные в базу данных Cosmos. 

> [!div class="nextstepaction"]
> [Перенос данных MongoDB в Azure Cosmos DB](mongodb-migrate.md)
