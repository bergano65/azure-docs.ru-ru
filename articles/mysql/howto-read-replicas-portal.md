---
title: Управление репликами чтения — портал Azure — база данных Azure для MySQL
description: Узнайте, как настраивать реплики чтения и управлять ими в базе данных Azure для MySQL с помощью портала Azure.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 6/10/2020
ms.openlocfilehash: ad8fd20d744f7aaa113b4c46f8ca0f05a6cc6951
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902843"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Создание реплик чтения и управление ими в базе данных Azure для MySQL с помощью портала Azure

В этой статье описано, как создавать реплики чтения и управлять ими в службе "База данных Azure для MySQL" с помощью портала Azure.

## <a name="prerequisites"></a>Предварительные требования

- [Сервер базы данных Azure для MySQL](quickstart-create-mysql-server-database-using-azure-portal.md), который будет использоваться в качестве главного сервера.

> [!IMPORTANT]
> Функция создания реплики чтения доступна только для серверов базы данных Azure для MySQL в ценовой категории "Общее назначение" или "Оптимизированная для операций в памяти". Убедитесь, что главный сервер находится в одной из этих ценовых категорий.

## <a name="create-a-read-replica"></a>Создание реплики чтения

> [!IMPORTANT]
> При создании реплики для главного сервера, у которого отсутствуют реплики, этот сервер сначала перезапускается для подготовки к репликации. Это необходимо учесть, то есть выполнять такие операции в период низкой нагрузки.

Чтобы создать сервер-реплику чтения, выполните следующие действия.

1. Войдите на [портал Azure](https://portal.azure.com/).

2. Выберите имеющийся сервер базы данных Azure для MySQL, который будет главным сервером. Откроется страница **Обзор**.

3. В меню в разделе **Параметры** выберите **Репликация**.

4. Выберите **Добавить реплику**.

   :::image type="content" source="./media/howto-read-replica-portal/add-replica.png" alt-text="База данных Azure для MySQL — репликация":::

5. Введите имя сервера реплики.

    :::image type="content" source="./media/howto-read-replica-portal/replica-name.png" alt-text="База данных Azure для MySQL — имя реплики":::

6. Укажите расположение сервера реплики. Расположение по умолчанию совпадает с местоположением главного сервера.

    :::image type="content" source="./media/howto-read-replica-portal/replica-location.png" alt-text="База данных Azure для MySQL — расположение реплики":::

   > [!NOTE]
   > Дополнительные сведения о том, в каких регионах можно создать реплику, см. в статье [об основных понятиях реплики чтения](concepts-read-replicas.md). 

7. Нажмите кнопку **ОК**, чтобы подтвердить создание реплики.

> [!NOTE]
> Реплики чтения создаются с той же конфигурацией сервера, что и у главного сервера. Вы можете изменить созданную конфигурацию сервера-реплики. Сервер реплики всегда создается в той же группе ресурсов и в той же подписке, что и главный сервер. Если вы хотите создать сервер реплики для другой группы ресурсов или другой подписки, можно [переместить сервер реплики](https://docs.microsoft.com/azure/azure-resource-manager/management/move-resource-group-and-subscription) после создания. Чтобы сервер-реплика мог работать с главным сервером, рекомендуется, чтобы значения конфигурации сервера-реплики были равны или превосходили значения конфигурации главного сервера.

Созданный сервер-реплику можно просмотреть в колонке **Репликация**.

   :::image type="content" source="./media/howto-read-replica-portal/list-replica.png" alt-text="База данных Azure для MySQL — список реплик":::

## <a name="stop-replication-to-a-replica-server"></a>Остановка репликации на сервер-реплику

> [!IMPORTANT]
> Остановка репликации на сервер является необратимой операцией. Репликацию, остановленную между главным сервером и сервером-репликой, отменить невозможно. Сервер-реплика становится автономным и начинает поддерживает операции чтения и записи. Это сервер нельзя снова преобразовать в реплику.

Чтобы остановить репликацию между главным сервером и сервером-репликой на портале Azure, следуйте инструкциям ниже.

1. На портале Azure выберите нужный главный сервер базы данных Azure для MySQL. 

2. В меню в разделе **Параметры** выберите **Репликация**.

3. Выберите сервер-реплику, для которого нужно остановить репликацию.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-select.png" alt-text="База данных Azure для MySQL — выбор сервера, для которого нужно остановить репликацию":::

4. Щелкните **Остановить репликацию**.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication.png" alt-text="База данных Azure для MySQL — остановка репликации":::

5. Подтвердите остановку репликации, нажав кнопку **ОК**.

   :::image type="content" source="./media/howto-read-replica-portal/stop-replication-confirm.png" alt-text="База данных Azure для MySQL — подтверждение остановки репликации":::

## <a name="delete-a-replica-server"></a>Удаление сервера-реплики

Чтобы удалить сервер-реплику чтения на портале Azure, следуйте инструкциям ниже.

1. На портале Azure выберите нужный главный сервер базы данных Azure для MySQL.

2. В меню в разделе **Параметры** выберите **Репликация**.

3. Выберите сервер-реплику, который нужно удалить.

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-select.png" alt-text="База данных Azure для MySQL — выбор удаляемого сервера-реплики":::

4. Щелкните **Удалить реплику**.

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica.png" alt-text="База данных Azure для MySQL — удаление реплики":::

5. Введите имя реплики и нажмите кнопку **Удалить**, чтобы подтвердить удаление реплики.  

   :::image type="content" source="./media/howto-read-replica-portal/delete-replica-confirm.png" alt-text="База данных Azure для MySQL — подтверждение удаления реплики":::

## <a name="delete-a-master-server"></a>Удаление главного сервера

> [!IMPORTANT]
> Удаление главного сервера приводит к остановке репликации на все серверы-реплики и удалению самого главного сервера. Серверы-реплики становятся автономными серверами, которые начинают поддерживать операции чтения и записи.

Чтобы удалить главный сервер на портале Azure, следуйте инструкциям ниже.

1. На портале Azure выберите нужный главный сервер базы данных Azure для MySQL.

2. На странице **Обзор** выберите **Удалить**.

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-overview.png" alt-text="База данных Azure для MySQL — удаление главного сервера":::

3. Введите имя главного сервера и нажмите кнопку **Удалить**, чтобы подтвердить его удаление  

   :::image type="content" source="./media/howto-read-replica-portal/delete-master-confirm.png" alt-text="База данных Azure для MySQL — удаление главного сервера":::

## <a name="monitor-replication"></a>Мониторинг репликации

1. На [портале Azure](https://portal.azure.com/) выберите сервер-реплику базы данных Azure для MySQL, который нужно отследить.

2. В разделе боковой панели **Мониторинг** выберите **Метрики**.

3. В раскрывающемся списке доступных метрик выберите **Replication lag in seconds** (Задержка репликации в секундах).

   :::image type="content" source="./media/howto-read-replica-portal/monitor-select-replication-lag.png" alt-text="Выбор задержки репликации":::

4. Выберите нужный диапазон времени. На рисунке ниже выбран диапазон в 30 минут.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range.png" alt-text="Выбор диапазона времени":::

5. Просмотрите задержку репликации для выбранного диапазона времени. На рисунке ниже отображаются последние 30 минут.

   :::image type="content" source="./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png" alt-text="Выбор диапазона времени":::

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [репликах чтения](concepts-read-replicas.md)
