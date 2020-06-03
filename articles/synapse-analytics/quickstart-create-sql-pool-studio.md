---
title: Краткое руководство. Создание пула SQL Synapse (предварительная версия) с помощью Synapse Studio
description: Создайте новый пул SQL Synapse с помощью Synapse Studio, выполнив инструкции из этого руководства.
services: synapse-analytics
author: julieMSFT
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 3/19/2020
ms.author: jrasnick
ms.reviewer: jrasnick
ms.openlocfilehash: da034479f110607e878a0ed26b9d487e41438e16
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665334"
---
# <a name="quickstart-create-a-synapse-sql-pool-preview-using-synapse-studio"></a>Краткое руководство. Создание пула SQL Synapse (предварительная версия) с помощью Synapse Studio

Azure Synapse Analytics предлагает различные механизмы аналитики, упрощающие прием, преобразование, моделирование и анализ данных. Пул SQL предусматривает возможности вычисления и хранения на основе T-SQL. После создания пула SQL в рабочей области Synapse данные можно загружать, моделировать, обрабатывать и доставлять для быстрого получения аналитических сведений.

В этом кратком руководстве показано, как создать пул SQL в рабочей области Synapse с помощью Synapse Studio.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись, прежде чем начинать работу](https://azure.microsoft.com/free/).


## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
- [Рабочая область Synapse](quickstart-create-workspace.md)

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портал Azure](https://portal.azure.com/)

## <a name="navigate-to-the-synapse-workspace"></a>Переход в рабочую область Synapse

1. Перейдите к рабочей области Synapse, в которой будет создан пул SQL, введя имя службы (или непосредственно имя ресурса) в строке поиска.
![Панель поиска на портале Azure с указанными рабочими областями Synapse.](media/quickstart-create-sql-pool/create-sql-pool-00a.png)
1. В списке рабочих областей введите имя (или часть имени) рабочей области, которую нужно открыть. В нашем примере будет использоваться рабочая область с именем **contosoanalytics**.
![Список рабочих областей Synapse, где отображаются только области с именем Contoso.](media/quickstart-create-sql-pool/create-sql-pool-00b.png)

## <a name="launch-synapse-studio"></a>Запуск Synapse Studio

1. В сводке по рабочей области щелкните **Запуск Synapse Studio**, чтобы открыть расположение, в котором будет создан пул SQL. В строке поиска введите имя службы или непосредственно ресурса.
![Окно обзора рабочей области Synapse на портале Azure, команда "Запуск Synapse Studio" выделена цветом.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-20.png)

## <a name="create-a-sql-pool-in-synapse-studio"></a>Создание пула SQL в Synapse Studio

1. На домашней странице Synapse Studio перейдите в **центр управления**, щелкнув в области навигации слева значок **Управление**.
![Домашняя страница Synapse Studio, раздел "Управление" выделен цветом.](media/quickstart-create-apache-spark-pool/create-spark-pool-studio-21.png)

1. В Центре управления перейдите к разделу **Пулы SQL**, чтобы просмотреть текущий список пулов SQL, доступных в рабочей области.
![Центр управления Synapse Studio с выбранной навигацией пулов SQL.](media/quickstart-create-sql-pool/create-sql-pool-studio-22.png)

1. Выберите команду **+ Создать**. Отобразится мастер создания нового пула SQL. 
![Список пулов SQL в Центре управления Synapse Studio.](media/quickstart-create-sql-pool/create-sql-pool-studio-23.png)

1. Введите следующие сведения на вкладке **Основные сведения**:

    | Параметр | Рекомендуемое значение | Описание |
    | :------ | :-------------- | :---------- |
    | **Имя пула SQL** | contosoedw | Это имя пула SQL. |
    | **Уровень производительности** | DW100c | Задайте минимальный размер, чтобы сократить затраты по работе в рамках этого краткого руководства. |

    ![Поток создания пулов SQL: вкладка "Основные сведения".](media/quickstart-create-sql-pool/create-sql-pool-studio-24.png)
    > [!IMPORTANT]
    > Обратите внимание, что для имен, которые могут использовать пулы SQL, существуют определенные ограничения. Имена не могут содержать специальные символы, их должно быть не более 15. Имена также не должны содержать зарезервированных слов и должны быть уникальными в рабочей области.

4. На следующей вкладке **Дополнительные параметры** выберите **Нет**, чтобы подготовить пул SQL без данных. Оставьте параметры сортировки по умолчанию.
![Поток создания пула SQL: вкладка "Дополнительные параметры"](media/quickstart-create-sql-pool/create-sql-pool-studio-25.png)

1. Мы не будем добавлять теги сейчас, поэтому выберите **Далее: Отзыв и создание**.

1. На вкладке **Просмотр и создание** убедитесь, что подробные сведения на основе ранее введенных данных указаны правильно, и нажмите кнопку **Создать**. 
![Поток создания пула SQL: вкладка просмотра параметров](media/quickstart-create-sql-pool/create-sql-pool-studio-26.png)

1. На этом этапе запустится поток подготовки ресурсов.

1. После завершения подготовки в рабочей области будет показана новая запись для созданного пула SQL.
 ![Поток создания пула SQL: подготовка ресурсов](media/quickstart-create-sql-pool/create-sql-pool-studio-27.png)

1. Созданный пул SQL станет доступным в рабочей области для загрузки данных, обработки потоков, чтения из озера и т. д.

## <a name="clean-up-sql-pools-using-synapse-studio"></a>Очистка пулов SQL с помощью Synapse Studio    

Чтобы удалить пул SQL из рабочей области с помощью Synapse Studio, сделайте следующее.
> [!WARNING]
> При удалении пула SQL аналитический модуль будет удален из рабочей области. Вы больше не сможете подключиться к пулу, а все запросы, конвейеры и скрипты, использующие этот пул SQL, не будут работать.

Если вы хотите удалить пул SQL, сделайте следующее.

1. Перейдите к разделу "Пулы SQL" в Центре управления Synapse Studio.
1. Щелкните многоточие в удаляемом пуле SQL (в нашем примере **contosoedw**), чтобы отобразить команды для пула SQL. ![Список пулов SQL с недавно созданным пулом.](media/quickstart-create-sql-pool/create-sql-pool-studio-28.png)
1. Нажмите кнопку **Удалить**
1. Подтвердите удаление и нажмите кнопку **Удалить**.
 ![Диалоговое окно для подтверждения удаления выбранного пула SQL.](media/quickstart-create-sql-pool/create-sql-pool-studio-29.png)
1. После успешного завершения процесса пул SQL больше не будет указываться в ресурсах рабочей области.

## <a name="next-steps"></a>Дальнейшие действия 
- См. [Краткое руководство. Создание записной книжки Apache Spark](quickstart-apache-spark-notebook.md).
- См. [Краткое руководство. Создание пула SQL Synapse с помощью портала Azure](quickstart-create-sql-pool-portal.md).