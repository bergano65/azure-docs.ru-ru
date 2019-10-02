---
title: включение файла
description: включение файла
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 45e6417ed990a233a699d98da89b9b951c1e834d
ms.sourcegitcommit: 7df70220062f1f09738f113f860fad7ab5736e88
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/24/2019
ms.locfileid: "71210323"
---
1. В новом окне браузера войдите на [портал Azure](https://portal.azure.com/).

2. Последовательно выберите **Создать ресурс** > **Базы данных** > **Azure Cosmos DB**.
   
   ![Область "Базы данных" на портале Azure](./media/cosmos-db-create-dbaccount-graph/create-nosql-db-databases-json-tutorial-1.png)

3. На странице **создания учетной записи Azure Cosmos DB** введите параметры для новой учетной записи Azure Cosmos DB. 

    Параметр|Значение|ОПИСАНИЕ
    ---|---|---
    Subscription|Ваша подписка|Вы подписку Azure, которую нужно использовать для этой учетной записи Azure Cosmos DB. 
    Группа ресурсов|Создание<br><br>Введите уникальное имя, указанное в идентификаторе.|Выберите **Создать**. Затем введите новое имя группы ресурсов для учетной записи. Для удобства используйте то же имя, которое присвоено идентификатору. 
    Имя учетной записи|Введите уникальное имя.|Введите уникальное имя для идентификации вашей учетной записи Azure Cosmos DB. Так как элемент *documents.azure.com* добавляется к указанному вами идентификатору для создания URI, используйте уникальный идентификатор.<br><br>Идентификатор может содержать только строчные буквы, цифры и знак дефиса (-). Длина — от 3 до 31 знака.
    API|Gremlin (граф)|API определяет тип учетной записи, которую нужно создать. Azure Cosmos DB предоставляет пять API: API Core (SQL) для баз данных документов, API Gremlin для графовых баз данных, API MongoDB для баз данных документов, API таблиц Azure и API Cassandra. Сейчас для каждого API требуется создавать отдельную учетную запись. <br><br>Выберите **Gremlin (граф)** , так как в этом кратком руководстве создается таблица, которая работает с API Gremlin. <br><br>[Дополнительные сведения об API Graph](../articles/cosmos-db/graph-introduction.md).|
    Location|Выберите ближайший к пользователям регион|Выберите географическое расположение для размещения учетной записи Azure Cosmos DB. Используйте ближайшее к пользователям расположение, чтобы предоставить им максимально быстрый доступ к данным.

    Выберите **Просмотр и создание**. Можете пропустить разделы **Сеть** и **Теги**. 

    ![Раздел "Новая учетная запись" для Azure Cosmos DB](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-create-new-account.png)

4. Создание учетной записи займет несколько минут. Дождитесь, пока на портале откроется страница с сообщением **Congratulations! Your Azure Cosmos DB account was created** (Поздравляем! Ваша учетная запись Azure Cosmos DB создана).

    ![Область "Уведомления" на портале Azure](./media/cosmos-db-create-dbaccount-graph/azure-cosmos-db-graph-created.png)