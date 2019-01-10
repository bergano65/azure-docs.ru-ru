---
title: Запрос данных с помощью API Azure Cosmos DB для MongoDB
description: Узнайте, как запрашивать данные с помощью API Azure Cosmos DB для MongoDB.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: tutorial
ms.date: 12/26/2018
ms.reviewer: sngun
ms.openlocfilehash: 8bdd88652019ceb48cfd9f05d1009271f5b7a8c7
ms.sourcegitcommit: 8330a262abaddaafd4acb04016b68486fba5835b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54042994"
---
# <a name="query-data-by-using-azure-cosmos-dbs-api-for-mongodb"></a>Запрос данных с использованием API Azure Cosmos DB для MongoDB

[API Azure Cosmos DB для MongoDB](mongodb-introduction.md) поддерживает [запросы MongoDB](https://docs.mongodb.com/manual/tutorial/query-documents/). 

В этой статье рассматриваются следующие задачи: 

> [!div class="checklist"]
> * Запрос данных, хранящихся в базе данных Cosmos, с помощью оболочки MongoDB

Начать работу можно с использования примеров в этом документе и просмотра видео [о запросе Azure Cosmos DB с помощью оболочки MongoDB](https://azure.microsoft.com/resources/videos/query-azure-cosmos-db-data-by-using-the-mongodb-shell/).

## <a name="sample-document"></a>Пример документа

В запросах в этой статье используется следующий образец документа.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```
## <a id="examplequery1"></a> Пример запроса 1 

С учетом приведенного выше образца документа сведений о семье следующий запрос возвращает документы, у которых поле идентификации соответствует `WakefieldFamily`.

**Запрос**
    
    db.families.find({ id: "WakefieldFamily"})

**Результаты**

    {
    "_id": "ObjectId(\"58f65e1198f3a12c7090e68c\")",
    "id": "WakefieldFamily",
    "parents": [
      {
        "familyName": "Wakefield",
        "givenName": "Robin"
      },
      {
        "familyName": "Miller",
        "givenName": "Ben"
      }
    ],
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ],
    "address": {
      "state": "NY",
      "county": "Manhattan",
      "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
    }

## <a id="examplequery2"></a> Пример запроса 2 

Следующий запрос возвращает перечень всех детей в семье. 

**Запрос**
    
    db.families.find( { id: "WakefieldFamily" }, { children: true } )

**Результаты**

    {
    "_id": "ObjectId("58f65e1198f3a12c7090e68c")",
    "children": [
      {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [
          { "givenName": "Goofy" },
          { "givenName": "Shadow" }
        ]
      },
      {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
      }
    ]
    }


## <a id="examplequery3"></a> Пример запроса 3 

Следующий запрос возвращает перечень всех семей, которые зарегистрированы. 

**Запрос**
    
    db.families.find( { "isRegistered" : true })
**Результаты**: документ не будет возвращен. 

## <a id="examplequery4"></a> Пример запроса 4

Следующий запрос возвращает перечень всех семей, которые не зарегистрированы. 

**Запрос**
    
    db.families.find( { "isRegistered" : false })
**Результаты**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery5"></a> Пример запроса 5

Следующий запрос возвращает перечень всех семей, которые не зарегистрированы и живут в штате Нью-Йорк. 

**Запрос**
    
     db.families.find( { "isRegistered" : false, "address.state" : "NY" })

**Результаты**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}


## <a id="examplequery6"></a> Пример запроса 6

Следующий запрос возвращает все семьи, где дети ходят в 8-й класс.

**Запрос**
  
     db.families.find( { children : { $elemMatch: { grade : 8 }} } )

**Результаты**

     {
    "_id": ObjectId("58f65e1198f3a12c7090e68c"),
    "id": "WakefieldFamily",
    "parents": [{
        "familyName": "Wakefield",
        "givenName": "Robin"
    }, {
        "familyName": "Miller",
        "givenName": "Ben"
    }],
    "children": [{
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1,
        "pets": [{
            "givenName": "Goofy"
        }, {
            "givenName": "Shadow"
        }]
    }, {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }],
    "address": {
        "state": "NY",
        "county": "Manhattan",
        "city": "NY"
    },
    "creationDate": 1431620462,
    "isRegistered": false
}

## <a id="examplequery7"></a> Пример запроса 7

Следующий запрос возвращает семьи, где 3 детей.

**Запрос**
  
      db.Family.find( {children: { $size:3} } )

**Результаты**

Результаты не будут выведены, так как у семей в документе не больше двоих детей. Этот запрос будет успешным и вернет полный документ, только если параметр будет равен 2.

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы выполнили следующее:

> [!div class="checklist"]
> * научились выполнять запросы с помощью API Cosmos DB для MongoDB.

Теперь вы можете приступать к следующему руководству, чтобы узнать, как глобально распределять данные.

> [!div class="nextstepaction"]
> [Глобальное распределение данных](tutorial-global-distribution-sql-api.md)

