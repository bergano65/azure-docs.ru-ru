---
title: Создание функции, активируемой с помощью Azure Cosmos DB | Документация Майкрософт
description: Используйте службу "Функции Azure" для создания бессерверной функции, которая вызывается при добавлении данных в базу данных в Azure Cosmos DB.
services: azure-functions
documentationcenter: na
author: ggailey777
manager: jeconnoc
ms.assetid: bc497d71-75e7-47b1-babd-a060a664adca
ms.service: azure-functions; cosmos-db
ms.devlang: multiple
ms.topic: quickstart
ms.date: 10/02/2018
ms.author: glenga
ms.custom: cc996988-fb4f-47
ms.openlocfilehash: f0173940656581c8bf6b66b0d2a4744e1c751f82
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52968294"
---
# <a name="create-a-function-triggered-by-azure-cosmos-db"></a>Создание функции, активируемой с помощью Azure Cosmos DB

Узнайте, как создать функцию, которая активируется при добавлении или изменении данных в Azure Cosmos DB. Дополнительные сведения о службе Azure Cosmos DB см. в статье [ Обработка данных бессерверных баз данных с помощью Azure Cosmos DB и Функций Azure](../cosmos-db/serverless-computing-database.md).

![Просмотр сообщения в журналах](./media/functions-create-cosmos-db-triggered-function/quickstart-completed.png)

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством:

+ Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

> [!NOTE]
> [!INCLUDE [SQL API support only](../../includes/functions-cosmosdb-sqlapi-note.md)]

## <a name="create-an-azure-cosmos-db-account"></a>создание учетной записи Azure Cosmos DB;

Перед созданием триггера необходимо получить учетную запись Azure Cosmos DB с API SQL.

[!INCLUDE [cosmos-db-create-dbaccount](../../includes/cosmos-db-create-dbaccount.md)]

## <a name="create-an-azure-function-app"></a>Создание приложения-функции Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Затем создайте функцию в новом приложении-функции.

<a name="create-function"></a>

## <a name="create-azure-cosmos-db-trigger"></a>Создание триггера Azure Cosmos DB

1. Разверните приложение-функцию и нажмите кнопку **+** рядом с элементом **Функции**. Если это первая функция в приложении-функции, последовательно выберите **На портале** и **Продолжить**. Если нет, перейдите к шагу 3.

   ![Страница быстрого начала работы с функциями на портале Azure](./media/functions-create-cosmos-db-triggered-function/function-app-quickstart-choose-portal.png)

1. Последовательно выберите **Дополнительные шаблоны** и **Finish and view templates** (Закончить и просмотреть шаблоны).

    ![Быстрый запуск функций: просмотр дополнительных шаблонов](./media/functions-create-cosmos-db-triggered-function/add-first-function.png)

1. В поле поиска введите `cosmos` и выберите шаблон **триггера Azure Cosmos DB**.

1. Если появится соответствующий запрос, щелкните **Установить**, чтобы установить в приложение-функцию расширение Службы хранилища Azure и другие необходимые зависимости. По завершении установки щелкните **Продолжить**.

    ![Установка расширений привязки](./media/functions-create-cosmos-db-triggered-function/functions-create-cosmos-db-trigger-portal.png)

1. Настройте новый триггер с параметрами, как указано в таблице под изображением.

    ![Создание функции, активируемой с помощью Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-settings.png)

    | Параметр      | Рекомендуемое значение  | Описание                                |
    | ------------ | ---------------- | ------------------------------------------ |
    | **Имя** | значение по умолчанию | Используйте имя функции по умолчанию, предложенное шаблоном.|
    | **Подключение учетной записи Azure Cosmos DB** | Новый параметр | Щелкните **Создать**, выберите **подписку** и созданную ранее **учетную запись базы данных**, а затем нажмите кнопку **Выбрать**. Будет создан параметр приложения для подключения к учетной записи. Этот параметр используется в привязке для подключения к базе данных. |
    | **Имя коллекции** | Items | Имя коллекции для отслеживания. |
    | **Create lease collection if it doesn't exist** (Создать коллекцию аренд, если она не существует) | Флажок установлен | Коллекция не существует, создайте ее. |
    | **Database name** (Имя базы данных) | Задачи | Имя базы данных с коллекцией для отслеживания. |

