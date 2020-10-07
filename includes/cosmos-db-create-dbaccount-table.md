---
title: включить файл
description: включить файл
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 01/24/2020
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 5015371afc67574a214097f8d3eef661df29c22f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "85115050"
---
1. В новом окне браузера войдите на [портал Azure](https://portal.azure.com/).

2. В меню слева выберите **Создать ресурс**.
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-0.png" alt-text="Создание ресурса на портале Azure":::
   
3. На странице **New** (Новый) выберите **Базы данных** > **Azure Cosmos DB**.
   
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png" alt-text="Создание ресурса на портале Azure":::
   
3. На странице **Создание учетной записи Azure Cosmos DB** введите параметры для новой учетной записи Azure Cosmos DB. 
 
    Параметр|Значение|Описание
    ---|---|---
    Подписка|Ваша подписка|Вы подписку Azure, которую нужно использовать для этой учетной записи Azure Cosmos DB. 
    Группа ресурсов|Щелкните **Создать**, а затем введите имя учетной записи.|Выберите **Создать**. Затем введите новое имя группы ресурсов для учетной записи. Для удобства можно использовать то же имя, которое присвоено учетной записи Azure Cosmos DB. 
    Имя учетной записи|Уникальное имя|Введите уникальное имя для идентификации вашей учетной записи Azure Cosmos DB.<br><br>Имя может содержать только строчные буквы, цифры и дефисы. Его длина должна быть от 3 до 31 знаков.
    API|Таблица|API определяет тип учетной записи, которую нужно создать. Azure Cosmos DB предоставляет пять API: API Core (SQL) для баз данных документов, API Gremlin для графовых баз данных, API MongoDB для баз данных документов, API таблиц Azure и API Cassandra. Для каждого API требуется создать отдельную учетную запись. <br><br>Выберите **Таблица Azure**, так как в этом кратком руководстве создается таблица, которая работает с API таблиц. <br><br>[Подробные сведения об API таблиц](../articles/cosmos-db/table-introduction.md).|
    Расположение|Ближайший к пользователям регион|Выберите географическое расположение для размещения учетной записи Azure Cosmos DB. Используйте ближайшее к пользователям расположение, чтобы предоставить им максимально быстрый доступ к данным.

    Вы можете не изменять значения по умолчанию для параметров **Геоизбыточность** и **Записи в нескольких регионах** (**Отключить**), чтобы избежать дополнительных затрат, и пропустить разделы **Сеть** и **Теги**.

5. Выберите **Просмотр и создание**. После завершения проверки нажмите **Создать**, чтобы создать учетную запись. 
 
   :::image type="content" source="./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png" alt-text="Создание ресурса на портале Azure":::

6. Создание учетной записи занимает несколько минут. Вы увидите сообщение **Развертывание выполняется**. Дождитесь завершения развертывания, а затем нажмите кнопку **Перейти к ресурсу**.

    :::image type="content" source="./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png" alt-text="Создание ресурса на портале Azure":::

