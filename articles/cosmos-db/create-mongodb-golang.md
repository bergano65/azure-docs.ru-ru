---
title: Создание консольного приложения с помощью API для MongoDB (Azure Cosmos DB) и пакета SDK Golang
description: В этой статье представлен пример кода Golang, который можно использовать для подключения и выполнения запросов c помощью API Azure Cosmos DB для MongoDB.
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: quickstart
ms.date: 12/26/2018
ms.author: rimman
ms.openlocfilehash: 5b60ac28cd8f65d464e659f328872524be59b3ed
ms.sourcegitcommit: 7723b13601429fe8ce101395b7e47831043b970b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56586886"
---
# <a name="quickstart-build-a-console-app-using-azure-cosmos-dbs-api-for-mongodb-and-golang-sdk"></a>Краткое руководство. Создание консольного приложения с помощью API для MongoDB (Azure Cosmos DB) и пакета SDK Golang

> [!div class="op_single_selector"]
> * [.NET](create-mongodb-dotnet.md)
> * [Java](create-mongodb-java.md)
> * [Node.js](create-mongodb-nodejs.md)
> * [Python](create-mongodb-flask.md)
> * [Xamarin](create-mongodb-xamarin.md)
> * [Golang](create-mongodb-golang.md)
>  

Azure Cosmos DB — это глобально распределенная многомодельная служба базы данных Майкрософт. Вы можете быстро создавать и запрашивать документы, пары "ключ-значение" и графовые базы данных, используя преимущества глобального распределения и горизонтального масштабирования Cosmos DB.

В этом кратком руководстве описывается, как использовать имеющееся приложение MongoDB, написанное на языке [Golang](https://golang.org/), а также как подключить его к базе данных Cosmos c помощью API Azure Cosmos DB для MongoDB.

Другими словами, только приложение Golang "знает" о подключении с помощью клиента MongoDB. Приложению понятно, что данные хранятся в базе данных Cosmos.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free), прежде чем начинать работу. 

  [!INCLUDE [cosmos-db-emulator-mongodb](../../includes/cosmos-db-emulator-mongodb.md)]

- [Go](https://golang.org/dl/) и базовые знания о языке [Go](https://golang.org/).
- Интегрированная среда разработки: [Gogland](https://www.jetbrains.com/go/) от Jetbrains, [Visual Studio Code](https://code.visualstudio.com/) от Майкрософт или [Atom](https://atom.io/). В этом руководстве используется GoLand.

<a id="create-account"></a>
## <a name="create-a-database-account"></a>Создание учетной записи базы данных

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount-mongodb.md)]

## <a name="clone-the-sample-application"></a>Клонирование примера приложения

Клонируйте пример приложения и установите необходимые пакеты.

1. Создайте папку с именем CosmosDBSample внутри папки GOROOT\src. По умолчанию это папка C:\Go\.
2. Выполните следующую команду в окне терминала git, например git bash, чтобы клонировать пример репозитория в папку CosmosDBSample. 

    ```bash
    git clone https://github.com/Azure-Samples/azure-cosmos-db-mongodb-golang-getting-started.git
    ```
3.  Выполните следующую команду, чтобы получить пакет mgo. 

    ```
    go get gopkg.in/mgo.v2
    ```

