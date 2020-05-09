---
title: Краткое руководство по созданию пула Apache Spark (предварительной версии)
description: Создайте пул Apache Spark для рабочей области Azure Synapse Analytics, выполнив действия, описанные в этом учебнике.
services: synapse-analytics
author: malvenko
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/15/2020
ms.author: josels
ms.reviewer: jrasnick, carlrab
ms.openlocfilehash: 2501f66c42360d3ec1626f5f4ee42e0212f1967b
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792247"
---
# <a name="quickstart-create-a-new-apache-spark-pool-preview"></a>Краткое руководство. Создание пула Apache Spark (предварительной версии)

Synapse Analytics предлагает различные механизмы аналитики, упрощающие прием, преобразование, моделирование, анализ и обслуживание данных. Пул Apache Spark предоставляет возможности для вычислений больших данных с открытым кодом. После создания пула Apache Spark в рабочей области Synapse данные можно загружать, моделировать, обрабатывать и обслуживать для получения аналитических сведений.

В этом кратком руководстве вы узнаете, как использовать портал Azure для создания пула Apache Spark в рабочей области Synapse.

> [!IMPORTANT]
> Экземпляры Spark оплачиваются пропорционально за каждую минуту, независимо от их использования. Не забудьте завершить работу экземпляра Spark после его использования или задайте короткое время ожидания. Дополнительные сведения см. в разделе **Очистка ресурсов** этой статьи.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure — [создайте бесплатную учетную запись](https://azure.microsoft.com/free/).
- [Рабочая область Synapse Analytics](quickstart-create-workspace.md).

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портал Azure](https://portal.azure.com/)

## <a name="create-new-apache-spark-pool"></a>Создание пула Apache Spark

1. В рабочей области Synapse, в которой необходимо создать пул Apache Spark, щелкните вкладку **New Apache Spark pool** (Новый пул Apache Spark).
![Обзор рабочей области Synapse с красным прямоугольником вокруг команды создания пула Apache Spark](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-01.png)
2. Введите следующие сведения на вкладке **Основные сведения**:

    |Параметр | Рекомендуемое значение | Описание |
    | :------ | :-------------- | :---------- |
    | **Имя пула Apache Spark** | Допустимое имя пула | Это имя, которое будет у пула Apache Spark. |
    | **Размер узла** | "Small (4 vCPU / 32 GB)" (Малый (4 виртуальных ЦП / 32 ГБ)) | Задайте минимальный размер, чтобы сократить затраты по работе в рамках этого краткого руководства. |
    | **Автомасштабирование** | Активировано | Оставьте для этого параметра значение по умолчанию. |
    | **Количество узлов** | 3–40 | Оставьте для этого параметра значение по умолчанию. |
    ||||

    ![Поток создания пула Apache Spark: вкладка "Основные сведения"](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-02.png)
    > [!IMPORTANT]
    > Обратите внимание, что для имен, которые могут использовать пулы Apache Spark, существуют определенные ограничения. Имя должно содержать только буквы и цифры, его длина не должна превышать 15 символов. Имя должно начинаться с буквы, быть уникальными в рабочей области, но не должно содержать зарезервированные слова.

3. Нажмите кнопку **Далее: дополнительные параметры** и проверьте параметры по умолчанию. Не изменяйте параметры по умолчанию.
![Поток создания пула Apache Spark: вкладка "Дополнительные параметры"](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03.png)

4. Нажмите кнопку **Далее: теги**. Не добавляйте теги.
![Поток создания пула Apache Spark: вкладка "Дополнительные параметры"](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-03-tags.png)

5. Щелкните **Review + create** (Просмотреть и создать).

6. Убедитесь, что подробные сведения на основе ранее введенных данных указаны правильно, и нажмите кнопку **Создать**.
![Поток создания пула Apache Spark: вкладка просмотра параметров](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-05.png)

7. На этом этапе запустится поток подготовки ресурсов. Его завершение будет указано. ![Поток создания Apache Spark: подготовка ресурсов](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-06.png)

8. После завершения подготовки в рабочей области будет показана новая запись для созданного пула Apache Spark.
 ![Поток создания пула Apache Spark: подготовка ресурсов](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-07.png)

9. На этом этапе нет работающих ресурсов, плата за Spark не взимается. Вы создали метаданные об экземплярах Spark, которые вам необходимо создать.

## <a name="clean-up-resources"></a>Очистка ресурсов

Выполните действия ниже, чтобы удалить пул Apache Spark из рабочей области.
> [!WARNING]
> При удалении пула Apache Spark аналитический модуль будет удален из рабочей области. Вы больше не сможете подключиться к пулу, а все запросы, конвейеры и записные книжки, использующие этот пул Apache Spark, перестанут работать.

Если вы хотите удалить пул Apache Spark, выполните следующие действия:

1. Перейдите в колонку "Пулы Apache Spark" в рабочей области.
2. Выберите пул Apache для удаления (в этом случае — **contosospark**).
3. Нажмите кнопку **Удалить**
 ![Список пулов Apache Spark с выбранным недавно созданным пулом](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-08.png)
4. Подтвердите удаление и нажмите кнопку **Удалить**.
 ![Диалоговое окно для подтверждения удаления выбранного пула Apache Spark](media/quickstart-create-apache-spark-pool/quickstart-create-spark-pool-10.png)
5. После успешного завершения процесса пул Apache Spark больше не будет указываться в ресурсах рабочей области.

После создания пул SQL становится доступным в рабочей области для загрузки данных, обработки потоков, чтения из озера и т. д.

## <a name="next-steps"></a>Дальнейшие действия

- См. [Краткое руководство. Создание записной книжки Apache Spark](quickstart-apache-spark-notebook.md).
- См. [Краткое руководство. Создание пула SQL Synapse с помощью портала Azure](quickstart-create-sql-pool.md).
