---
title: Подключение к хранилищу знаний (предварительная версия) с помощью Power BI
titleSuffix: Azure Cognitive Search
description: Подключение к хранилищу знаний (предварительная версия) службы "Когнитивный поиск Azure" с помощью Power BI для анализа и исследования.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/04/2019
ms.openlocfilehash: d1e836e0f463d1d2ce2b71d689ed590239cfb607
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406584"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Подключение хранилища знаний с помощью Power BI

> [!IMPORTANT] 
> Сейчас предоставляется общедоступная предварительная версия хранилища знаний. Для предварительной версии функции соглашение об уровне обслуживания не предусмотрено. Мы не рекомендуем использовать ее в рабочей среде. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Версия REST API 2019-05-06-Preview](search-api-preview.md) предоставляет предварительные версии функций. Сейчас доступна ограниченная поддержка портала. Поддержка пакета SDK для .NET пока не реализована.

В этой статье объясняется, как подключить и изучить хранилище знаний с помощью Power Query в приложении Power BI Desktop. Вы можете быстрее приступить к работе с помощью шаблонов или создать пользовательскую панель мониторинга с нуля.

+ Выполните инструкции по [созданию хранилища знаний на портале Azure](knowledge-store-create-portal.md) или [созданию хранилища знаний службы "Когнитивный поиск Azure" с помощью REST](knowledge-store-create-rest.md), чтобы создать пример хранилища знаний для работы с этим пошаговым руководством. Кроме того, вам потребуется имя учетной записи хранения Azure, которая использовалась для создания хранилища знаний, а также его ключ доступа из портала Azure.

+ [Установка Power BI Desktop](https://powerbi.microsoft.com/downloads/)

## <a name="sample-power-bi-template---azure-portal-only"></a>Пример шаблона Power BI — только портал Azure

Если вы [создали хранилище знаний с помощью портала Azure](knowledge-store-create-portal.md), можно просмотреть визуализации Power BI и поэкспериментировать с ними с использованием [примера шаблона Power BI для Когнитивного поиска Azure](https://github.com/Azure-Samples/cognitive-search-templates). Этот шаблон также доступен для загрузки на одном из шагов мастера **импорта данных**.

Пример шаблона обеспечит автоматическую настройку, описанную в оставшейся части этой статьи. Если для создания хранилища знаний вы использовали REST API, пропустите работу с шаблоном и переходите к дальнейшим разделам статьи, чтобы подключить хранилище знаний к Power BI. Начните со статьи о [подключении с помощью Power BI](#connect-with-power-bi).

Этот пример шаблона включает несколько визуализаций, например WordCloud и Network Navigator В некоторых визуализациях в шаблоне, такие как Map Locations и Entity-Graph Viewer, не отображаются данные из примера хранилища знаний, созданного рамках статьи о [создании хранилища знаний на портале Azure](knowledge-store-create-portal.md). Это связано с тем, что в мастере **импорта данных** использовался не полный набор доступных обогащений с помощью ИИ.

![Пример шаблона Power BI для Когнитивного поиска Azure](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Пример шаблона Power BI")

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