Драйвер [mgo](https://labix.org/mgo) — это драйвер [MongoDB](https://www.mongodb.com/) для [языка Go](https://golang.org/), реализующий расширенный и протестированный набор функций в очень простом API в соответствии со стандартными идиомами Go.

<a id="connection-string"></a>

## <a name="update-your-connection-string"></a>Обновление строки подключения

Теперь вернитесь на портал Azure, чтобы получить данные строки подключения. Скопируйте эти данные в приложение.

1. Выберите раздел **Быстрый запуск** в меню навигации слева, а затем откройте вкладку **Другие**, чтобы просмотреть сведения строки подключения, необходимые для приложения Go.

2. В Goglang откройте файл main.go в каталоге GOROOT\CosmosDBSample и измените следующие строки кода, используя сведения строки подключения с портала Azure, как показано на следующем снимке экрана. 

    Имя базы данных — это префикс значения **узла** в области строки подключения на портале Azure. Для учетной записи, показанной на рисунке ниже, имя базы данных — golang-coach.

    ```go
    Database: "The prefix of the Host value in the Azure portal",
    Username: "The Username in the Azure portal",
    Password: "The Password in the Azure portal",
    ```

    ![Панель быстрого запуска и вкладка "Другие" на портале Azure, отображающая сведения строки подключения](./media/create-mongodb-golang/cosmos-db-golang-connection-string.png)

3. Сохраните файл main.go.

## <a name="review-the-code"></a>Просмотр кода

Этот шаг не является обязательным. Если вы хотите узнать, как создать в коде ресурсы базы данных, изучите приведенные ниже фрагменты кода. Или вы можете сразу перейти к [запуску приложения](#run-the-app). 

Приведенные ниже фрагменты кода взяты из файла main.go.

### <a name="connecting-the-go-app-to-cosmos-db"></a>Подключение приложения Go к Cosmos DB

API Azure Cosmos DB для MongoDB поддерживает подключение по протоколу SSL. Чтобы подключиться, необходимо определить функцию **DialServer** в разделе [mgo.DialInfo](https://godoc.org/gopkg.in/mgo.v2#DialInfo) и использовать функцию [tls.*Dial*](https://golang.org/pkg/crypto/tls#Dial) для установки подключения.

Следующий фрагмент кода Golang подключается к приложению Go с помощью API Azure Cosmos DB для MongoDB. Класс *DialInfo* содержит параметры для установки сеанса подключения.

```go
// DialInfo holds options for establishing a session.
dialInfo := &mgo.DialInfo{
    Addrs:    []string{"golang-couch.documents.azure.com:10255"}, // Get HOST + PORT
    Timeout:  60 * time.Second,
    Database: "database", // It can be anything
    Username: "username", // Username
    Password: "Azure database connect password from Azure Portal", // PASSWORD
    DialServer: func(addr *mgo.ServerAddr) (net.Conn, error) {
        return tls.Dial("tcp", addr.String(), &tls.Config{})
    },
}

// Create a session which maintains a pool of socket connections
// to Cosmos database (using Azure Cosmos DB's API for MongoDB).
session, err := mgo.DialWithInfo(dialInfo)

if err != nil {
    fmt.Printf("Can't connect, go error %v\n", err)
    os.Exit(1)
}

defer session.Close()

// SetSafe changes the session safety mode.
// If the safe parameter is nil, the session is put in unsafe mode, 
// and writes become fire-and-forget,
// without error checking. The unsafe mode is faster since operations won't hold on waiting for a confirmation.
// 
session.SetSafe(&mgo.Safe{})
```

Метод **mgo.Dial()** используется, когда отсутствует подключение SSL. Для подключения SSL требуется метод **mgo.DialWithInfo()**.

Для создания объекта сеанса используется экземпляр объекта **DialWIthInfo{}**. После установки сеанса можно получить доступ к коллекции, используя следующий фрагмент кода:

```go
collection := session.DB("database").C("package")
```

<a id="create-document"></a>

### <a name="create-a-document"></a>Создание документа

```go
// Model
type Package struct {
    Id bson.ObjectId  `bson:"_id,omitempty"`
    FullName      string
    Description   string
    StarsCount    int
    ForksCount    int
    LastUpdatedBy string
}

// insert Document in collection
err = collection.Insert(&Package{
    FullName:"react",
    Description:"A framework for building native apps with React.",
    ForksCount: 11392,
    StarsCount:48794,
    LastUpdatedBy:"shergin",

})

if err != nil {
    log.Fatal("Problem inserting data: ", err)
    return
}
```

### <a name="query-or-read-a-document"></a>Запрос к документу или чтение документа

Cosmos DB поддерживает полнофункциональные запросы к данным, хранящимся в каждой коллекции. В следующем примере кода показан выполняемый запрос к документам в коллекции.

```go
// Get a Document from the collection
result := Package{}
err = collection.Find(bson.M{"fullname": "react"}).One(&result)
if err != nil {
    log.Fatal("Error finding record: ", err)
    return
}

fmt.Println("Description:", result.Description)
```


### <a name="update-a-document"></a>обновление документа;

```go
// Update a document
updateQuery := bson.M{"_id": result.Id}
change := bson.M{"$set": bson.M{"fullname": "react-native"}}
err = collection.Update(updateQuery, change)
if err != nil {
    log.Fatal("Error updating record: ", err)
    return
}
```

### <a name="delete-a-document"></a>Удаление документа

Cosmos DB поддерживает удаление документов.

```go
// Delete a document
query := bson.M{"_id": result.Id}
err = collection.Remove(query)
if err != nil {
   log.Fatal("Error deleting record: ", err)
   return
}
```
    
## <a name="run-the-app"></a>Запуск приложения

1. В Golang убедитесь, что путь GOPATH (находится в меню **File** (Файл), **Settings** (Параметры), **Go**, **GOPATH**) включает расположение, в котором установлен gopkg. По умолчанию это папка USERPROFILE\go. 
2. Закомментируйте строки, удаляющие документ (строки 103–107), чтобы вы могли видеть документ после запуска приложения.
3. В Golang выберите команду **Run** (Выполнить), затем нажмите кнопку **Run 'Build main.go and run'** (Выполнить команду "Создать и выполнить main.go").

    Будет создано приложение и отобразится описание документа, созданного в разделе [Создание документа](#create-document).
    
    ```
    Description: A framework for building native apps with React.
    
    Process finished with exit code 0
    ```

    ![Среда Golang, отображающая выходные данные приложения](./media/create-mongodb-golang/goglang-cosmos-db.png)
    
## <a name="review-your-document-in-data-explorer"></a>Просмотр документа в обозревателе данных

Вернитесь на портал Azure, чтобы просмотреть документ в обозревателе данных.

1. Щелкните **Обозреватель данных (предварительная версия)** в меню навигации слева, разверните узел **golang-coach**, **Пакет**, а затем выберите раздел **Документы**. На вкладке **Документы** щелкните \_идентификатор, чтобы открыть документ на панели справа. 

    ![Обозреватель данных, отображающий только что созданный документ](./media/create-mongodb-golang/golang-cosmos-db-data-explorer.png)
    
2. Вы можете работать со встроенным документом. Чтобы сохранить его, нажмите кнопку **Обновить**. Вы также можете удалить документ или создать новые документы или запросы.

## <a name="review-slas-in-the-azure-portal"></a>Просмотр соглашений об уровне обслуживания на портале Azure

[!INCLUDE [cosmosdb-tutorial-review-slas](../../includes/cosmos-db-tutorial-review-slas.md)]

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [cosmosdb-delete-resource-group](../../includes/cosmos-db-delete-resource-group.md)]

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы узнали, как создать учетную запись Cosmos и запустить приложение Golang. Теперь вы можете импортировать дополнительные данные в базу данных Cosmos. 

> [!div class="nextstepaction"]
> [Перенос данных MongoDB в Azure Cosmos DB](mongodb-migrate.md)
