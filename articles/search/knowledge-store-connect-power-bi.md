---
title: Подключение к хранилищу знаний с помощью Power BI
titleSuffix: Azure Cognitive Search
description: Подключение к хранилищу знаний службы "Когнитивный поиск Azure" с помощью Power BI для анализа и исследования.
author: lisaleib
manager: nitinme
ms.author: v-lilei
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: 7852eda849dfb05343829875ba5a66fa47970e7e
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72790066"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Подключение хранилища знаний с помощью Power BI

> [!Note]
> Хранилище знаний предоставляется в предварительной версии и не предназначено для использования в рабочей среде. Этот компонент предоставляется в [REST API службы "Когнитивный поиск Azure" версии 2019-05-06-Preview](search-api-preview.md). Поддержка пакета SDK для .NET пока не реализована.
>
В этой статье объясняется, как подключить и изучить хранилище знаний с помощью Power Query в приложении Power BI Desktop. Сведения о создании примера хранилища знаний, используемого в этом пошаговом руководстве, см. в разделе [Создание хранилища знаний на портале Azure](knowledge-store-create-portal.md).

## <a name="prerequisites"></a>Предварительные требования

+ Выполните действия, описанные в разделе [Создание хранилища знаний на портале Azure](knowledge-store-create-portal.md) , чтобы создать образец хранилища знаний, используемый в этом пошаговом руководстве. Кроме того, вам потребуется имя учетной записи хранения Azure, которая использовалась для создания хранилища знаний, а также его ключ доступа из портала Azure.

+ [Установка Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="connect-with-power-bi"></a>Подключение с помощью Power BI

1. Запустите Power BI Desktop и выберите **Получение данных**.

1. В окне **Получение данных** выберите **Azure**, а затем выберите **Хранилище таблиц Azure**.

1. Щелкните **Подключить**.

1. В поле **Имя или URL-адрес учетной записи** введите имя своей учетной записи хранилища Azure (для вас будет создан полный URL-адрес).

1. При появлении запроса введите ключ учетной записи хранения.

1. Выберите таблицы *hotelReviewsSsDocument*, *hotelReviewsSsKeyPhrases* и *hotelReviewsSsPages*. Эти таблицы являются проекциями таблиц Azure с примерами данных отзывов об отелях и включают обогащение с помощью ИИ, выбранное при создании хранилища знаний.

1. Нажмите кнопку **Загрузить**.

1. На верхней ленте нажмите **Изменить запросы**, чтобы открыть **Редактор Power Query**.

   ![Открытие Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Открытие Power Query")

1. Выберите *hotelReviewsSsDocument*, а затем удалите столбцы *PartitionKey*, *RowKey* и *Метка времени*. 

   ![Изменение таблиц](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Изменение таблиц")

1. Щелкните значок с противоположными стрелками в верхней правой части таблицы, чтобы развернуть *Содержимое*. Когда отобразится список столбцов, выберите все столбцы, а затем отмените выбор столбцов, начинающихся с "метаданных". Щелкните **ОК**, чтобы отобразить выбранные столбцы.

   ![Изменение таблиц](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Развертывание содержимого")

1. Измените тип данных для следующих столбцов, щелкнув значок ABC-123 в левом верхнем углу столбца.

   + Для *content.latitude* и *Content.longitude* выберите **Десятичное число**.
   + Для *Content.reviews_date* и *Content.reviews_dateAdded* выберите **Дата и время**.

   ![Изменение типов данных](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Изменение типов данных")

1. Выберите *hotelReviewsSsPages*, а затем повторите шаги 9 и 10, чтобы удалить столбцы и развернуть *Содержимое*.
1. Измените тип данных для *Content. SentimentScore* на **Десятичное число**.
1. Выберите *hotelReviewsSsKeyPhrases* и повторите шаги 9 и 10, чтобы удалить столбцы и развернуть *Содержимое*. В этой таблице не нужно изменять тип данных.

1. На панели команд щелкните **Закрыть и применить**.

1. Щелкните плитку "Модель" на левой панели навигации и убедитесь, что Power BI отображает связи между всеми тремя таблицами.

   ![Проверка связей](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Проверка связей")

1. Дважды щелкните каждую связь и убедитесь, что **Направление перекрестной фильтрации** установлено на **Обе**.  Это позволяет обновлять визуальные элементы при применении фильтра.

<!-- ## Try with larger data sets

We purposely kept the data set small to avoid charges for a demo walkthrough. For a more realistic experience, you can create and then attach a billable Cognitive Services resource to enable a larger number of transactions against the sentiment analyzer, keyphrase extraction, and language detector skills.

Create new containers in Azure Blob storage and upload each CSV file to its own container. Specify one of these containers in the data source creation step in Import data wizard.

| Description | Link |
|-------------|------|
| Free tier   | [HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Small (500 Records) | [HotelReviews_Small.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Small.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D) |
| Medium (6000 Records)| [HotelReviews_Medium.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Medium.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D)
| Large (Full dataset 35000 Records) | [HotelReviews_Large.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Large.csv?st=2019-07-29T17%3A51%3A30Z&se=2021-07-30T17%3A51%3A00Z&sp=rl&sv=2018-03-28&sr=c&sig=LnWLXqFkPNeuuMgnohiz3jfW4ijePeT5m2SiQDdwDaQ%3D). Be aware that very large data sets are expensive to process. This one costs roughly $1000 U.S dollars.|

In the enrichment step of the wizard, attach a billable [Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) resource, created at the *S0* tier, in the same region as Azure Cognitive Search to use larger data sets. 

  ![Create a Cognitive Services resource](media/knowledge-store-connect-power-bi/create-cognitive-service.png "Create a Cognitive Services resource") -->

## <a name="clean-up"></a>Очистка

Если вы работаете в своей подписке, по окончании проекта рекомендуем решить, нужны ли вам созданные ресурсы. Работающие ресурсы могут означать лишние затраты. Можно удалить отдельные ресурсы или удалить группу ресурсов, что позволит удалить весь набор ресурсов.

Просматривать ресурсы и управлять ими можно на портале с помощью ссылок **Все ресурсы** или **Группы ресурсов** на панели навигации слева.

При работе с бесплатной версией службы помните о том, что вам доступно максимум три индекса, индексатора и источника данных. Вы можете удалить отдельные элементы на портале, чтобы не превысить лимит.

## <a name="next-steps"></a>Дополнительная информация

Сведения о том, как исследовать это хранилище знаний с помощью Обозревателя службы хранилища, см. в следующей статье.

> [!div class="nextstepaction"]
> [Просмотр данных с помощью Обозревателя службы хранилища](knowledge-store-view-storage-explorer.md)

Сведения о создании хранилища знаний с помощью интерфейсов REST API и Postman, см. в следующей статье.  

> [!div class="nextstepaction"]
> [Создание хранилища знаний в REST](knowledge-store-howto.md)
