---
title: Краткое руководство. Создание рабочей области Synapse
description: Создайте рабочую область Synapse, выполнив действия, описанные в этом руководстве.
services: synapse-analytics
author: pimorano
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: pimorano
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: e5896d1eabaf43afcbab7513d7abb0b1de4efe51
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/10/2020
ms.locfileid: "84656688"
---
# <a name="quickstart-create-a-synapse-workspace"></a>Краткое руководство. Создание рабочей области Synapse

В этом кратком руководстве описаны действия по созданию рабочей области Azure Synapse с помощью портала Azure.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись, прежде чем начинать работу](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Предварительные требования

- [Учетная запись хранения Azure Data Lake Storage 2-го поколения](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портал Azure](https://portal.azure.com/)

## <a name="create-an-azure-synapse-workspace-using-the-azure-portal"></a>Создание рабочей области Azure Synapse с помощью портала Azure

1. В области поиска Microsoft Azure введите **Рабочая область Synapse**, а затем выберите эту службу.
![Панель поиска на портале Azure с введенными рабочими областями Azure Synapse](media/quickstart-create-synapse-workspace/workspace-search.png)
2. На странице **Рабочие области Synapse** щелкните **+ Добавить**.
![Выделенная команда для создания рабочей области Azure Synapse](media/quickstart-create-synapse-workspace/create-workspace-02.png)
3. Заполните форму **Рабочая область Azure Synapse**, указав следующую информацию:

    | Параметр | Рекомендуемое значение | Описание |
    | :------ | :-------------- | :---------- |
    | **Подписка** | *Ваша подписка* | Дополнительные сведения о подписках см. [здесь](https://account.windowsazure.com/Subscriptions). |
    | **Группа ресурсов** | *Любая группа ресурсов* | Допустимые имена групп ресурсов см. в статье о [правилах и ограничениях именования](/azure/architecture/best-practices/resource-naming?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json&view=azure-sqldw-latest). |
    | **Имя рабочей области** | mysampleworkspace | Указывает имя рабочей области, которое также будет использоваться для конечных точек подключения.|
    | **Регион** | восточная часть США 2 | Указывает расположение рабочей области.|
    | **Data Lake Storage 2-го поколения** | Учетная запись —`storage account name`. </br> Файловая система —`root file system to use`. | Указывает имя учетной записи хранения ADLS 2-го поколения для использования в качестве основного хранилища, а также используемую файловую систему.|
    ||||

    ![Поток подготовки рабочей области: вкладка "Основные сведения"](media/quickstart-create-synapse-workspace/create-workspace-03.png)

    Учетная запись хранения можно указать такими способами:
    - выбрать в списке учетных записей ADLS 2-го поколения, доступных в вашей подписке;
    - ввести, используя имя учетной записи.

    > [!IMPORTANT]
    > В рабочей области Azure Synapse должна быть возможность считывать данные в выбранной учетной записи ADLS 2-го поколения и выполнять запись в нее. Кроме того, для любой учетной записи хранения, связываемой в качестве основной учетной записи хранения, необходимо включить **иерархическое пространство имен** при создании учетной записи хранения.
    >
    > Под полями выбора ADLS 2-го поколения есть примечание о том, что управляемому удостоверению рабочей области будет назначена роль **Участник для данных BLOB-объектов хранилища** в выбранной файловой системе Data Lake Storage 2-го поколения и предоставлен полный доступ.

4. (Необязательно) Измените параметры по умолчанию на вкладке **Security + networking** (Безопасность и сеть).
5. (Необязательно) Добавьте теги на вкладке **Теги**.
6. На вкладке **Сводка** будут выполнены необходимые проверки возможности создания рабочей области. После завершения проверки нажмите кнопку **Создать**. ![Процедура подготовки рабочей области: вкладка подтверждения](media/quickstart-create-synapse-workspace/create-workspace-05.png)
7. После успешного завершения подготовки ресурсов вы увидите запись для созданной рабочей области в списке рабочих областей Synapse. ![Список рабочих областей Synapse с новой подготовленной рабочей областью](media/quickstart-create-synapse-workspace/create-workspace-07.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Выполните действия ниже, чтобы удалить рабочую область Azure Synapse.
> [!WARNING]
> Удаление рабочей области Azure Synapse приведет к удалению модулей аналитики и данных, хранящихся в базе данных автономных пулов SQL и метаданных рабочей области. В этом случае невозможно будет подключиться к конечным точкам SQL и Apache Spark. Все артефакты кода будут удалены (запросы, записные книжки, определения заданий и конвейеры).
>
> Удаление рабочей области **не** влияет на данные в Azure Data Lake Store 2-го поколения, связанные с рабочей областью.

Если вы хотите удалить рабочую область Azure Synapse, выполните следующие действия:

1. Перейдите к рабочей области Azure Synapse, которую нужно удалить.
1. На панели команд щелкните **Удалить**.
 ![Обзор рабочей области Azure Synapse с выделенной командой удаления](media/quickstart-create-synapse-workspace/create-workspace-10.png)
1. Подтвердите удаление и нажмите кнопку **Удалить**.
 ![Обзор рабочей области Azure Synapse: диалоговое окно подтверждения удаления рабочей области](media/quickstart-create-synapse-workspace/create-workspace-11.png)
1. После успешного завершения процесса рабочая область Azure Synapse больше не будет отображаться в списке рабочих областей.

## <a name="next-steps"></a>Дальнейшие действия

Теперь можно создать пулы [SQL](quickstart-create-sql-pool-studio.md) или [Apache Spark](quickstart-create-apache-spark-pool-studio.md), чтобы начать анализ и изучение данных.
