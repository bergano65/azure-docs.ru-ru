---
title: Изменение базы знаний — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker позволяет управлять содержимым базы знаний, предоставляя возможности удобного редактирования.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: cc4ead968a0ee2c9890c1cd24a6b70516b2b2e74
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326804"
---
# <a name="edit-a-knowledge-base-in-qna-maker"></a>Изменение базы знаний в QnA Maker

QnA Maker позволяет управлять содержимым базы знаний, предоставляя возможности удобного редактирования.

<a name="add-datasource"></a>

## <a name="edit-your-knowledge-base-content"></a>Редактирование содержимого базы знаний

1.  Выберите **My knowledge bases** (Мои базы знаний) на панели навигации вверху. 

    Отобразятся все службы, созданные или предоставленные для совместного использования, в порядке убывания по дате **последнего изменения**.

    ![Раздел My Knowledge Bases (Мои базы знаний)](../media/qnamaker-how-to-edit-kb/my-kbs.png)

1. Выберите определенную базу знаний, чтобы внести в нее изменения.
 
1. Выберите элемент **Параметры**. Здесь вы можете изменить значение в обязательном поле имени службы.
  
    |Цель|Действия|
    |--|--|
    |Добавление URL-адреса|Вы можете добавить новые URL-адреса, чтобы добавить в базу знаний новое содержимое с вопросами и ответами. Для этого последовательно выберите **Manage knowledgebase -> + Add URL** (Управление базой знаний -> + Добавить URL-адрес).|
    |Удаление URL-адреса|Имеющиеся URL-адреса можно удалить, щелкнув корзину или значок "Удалить".|
    |Refresh content|Если вы хотите, чтобы ваша база выполняла сканирование последней версии URL-адресов, установите флажок **Обновление**. This will update the knowledge base with latest URL content once. This is not setting a regular schedule of updates.|
    |Добавление файла|Вы можете добавить поддерживаемый файл документа в базу знаний, выбрав **Manage knowledge base** (Управление базой знаний) и **+ Add File** (+ Добавить файл).|
    |Импорт|Вы также можете импортировать любую имеющуюся базу знаний, нажав кнопку **Import Knowledge base** (Импортировать базу знаний). |
    |Обновить|Обновление базы знаний зависит от **ценовой категории управления**, используемой при создании службы QnA Maker, которая связана с базой знаний. При необходимости можно также обновить ценовую категорию управления на портале Azure.

1. Внеся изменения, выберите **Save and train** (Сохранение и обучение) в правом верхнем углу страницы, чтобы сохранить изменения.    

    ![Сохранение и обучение](../media/qnamaker-how-to-edit-kb/save-and-train.png)

    >[!CAUTION]
    >Если вы покинете страницу, прежде чем выбрать **Save and train** (Сохранение и обучение), все изменения будут потеряны.

## <a name="add-a-qna-pair"></a>Добавление пары QnA

On the **EDIT** page, select **Add QnA pair** to add a new row to the knowledge base table.

![Добавление пары QnA](../media/qnamaker-how-to-edit-kb/add-qnapair.png)

## <a name="delete-a-qna-pair"></a>Удаление пары QnA

Чтобы удалить пару QnA, щелкните значок **Удалить** справа в конце строки QnA. Это необратимая операция. Эту операцию невозможно отменить. Рассмотрите возможность экспорта вашей базы знаний со страницы **Публикация** перед удалением пары. 

![Удаление пары QnA](../media/qnamaker-how-to-edit-kb/delete-qnapair.png)

## <a name="add-alternate-questions"></a>Добавление альтернативных вопросов

Добавьте альтернативные вопросы к существующей паре QnA, чтобы повысить вероятность совпадения с запросом пользователя.

![Добавление альтернативных вопросов](../media/qnamaker-how-to-edit-kb/add-alternate-question.png)

## <a name="add-metadata"></a>Добавление метаданных

Add metadata pairs by first selecting **View options**, then selecting **Show metadata**. This displays the metadata column. Next, select the **+** sign to add a metadata pair. This pair consists of one key and one value.

![Добавление метаданных](../media/qnamaker-how-to-edit-kb/add-metadata.png)

> [!TIP]
> Не забывайте периодически сохранять и обучать базу знаний после внесения правок, чтобы сохранить изменения.

## <a name="manage-large-knowledge-bases"></a>Управление большими базами знаний

* **Data source groups**: The QnAs are grouped by the data source from which they were extracted. Источник данных можно развернуть или свернуть.

    ![Используйте панель источников данных QnA Maker, чтобы сворачивать и разворачивать вопросы и ответы источника данных.](../media/qnamaker-how-to-edit-kb/data-source-grouping.png)

* **Search knowledge base**: You can search the knowledge base by typing in the text box at the top of the Knowledge Base table. Щелкните клавишу ВВОД для поиска по вопросу, ответу или содержимому метаданных. Щелкните значок X, чтобы удалить фильтр поиска.

    ![Используйте поле поиска QnA Maker над вопросами и ответами для перехода к просмотру только элементов, соответствующих фильтру.](../media/qnamaker-how-to-edit-kb/search-paginate-group.png)

* **Pagination**: Quickly move through data sources to manage large knowledge bases

    ![Используйте функции разбиения на страницы QnA Maker над вопросами и ответами для перемещения по страницам вопросов и ответов.](../media/qnamaker-how-to-edit-kb/pagination.png)

## <a name="delete-knowledge-bases"></a>Удаление базы знаний

Удаление базы знаний является необратимой операцией. Эту операцию невозможно отменить. Прежде чем удалить базу знаний, экспортируйте ее на странице **Settings** (Параметры) портала QnA Maker. 

Если вы предоставили [участникам совместной работы](collaborate-knowledge-base.md) общий доступ к базе знаний, а затем удалили ее, все участники утратят доступ к этой базе. 

## <a name="delete-azure-resources"></a>Удаление ресурсов Azure 

Если удалить любой из ресурсов Azure, используемых для ваших баз знаний QnA Maker, базы знаний перестанут работать. Перед удалением любых ресурсов на странице **Настройки** убедитесь, что вы экспортируете свои базы знаний. 

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Collaborate on a knowledge base](./collaborate-knowledge-base.md) (Совместная работа над базой знаний)
