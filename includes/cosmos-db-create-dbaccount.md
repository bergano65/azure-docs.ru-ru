---
title: включение файла
description: включение файла
services: cosmos-db
author: rimman
ms.service: cosmos-db
ms.topic: include
ms.date: 04/08/2019
ms.author: rimman
ms.custom: include file
ms.openlocfilehash: 93652273dda16e93bdcf9123d7d4a9b3fc956fe9
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73466187"
---
1. Создайте учетную запись для базы данных Azure Cosmos DB, [перейдя на портал Azure](https://portal.azure.com/). Найдите в поиске и выберите **Azure Cosmos DB**

   ![Область "Базы данных" на портале Azure](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

1. Выберите **Добавить**.
1. На странице **создания учетной записи Azure Cosmos DB** введите основные параметры для новой учетной записи Azure Cosmos. 

    |Параметр|Значение|ОПИСАНИЕ |
    |---|---|---|
    |Subscription|Имя подписки|Выберите подписку Azure, которую нужно использовать для этой учетной записи Azure Cosmos. |
    |Группа ресурсов|Имя группы ресурсов|Выберите группу ресурсов или **Создать**, затем введите уникальное имя для новой группы ресурсов. |
    |Имя учетной записи|Уникальное имя|Введите имя для идентификации учетной записи Azure Cosmos. Так как элемент *documents.azure.com* добавляется к указанному вами идентификатору для создания URI, используйте уникальный идентификатор.<br><br>Идентификатор может содержать только строчные буквы, цифры и знак дефиса (-). Длина — от 3 до 31 знака.|
    |API|Тип учетной записи, которую нужно создать.|Выберите **Core (SQL)** для создания базы данных документов и запроса с использованием синтаксиса SQL. <br><br>API определяет тип учетной записи, которую нужно создать. Azure Cosmos DB предоставляет пять API: Core (SQL) и MongoDB для данных документа, Gremlin для данных графа, таблица Azure и Cassandra. Сейчас для каждого API требуется создавать отдельную учетную запись. <br><br>[Дополнительные сведения об API SQL](../articles/cosmos-db/documentdb-introduction.md)|
    |Location|Ближайший к пользователям регион|Выберите географическое расположение для размещения учетной записи Azure Cosmos DB. Используйте ближайшее к пользователям расположение, чтобы предоставить им максимально быстрый доступ к данным.|

   ![Страница "Новая учетная запись" для Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-account.png)

1. Выберите **Review + create** (Просмотреть и создать). Вы можете пропустить разделы **Сеть** и **Теги**.

1. Проверьте параметры учетной записи, а затем нажмите кнопку **Создать**. Создание учетной записи занимает несколько минут. Дождитесь, пока на странице портала появится сообщение **Развертывание выполнено**. 

    ![Область "Уведомления" на портале Azure](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

1. Выберите **Перейти к ресурсу**, чтобы перейти на страницу учетной записи Azure Cosmos DB. 

    ![Страница учетной записи Azure Cosmos DB](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created-2.png)
