---
title: Краткое руководство. Использование API Cassandra с Node.js в Azure Cosmos DB
description: В этом руководстве показано, как использовать API Cassandra Azure Cosmos DB для создания приложения профиля с помощью Node.js
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.devlang: nodejs
ms.topic: quickstart
ms.date: 05/18/2020
ms.custom: devx-track-js
ms.openlocfilehash: b9e036df91eecadc701664a19905a92c142b7585
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97591911"
---
# <a name="quickstart-build-a-cassandra-app-with-nodejs-sdk-and-azure-cosmos-db"></a>Краткое руководство. Создание приложения Cassandra с помощью пакета SDK для Node.js и Azure Cosmos DB
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

> [!div class="op_single_selector"]
> * [.NET](create-cassandra-dotnet.md)
> * [.NET Core](create-cassandra-dotnet-core.md)
> * [Java, версия 3](create-cassandra-java.md)
> * [Java, версия 4](create-cassandra-java-v4.md)
> * [Node.js](create-cassandra-nodejs.md)
> * [Python](create-cassandra-python.md)
>  

В этом кратком руководстве вы создадите учетную запись API Cassandra Azure Cosmos DB и используете приложение Cassandra Node.js, клонированное из GitHub, чтобы создать базу данных и контейнер Cassandra. Azure Cosmos DB — это служба многомодельной базы данных, позволяющая быстро создавать и запрашивать документы, таблицы, пары "ключ-значение" и графовые базы данных, используя возможности глобального распределения и горизонтального масштабирования.

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] Кроме того, можно воспользоваться [бесплатной пробной версией Azure Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) без подписки Azure, оплаты и каких-либо обязательств.