1. Нажмите кнопку **Создать**, чтобы создать функцию, активируемую с помощью Azure Cosmos DB. После создания функции отображается код функции на основе шаблона.  

    ![Шаблон функции Cosmos DB на C#](./media/functions-create-cosmos-db-triggered-function/function-cosmosdb-template.png)

    Этот шаблон функции записывает в журналы количество документов и идентификатор первого документа.

Затем подключитесь к учетной записи Azure Cosmos DB и создайте в базе данных `Tasks` коллекцию `Items`.

## <a name="create-the-items-collection"></a>Создание коллекции элементов

1. Откройте второй экземпляр [портала Azure](https://portal.azure.com) на новой вкладке в браузере.

1. В левой части портала разверните панель значков, введите `cosmos` в поле поиска и выберите **Azure Cosmos DB**.

    ![Поиск службы Azure Cosmos DB](./media/functions-create-cosmos-db-triggered-function/functions-search-cosmos-db.png)

1. Выберите учетную запись Azure Cosmos DB, а затем выберите **обозреватель данных**. 

1. В разделе **Коллекции** выберите **taskDatabase** и **Новая коллекция**.

    ![Создание коллекции](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection.png)

1. На странице **Добавить коллекцию** используйте параметры, перечисленные в таблице под изображением. 

    ![Определение taskCollection](./media/functions-create-cosmos-db-triggered-function/cosmosdb-create-collection2.png)

    | Параметр|Рекомендуемое значение|ОПИСАНИЕ |
    | ---|---|--- |
    | **Идентификатор базы данных** | Задачи |Имя новой базы данных. Должен соответствовать имени, заданному в привязке функции. |
    | **Идентификатор коллекции** | Items | Имя новой коллекции. Должен соответствовать имени, заданному в привязке функции.  |
    | **Емкость хранилища** | Фиксированный (10 ГБ)|Используйте значение по умолчанию. Это значение представляет емкость хранилища базы данных. |
    | **Пропускная способность** |400 ЕЗ| Используйте значение по умолчанию. Чтобы сократить задержку, позже вы можете увеличить масштаб пропускной способности. |
    | **[Ключ секции](../cosmos-db/partition-data.md)** | /category|Ключ секции, который равномерно распределяет данные в каждой секции. Для создания высокопроизводительной коллекции важно выбрать правильный ключ раздела. | 

1. Нажмите кнопку **ОК**, чтобы создать коллекцию элементов. Процесс создания коллекции может занять некоторое время.

После создания коллекции, указанной в привязке функции, можно протестировать функцию, добавив в новую коллекцию документы.

## <a name="test-the-function"></a>Проверка функции

1. Разверните новую коллекцию **taskCollection** в обозревателе данных, выберите **Documents** (Документы), а затем — **Новый документ**.

    ![Создание документа в taskCollection](./media/functions-create-cosmos-db-triggered-function/create-document-in-collection.png)

1. Замените содержимое нового документа следующим содержимым, а затем выберите **Сохранить**.

        {
            "id": "task1",
            "category": "general",
            "description": "some task"
        }

1. На портале переключитесь на первую вкладку браузера, содержащую функцию. Разверните журналы функций и убедитесь, что новый документ активировал функцию. На экране будет видно, что значение идентификатора документа `task1` записано в журналы. 

    ![Просмотр сообщения в журналах](./media/functions-create-cosmos-db-triggered-function/functions-cosmosdb-trigger-view-logs.png)

1. (Необязательно.) Перейдите обратно к документу, внесите изменения и нажмите кнопку **Изменить**. Затем вернитесь к журналам функций и убедитесь, что изменение также активировало функцию.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

Вы создали функцию, которая запускается при добавлении или изменении документов в Azure Cosmos DB.

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]

Дополнительные сведения о триггерах Azure Cosmos DB см. в статье [Привязки Azure Cosmos DB для службы "Функции Azure"](functions-bindings-cosmosdb.md).
