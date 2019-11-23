---
title: View a knowledge store (preview) with Storage Explorer
titleSuffix: Azure Cognitive Search
description: Просматривайте и анализируйте хранилище знаний Когнитивного поиска Azure с помощью Обозревателя службы хранилища портала Azure. knowledge store is currently in public preview.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: ca2df05cf20ef51b2d5ca866f22bd9450dd6acaf
ms.sourcegitcommit: dd0304e3a17ab36e02cf9148d5fe22deaac18118
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74406556"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Просмотр хранилища знаний с помощью Обозревателя службы хранилища

> [!IMPORTANT] 
> Сейчас предоставляется общедоступная предварительная версия хранилища знаний. Для предварительной версии функции соглашение об уровне обслуживания не предусмотрено. Мы не рекомендуем использовать ее в рабочей среде. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Версия REST API 2019-05-06-Preview](search-api-preview.md) предоставляет предварительные версии функций. В настоящее время предоставляется ограниченная поддержка портала, но поддержка пакета SDK для .NET пока что отсутствует.

In this article, you'll learn by example how to connect to and explore a knowledge store using Storage Explorer in the Azure portal.

## <a name="prerequisites"></a>Технические условия

+ Follow the steps in [Create a knowledge store in Azure portal](knowledge-store-create-portal.md) or [Create an Azure Cognitive Search knowledge store by using REST](knowledge-store-create-rest.md) to create the sample knowledge store used in this walkthrough.

+ Кроме того, вам потребуется имя учетной записи хранения Azure, которая использовалась для создания хранилища знаний, а также его ключ доступа из портала Azure.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>View, edit, and query a knowledge store in Storage Explorer

1. In the Azure portal, [open the Storage account](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) that you used to create the knowledge store.

1. В левой области навигации учетной записи хранения щелкните **Обозреватель службы хранилища.**

1. Разверните список **Таблицы**, чтобы показать список проекций Azure, которые были созданы при запуске мастера **импорта данных** на примере данных обзоров отелей.

Выберите любую таблицу для просмотра обогащенных данных, включая ключевые фразы оценки тональности, широту и долготу, а также многое другое.

   ![Просмотр таблиц в Обозревателе службы хранилища](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Просмотр таблиц в Обозревателе службы хранилища")

Чтобы изменить тип данных для любого значения таблицы или изменить отдельные значения в таблице, нажмите кнопку **Изменить**. При изменении типа данных для любого столбца в одной строке таблицы, он будет применен ко всем строкам.

   ![Изменение таблицы в Обозревателе службы хранилища](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Изменение таблицы в Обозревателе службы хранилища")

Чтобы выполнить запросы, щелкните **Запрос** на панели команд и введите условия.  

   ![Запрос таблицы в Обозревателе службы хранилища](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Запрос таблицы в Обозревателе службы хранилища")

## <a name="clean-up"></a>Очистка

Если вы работаете в своей подписке, по окончании проекта рекомендуем решить, нужны ли вам созданные ресурсы. Работающие ресурсы могут означать лишние затраты. Можно удалить отдельные ресурсы или удалить группу ресурсов, что позволит удалить весь набор ресурсов.

Просматривать ресурсы и управлять ими можно на портале с помощью ссылок **Все ресурсы** или **Группы ресурсов** на панели навигации слева.

При работе с бесплатной версией службы помните о том, что вам доступно максимум три индекса, индексатора и источника данных. Вы можете удалить отдельные элементы на портале, чтобы не превысить лимит.

## <a name="next-steps"></a>Дальнейшие действия

Connect this knowledge store to Power BI for deeper analysis, or move forward with code, using the REST API and Postman to create a different knowledge store.

> [!div class="nextstepaction"]
> [Connect with Power BI](knowledge-store-connect-power-bi.md)
> [Create a knowledge store in REST](knowledge-store-howto.md)
