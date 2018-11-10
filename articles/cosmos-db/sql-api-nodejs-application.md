---
title: Создание веб-приложения Node.js с помощью пакета SDK JavaScript для управления данными API SQL для Azure Cosmos DB | Документация Майкрософт
description: В этом руководстве по Node.js описывается использование Microsoft Azure Cosmos DB для хранения данных и доступа к ним из веб-приложения Node.js Express, размещенного на веб-сайтах Azure.
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 09/24/2018
ms.author: sngun
ms.openlocfilehash: 0c99b7d1ef774e20a49564db269555bab95789a3
ms.sourcegitcommit: ae45eacd213bc008e144b2df1b1d73b1acbbaa4c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/01/2018
ms.locfileid: "50741796"
---
# <a name="tutorial-build-a-nodejs-web-app-using-javascript-sdk-to-manage-azure-cosmos-db-sql-api-data"></a>Руководство. Создание веб-приложения Node.js с помощью пакета SDK JavaScript для управления данными API SQL для Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

В этом руководстве по Node.js показано, как с помощью приложения Node.js Express, размещенного на веб-сайтах Azure, хранить данные и обеспечивать доступ к ним из учетной записи API SQL для Azure Cosmos DB. В этом руководстве вы создадите веб-приложение (приложение со списком задач), которое позволит вам создавать, извлекать и выполнять задачи. Задачи будут храниться в виде документов JSON в Azure Cosmos DB. 

В этом руководстве показано, как создать учетную запись API SQL для Azure Cosmos DB с помощью портала Azure. Затем вы создадите и запустите веб-приложение, созданное на основе пакета SDK для Node.js, для создания базы данных, контейнера и добавления элементов в контейнер. В этом руководстве используется пакет SDK для JavaScript версии 2.0.