Кроме того, вам потребуется:
* [Node.js](https://nodejs.org/dist/v0.10.29/x64/node-v0.10.29-x64.msi) версии 0.10.29 или более поздней.
* [Git](https://git-scm.com/);

## <a name="create-a-database-account"></a>Создание учетной записи базы данных

Прежде чем создавать базу данных документов, необходимо создать в Azure Cosmos DB учетную запись Cassandra.

[!INCLUDE [cosmos-db-create-dbaccount-cassandra](../../includes/cosmos-db-create-dbaccount-cassandra.md)]

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Теперь необходимо клонировать приложение API Cassandra из GitHub, задать строку подключения и запустить приложение. Вы узнаете, как можно упростить работу с данными программным способом. 

1. Откройте командную строку. Создайте папку с именем `git-samples`. Затем закройте командную строку.

    ```bash
    md "C:\git-samples"
    ```

2. Откройте окно терминала Git, например Git Bash. С помощью команды `cd` перейдите в новую папку для установки примера приложения.

    ```bash
    cd "C:\git-samples"
    ```

3. Выполните команду ниже, чтобы клонировать репозиторий с примером. Эта команда создает копию примера приложения на локальном компьютере.

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-cassandra-nodejs-getting-started.git
    ```

## <a name="review-the-code"></a>Просмотр кода

Это необязательный шаг. Если вы хотите узнать, как создавать ресурсы базы данных в коде, изучите приведенные ниже фрагменты кода. Все фрагменты кода взяты из файла `uprofile.js`, расположенного в папке `C:\git-samples\azure-cosmos-db-cassandra-nodejs-getting-started`. Если вас это не интересует, можете сразу переходить к разделу [Обновление строки подключения](#update-your-connection-string). 

* Для указания имени пользователя и пароля используется страница строки подключения на портале Azure. `path\to\cert` указывает путь к сертификату X509. 

   ```javascript
   var ssl_option = {
        cert : fs.readFileSync("path\to\cert"),
        rejectUnauthorized : true,
        secureProtocol: 'TLSv1_2_method'
        };
   const authProviderLocalCassandra = new cassandra.auth.PlainTextAuthProvider(config.username, config.password);
   ```

* `client` инициализируется со сведениями contactPoint. contactPoint извлекается с портала Azure.

    ```javascript
    const client = new cassandra.Client({contactPoints: [config.contactPoint], authProvider: authProviderLocalCassandra, sslOptions:ssl_option});
    ```

* `client` подключается к API Cassandra Azure Cosmos DB.

    ```javascript
    client.connect(next);
    ```

* Создается пространство ключей.

    ```javascript
    function createKeyspace(next) {
        var query = "CREATE KEYSPACE IF NOT EXISTS uprofile WITH replication = {\'class\': \'NetworkTopologyStrategy\', \'datacenter1\' : \'1\' }";
        client.execute(query, next);
        console.log("created keyspace");    
  }
    ```

* Создается таблица.

   ```javascript
   function createTable(next) {
       var query = "CREATE TABLE IF NOT EXISTS uprofile.user (user_id int PRIMARY KEY, user_name text, user_bcity text)";
        client.execute(query, next);
        console.log("created table");
   },
   ```

* Вставляются сущности ключа и значения.

    ```javascript
        function insert(next) {
            console.log("\insert");
            const arr = ['INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (1, \'AdrianaS\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (2, \'JiriK\', \'Toronto\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (3, \'IvanH\', \'Mumbai\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (4, \'IvanH\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (5, \'IvanaV\', \'Belgaum\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (6, \'LiliyaB\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (7, \'JindrichH\', \'Buenos Aires\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (8, \'AdrianaS\', \'Seattle\')',
                        'INSERT INTO  uprofile.user (user_id, user_name , user_bcity) VALUES (9, \'JozefM\', \'Seattle\')'];
            arr.forEach(element => {
            client.execute(element);
            });
            next();
        },
    ```

* Запрос на получение всех значений ключа.

    ```javascript
        function selectAll(next) {
            console.log("\Select ALL");
            var query = 'SELECT * FROM uprofile.user';
            client.execute(query, function (err, result) {
            if (err) return next(err);
            result.rows.forEach(function(row) {
                console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
            }, this);
            next();
            });
        },
    ```  

* Запрос на получение значения ключа.

    ```javascript
        function selectById(next) {
            console.log("\Getting by id");
            var query = 'SELECT * FROM uprofile.user where user_id=1';
            client.execute(query, function (err, result) {
            if (err) return next(err);
            result.rows.forEach(function(row) {
                console.log('Obtained row: %d | %s | %s ',row.user_id, row.user_name, row.user_bcity);
            }, this);
            next();
            });
        }
    ```  

## <a name="update-your-connection-string"></a>Обновление строки подключения

Теперь вернитесь на портал Azure, чтобы получить данные строки подключения. Скопируйте эти данные в приложение. Строка подключения обеспечивает обмен данными между вашим приложением и размещенной базой данных.

1. Выберите элемент **Строка подключения** в своей учетной записи Azure Cosmos DB на [портале Azure](https://portal.azure.com/). 

1. Нажмите кнопку :::image type="icon" source="./media/create-cassandra-nodejs/copy.png"::: в правой части экрана, чтобы скопировать верхнее значение "Contact point" (Контакт).

    :::image type="content" source="./media/create-cassandra-nodejs/keys.png" alt-text="Просмотр и копирование значений параметров Contact point (Контакт), &quot;Пользователь&quot; и &quot;Пароль&quot; на странице строки подключения на портале Azure":::

1. Откройте файл `config.js` . 

1. Вставьте полученное на портале значение параметра Contact point (Контакт) над элементом `<FillMEIN>` в строке 4.

    Теперь строка 4 должна выглядеть примерно так: 

    `config.contactPoint = "cosmos-db-quickstarts.cassandra.cosmosdb.azure.com:10350"`

1. Скопируйте значение параметра "Пользователь" на портале и вставьте его над элементом `<FillMEIN>` в строке 2.

    Теперь строка 2 должна выглядеть примерно так: 

    `config.username = 'cosmos-db-quickstart';`

1. Скопируйте значение параметра "Пароль" с портала и вставьте его над элементом `<FillMEIN>` в строке 3.

    Теперь строка 3 должна выглядеть примерно так:

    `config.password = '2Ggkr662ifxz2Mg==';`

1. Сохраните файл `config.js`.

## <a name="use-the-x509-certificate"></a>Использование сертификата X509

1. Скачайте сертификат Baltimore CyberTrust Root на локальный компьютер из [https://cacert.omniroot.com/bc2025.crt](https://cacert.omniroot.com/bc2025.crt). Переименуйте файл, указав расширение файла `.cer`.

   Сертификат имеет серийный номер `02:00:00:b9` и отпечаток SHA1 `d4:de:20:d0:5e:66:fc:53:fe:1a:50:88:2c:78:db:28:52:ca:e4:74`.

2. Откройте `uprofile.js` и измените `path\to\cert` таким образом, чтобы они указывали на новый сертификат.

3. Сохраните `uprofile.js`.

> [!NOTE]
> Если на последующих этапах возникнет ошибка, связанная с сертификатом,при работе на компьютере Windows, убедитесь, что правильно выполнены все приведенные ниже инструкции по преобразованию CRT-файла в формат Майкрософт CER.
> 
> Дважды щелкните CRT-файл, чтобы открыть его в средстве отображения сертификатов. 
>
> :::image type="content" source="./media/create-cassandra-nodejs/crtcer1.gif" alt-text="Снимок экрана, на котором показано окно &quot;Сертификат&quot;.":::
>
> Нажмите кнопку "Далее" в мастере сертификатов. Выберите элемент "Файлы X.509 (.CER) в кодировке Base-64" и нажмите кнопку "Далее".
>
> :::image type="content" source="./media/create-cassandra-nodejs/crtcer2.gif" alt-text="Снимок экрана, на котором показан элемент &quot;Файлы X.509 (.CER) в кодировке Base-64&quot;.":::
>
> Нажмите кнопку "Обзор" (чтобы определить назначение) и введите имя файла.
> Последовательно нажмите кнопки "Далее" и "Готово".
>
> Теперь у вас должен быть правильно отформатированный CER-файл. Убедитесь, что путь в `uprofile.js` указывает на этот файл.

## <a name="run-the-nodejs-app"></a>Запуск приложения Node.js

1. В окне терминала Git убедитесь, что открыт каталог примеров, который вы клонировали ранее:

    ```bash
    cd azure-cosmos-db-cassandra-nodejs-getting-started
    ```

2. Выполните `npm install`, чтобы установить необходимые модули npm.

3. Запустите `node uprofile.js`, чтобы запустить приложение Node.

4. Проверьте результаты из командной строки.

    :::image type="content" source="./media/create-cassandra-nodejs/output.png" alt-text="Просмотр и проверка выходных данных":::

    Нажмите клавиши CTRL+C, чтобы остановить выполнение программы и закрыть окно консоли. 

5. На портале Azure откройте **обозреватель данных**, чтобы запросить, изменить и обработать новые данные. 

    :::image type="content" source="./media/create-cassandra-nodejs/data-explorer.png" alt-text="Просмотр данных в обозревателе данных"::: 

## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Дальнейшие действия

Из этого краткого руководства вы узнали, как создать учетную запись Azure Cosmos DB с помощью API Cassandra и запустить приложение Cassandra Node.js, которое создает базу данных и контейнер Cassandra. Теперь вы можете импортировать дополнительные данные в учетную запись Azure Cosmos DB. 

> [!div class="nextstepaction"]
> [Импорт данных Cassandra в Azure Cosmos DB](cassandra-import-data.md)