Вы также можете получить полный пример из репозитория [GitHub][GitHub]. Инструкции по запуску приложения см. в файле [Readme](https://github.com/Azure-Samples/documentdb-node-todo-app/blob/master/README.md).

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * создание учетной записи Azure Cosmos DB;
> * создание приложения Node.js;
> * подключение приложения к Azure Cosmos DB;
> * запуск и развертывание приложения в Azure.

## <a name="_Toc395783176"></a>Предварительные требования

Перед выполнением инструкций, приведенных в этой статье, обеспечьте наличие следующих ресурсов:

* Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 

  [!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

* [Node.js][Node.js] версии 6.10 или выше.
* [Генератор Express](http://www.expressjs.com/starter/generator.html) (его можно установить через `npm install express-generator -g`).
* Установите [Git][Git] на локальной рабочей станции.

## <a name="_Toc395637761"></a>Шаг 1. Создание учетной записи Azure Cosmos DB
Давайте сначала создадим учетную запись Azure Cosmos DB. Если у вас уже есть учетная запись или вы используете эмулятор Azure Cosmos DB в этом руководстве, можно перейти к разделу [Шаг 2. Создание приложения Node.js](#_Toc395783178).

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

[!INCLUDE [cosmos-db-keys](../../includes/cosmos-db-keys.md)]

## <a name="_Toc395783178"></a>Шаг 2. Создание приложения Node.js
Теперь создадим базовый проект Node.js «Привет, мир!» с помощью платформы [Express](http://expressjs.com/) .

1. Откройте предпочитаемый терминал, например командную строку Node.js.

1. Перейдите в каталог, в котором будет сохранено новое приложение.

1. С помощью генератора Express создайте новое приложение с именем **todo**.

   ```bash
   express todo
   ```

1. Откройте каталог **todo** и установите зависимости.

   ```bash
   cd todo
   npm install
   ```

1. Запустите новое приложение.

   ```bash
   npm start
   ```

1. Новое приложение можно просмотреть, перейдя в браузере по адресу [http://localhost:3000](http://localhost:3000).
   
   ![Изучение Node.js — снимок экрана приложения "Привет, мир" в окне браузера](./media/sql-api-nodejs-application/cosmos-db-node-js-express.png)

 Остановите приложение, нажав клавиши CTRL+C в окне терминала, и выберите **Y**, чтобы завершить пакетное задание.

## <a name="_Toc395783179"></a>Шаг 3. Установка необходимых модулей

Файл **package.json** является одним из файлов, создаваемых в корневой папке проекта. Этот файл содержит список дополнительных модулей, необходимых для приложения Node.js. При развертывании этого приложения в Azure этот файл будет использоваться для определения модулей, которые следует установить в Azure для поддержки вашего приложения. Для работы с этим руководством установите еще два пакета.

1. Откройте терминал и установите модуль **async** с помощью npm.

   ```bash
   npm install async --save
   ```

2. Установите модуль **@azure/cosmos** с помощью npm. 

   ```bash
   npm install @azure/cosmos
   ```

## <a name="_Toc395783180"></a>Шаг 4. Подключение приложения Node.js к Azure Cosmos DB
Теперь, когда вы завершили первоначальную настройку и установку, нужно написать код, который требуется приложению со списком задач для обмена данными с Azure Cosmos DB.

### <a name="create-the-model"></a>Создание модели
1. В корне каталога проекта создайте каталог с именем **models**.  

2. В каталоге **models** создайте новый файл с именем **taskDao.js**. Этот файл содержит код, необходимый для создания базы данных и контейнера, и определяет методы чтения, обновления, создания и поиска задач в Azure Cosmos DB. 

3. Скопируйте следующий код в файл **taskDao.js**

   ```nodejs
   // @ts-check
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const debug = require("debug")("todo:taskDao");
   class TaskDao {
     /**
      * Manages reading, adding, and updating Tasks in Cosmos DB
      * @param {CosmosClient} cosmosClient
      * @param {string} databaseId
      * @param {string} containerId
      */
     constructor(cosmosClient, databaseId, containerId) {
       this.client = cosmosClient;
       this.databaseId = databaseId;
       this.collectionId = containerId;

       this.database = null;
       this.container = null;
     }

     async init() {
       debug("Setting up the database...");
       const dbResponse = await this.client.databases.createIfNotExists({
         id: this.databaseId
       });
       this.database = dbResponse.database;
       debug("Setting up the database...done!");
       debug("Setting up the container...");
       const coResponse = await this.database.containers.createIfNotExists({
         id: this.collectionId
       });
       this.container = coResponse.container;
       debug("Setting up the container...done!");
     }

     async find(querySpec) {
       debug("Querying for items from the database");
       if (!this.container) {
         throw new Error("Collection is not initialized.");
       }
       const { result: results } = await this.container.items
        .query(querySpec)
        .toArray();
      return results;
    }

    async addItem(item) {
      debug("Adding an item to the database");
      item.date = Date.now();
      item.completed = false;
      const { body: doc } = await this.container.items.create(item);
      return doc;
    }

    async updateItem(itemId) {
      debug("Update an item in the database");
      const doc = await this.getItem(itemId);
      doc.completed = true;

      const { body: replaced } = await this.container.item(itemId).replace(doc);
      return replaced;
    }

    async getItem(itemId) {
      debug("Getting an item from the database");
      const { body } = await this.container.item(itemId).read();
      return body;
    }
  }

   module.exports = TaskDao;
   ```
4. Сохраните и закройте файл **taskDao.js** .  

### <a name="create-the-controller"></a>Создание контроллера

1. В каталоге проекта **routes** создайте новый файл с именем **tasklist.js**.  

2. Добавьте в **tasklist.js**следующий код. Этот код загружает модули CosmosClient и async, используемые файлом **tasklist.js**. Он также определяет класс **TaskList**, который передается как экземпляр определенного ранее объекта **TaskDao**:
   
   ```nodejs
   const TaskDao = require("../models/TaskDao");

   class TaskList {
     /**
      * Handles the various APIs for displaying and managing tasks
      * @param {TaskDao} taskDao
     */
    constructor(taskDao) {
    this.taskDao = taskDao;
    }
    async showTasks(req, res) {
      const querySpec = {
        query: "SELECT * FROM root r WHERE r.completed=@completed",
        parameters: [
          {
            name: "@completed",
            value: false
          }
        ]
      };

      const items = await this.taskDao.find(querySpec);
      res.render("index", {
        title: "My ToDo List ",
        tasks: items
      });
    }

    async addTask(req, res) {
      const item = req.body;

      await this.taskDao.addItem(item);
      res.redirect("/");
    }

    async completeTask(req, res) {
      const completedTasks = Object.keys(req.body);
      const tasks = [];

      completedTasks.forEach(task => {
        tasks.push(this.taskDao.updateItem(task));
      });

      await Promise.all(tasks);

      res.redirect("/");
    }
  }

  module.exports = TaskList;
   ```

3. Сохраните и закройте файл **tasklist.js** .

### <a name="add-configjs"></a>Добавление config.js

1. В корне каталога проекта создайте файл с именем **config.js**. 

2. Добавьте следующий код в файл **config.js**. Он определяет значения и параметры конфигурации, необходимые нашему приложению.
   
   ```nodejs
   const config = {};

   config.host = process.env.HOST || "[the endpoint URI of your Azure Cosmos DB account]";
   config.authKey =
     process.env.AUTH_KEY || "[the PRIMARY KEY value of your Azure Cosmos DB account";
   config.databaseId = "ToDoList";
   config.containerId = "Items";

   if (config.host.includes("https://localhost:")) {
     console.log("Local environment detected");
     console.log("WARNING: Disabled checking of self-signed certs. Do not have this code in production.");
     process.env.NODE_TLS_REJECT_UNAUTHORIZED = "0";
     console.log(`Go to http://localhost:${process.env.PORT || '3000'} to try the sample.`);
   }

   module.exports = config;
   ```

3. В файле **config.js** замените значения HOST и AUTH_KEY значениями, найденными на странице "Ключи" учетной записи Azure Cosmos DB на [портале Microsoft Azure](https://portal.azure.com). 

4. Сохраните и закройте файл **config.js** .

### <a name="modify-appjs"></a>Изменение app.js

1. В каталоге проекта откройте файл **app.js** . Этот файл был создан ранее, при создании веб-приложения Express.  

2. Добавьте следующий код в файл **app.js**. Этот код определяет используемый файл конфигурации и загружает значения в переменные, которые будут использоваться в следующих разделах. 
   
   ```nodejs
   const CosmosClient = require("@azure/cosmos").CosmosClient;
   const config = require("./config");
   const TaskList = require("./routes/tasklist");
   const TaskDao = require("./models/taskDao");

   const express = require("express");
   const path = require("path");
   const logger = require("morgan");
   const cookieParser = require("cookie-parser");
   const bodyParser = require("body-parser");

   const app = express();

   // view engine setup
   app.set("views", path.join(__dirname, "views"));
   app.set("view engine", "jade");

   // uncomment after placing your favicon in /public
   //app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
   app.use(logger("dev"));
   app.use(bodyParser.json());
   app.use(bodyParser.urlencoded({ extended: false }));
   app.use(cookieParser());
   app.use(express.static(path.join(__dirname, "public")));

   //Todo App:
   const cosmosClient = new CosmosClient({
     endpoint: config.host,
     auth: {
       masterKey: config.authKey
     }
   });
   const taskDao = new TaskDao(cosmosClient, config.databaseId, config.containerId);
   const taskList = new TaskList(taskDao);
   taskDao
     .init(err => {
       console.error(err);
     })
     .catch(err => {
       console.error(err);
       console.error("Shutting down because there was an error settinig up the database.");
       process.exit(1);
     });

   app.get("/", (req, res, next) => taskList.showTasks(req, res).catch(next));
   app.post("/addtask", (req, res, next) => taskList.addTask(req, res).catch(next));
   app.post("/completetask", (req, res, next) => taskList.completeTask(req, res).catch(next));
   app.set("view engine", "jade");

   // catch 404 and forward to error handler
   app.use(function(req, res, next) {
     const err = new Error("Not Found");
     err.status = 404;
     next(err);
   });

   // error handler
   app.use(function(err, req, res, next) {
     // set locals, only providing error in development
     res.locals.message = err.message;
     res.locals.error = req.app.get("env") === "development" ? err : {};

     // render the error page
     res.status(err.status || 500);
     res.render("error");
   });

   module.exports = app;
   ```

3. Наконец, сохраните и закройте файл **app.js**.

## <a name="_Toc395783181"></a>Шаг 5. Построение пользовательского интерфейса

Теперь давайте создадим пользовательский интерфейс, чтобы пользователь мог взаимодействовать с приложением. Созданное нами в предыдущих разделах приложение Express использует в качестве обработчика представлений **Jade**. Дополнительные сведения о Jade см. [здесь](http://jade-lang.com/).

1. Файл **layout.jade** в каталоге **views** используется как глобальный шаблон для других файлов **.jade**. На этом шаге он будет изменен для использования [Twitter Bootstrap](https://github.com/twbs/bootstrap) — набора средств для разработки веб-сайта.  

2. Откройте файл **layout.jade**, расположенный в папке **views**, и замените его содержимое следующим кодом:

   ```html
   doctype html
   html
     head
       title= title
       link(rel='stylesheet', href='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/css/bootstrap.min.css')
       link(rel='stylesheet', href='/stylesheets/style.css')
     body
       nav.navbar.navbar-inverse.navbar-fixed-top
         div.navbar-header
           a.navbar-brand(href='#') My Tasks
       block content
       script(src='//ajax.aspnetcdn.com/ajax/jQuery/jquery-1.11.2.min.js')
       script(src='//ajax.aspnetcdn.com/ajax/bootstrap/3.3.2/bootstrap.min.js')
   ```

    Этот код позволяет эффективно сообщить подсистеме **Jade** о том, что необходимо выполнить прорисовку HTML-кода для нашего приложения, и создает **block** с именем **content**, где можно указать макет для страниц содержимого. Сохраните и закройте файл **layout.jade**.

3. Теперь откройте файл **index.jade** (представление, которое будет использоваться нашим приложением) и замените содержимое файла следующим кодом:

   ```html
   extends layout
   block content
        h1 #{title}
        br
        
        form(action="/completetask", method="post")
         table.table.table-striped.table-bordered
            tr
              td Name
              td Category
              td Date
              td Complete
            if (typeof tasks === "undefined")
              tr
                td
            else
              each task in tasks
                tr
                  td #{task.name}
                  td #{task.category}
                  - var date  = new Date(task.date);
                  - var day   = date.getDate();
                  - var month = date.getMonth() + 1;
                  - var year  = date.getFullYear();
                  td #{month + "/" + day + "/" + year}
                  td
                   if(task.completed) 
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
                   else
                    input(type="checkbox", name="#{task.id}", value="#{!task.completed}", checked=task.completed)
          button.btn.btn-primary(type="submit") Update tasks
        hr
        form.well(action="/addtask", method="post")
          label Item Name:
          input(name="name", type="textbox")
          label Item Category:
          input(name="category", type="textbox")
          br
          button.btn(type="submit") Add item
   ```

Этот код дополняет макет и предоставляет содержимое для заполнителя **content**, который мы ранее видели в файле **layout.jade**. В макете мы создали две формы HTML.

Первая форма содержит таблицу для наших данных и кнопку, позволяющую обновлять элементы путем отправки POST-запроса к методу **/completeTask** контроллера.
    
Вторая форма содержит два поля ввода и кнопку, позволяющую создать новый элемент путем POST-запроса к методу **/addtask** контроллера. Это все, что нужно для работы приложения.

## <a name="_Toc395783181"></a>Шаг 6. Локальный запуск приложения

1. Чтобы протестировать приложение на локальном компьютере, выполните `npm start` в терминале для запуска приложения, а затем обновите в браузере страницу [http://localhost:3000](http://localhost:3000). Теперь страница будет выглядеть, как показано на следующем снимке экрана:
   
    ![Снимок экрана приложения «Мой список дел» в окне браузера](./media/sql-api-nodejs-application/cosmos-db-node-js-localhost.png)

    > [!TIP]
    > Если появляется сообщение об ошибке отступа в файле layout.jade или index.jade, выровняйте две первые строки в этих двух файлах по левому краю без пробелов. Если перед первыми двумя строками есть пробелы, удалите их, сохраните оба файла, а затем обновите окно браузера. 

2. Используйте поля "Элемент", "Имя элемента" и "Категория", чтобы указать новую задачу, а затем выберите **Добавить элемент**. Будет создан документ в Azure Cosmos DB с заданными свойствами. 

3. Страница должна обновиться, чтобы отобразить только что созданный элемент в списке ToDo.
   
    ![Снимок экрана приложения с новым элементом в списке дел](./media/sql-api-nodejs-application/cosmos-db-node-js-added-task.png)

4. Чтобы завершить задачу, установите флажок в столбце "Завершено" и выберите **Обновить задачи**. После этого созданный документ будет обновлен и удален из представления.

5. Чтобы остановить приложение, нажмите клавиши CTRL+C в окне терминала и выберите **Y** для завершения пакетного задания.

## <a name="_Toc395783182"></a>Шаг 7. Развертывание приложения на веб-сайтах Azure

1. Если это еще не сделано, включите репозиторий Git для веб-сайта Azure. Соответствующие инструкции см. в статье [Развертывание локального репозитория Git в Службе приложений Azure](../app-service/app-service-deploy-local-git.md).

2. Добавьте веб-сайт Azure в качестве удаленного репозитория git.
   
   ```bash
   git remote add azure https://username@your-azure-website.scm.azurewebsites.net:443/your-azure-website.git
   ```

3. Разверните приложение, отправив его в удаленный репозиторий.
   
   ```bash
   git push azure master
   ```

4. Через несколько секунд веб-приложение будет опубликовано и запущено в браузере.

Если вы хотите скачать полный пример приложения или сослаться на него во время работы с этим руководством, вы можете скачать его из репозитория [GitHub][GitHub].

## <a name="_Toc395637775"></a>Дальнейшие действия

В этом руководстве вы узнали, как создавать веб-приложение Node.js с помощью пакета SDK JavaScript для управления данными API SQL для Azure Cosmos DB. Теперь вы можете перейти к следующей статье:

> [!div class="nextstepaction"]
> [Создание мобильных приложений с помощью Xamarin и Azure Cosmos DB](mobile-apps-with-xamarin.md)


[Node.js]: http://nodejs.org/
[Git]: http://git-scm.com/
[GitHub]: https://github.com/Azure-Samples/azure-cosmos-db-sql-api-nodejs-todo-app

