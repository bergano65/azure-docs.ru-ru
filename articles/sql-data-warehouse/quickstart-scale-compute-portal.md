---
title: 'Краткое руководство. Масштабирование вычислительных ресурсов на портале Azure '
description: Масштабирование вычислительных ресурсов в пуле SQL с помощью портала Azure. Масштабируйте вычислительные ресурсы, чтобы повысить производительность, или выполняйте обратное масштабирование, чтобы сократить расходы.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7463849223fdf81466237c7d0c912763988e80e6
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200361"
---
# <a name="quickstart-scale-compute-in-azure-synapse-analytics-sql-pool-in-the-azure-portal"></a>Краткое руководство. Масштабирование вычислительных ресурсов в пуле SQL Azure Synapse Analytics на портале Azure

Масштабирование вычислительных ресурсов в пуле SQL с помощью портала Azure. [Горизонтально масштабируйте вычислительные ресурсы](sql-data-warehouse-manage-compute-overview.md), чтобы повысить производительность, или уменьшайте их масштаб, чтобы сократить затраты. 

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портал Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Перед началом

Вы можете масштабировать уже существующий пул SQL или создать его с именем **mySampleDataWarehouse**, воспользовавшись кратким руководством [Создание хранилища данных SQL Azure на портале Azure и отправка запросов к этому хранилищу данных](create-data-warehouse-portal.md).  В этом кратком руководстве масштабируется хранилище **mySampleDataWarehouse**.

>[!Note]
>Чтобы можно было изменить масштаб пула SQL, он должен быть подключен к сети. 

## <a name="scale-compute"></a>Масштабирование вычислительных ресурсов

Вычислительные ресурсы пула SQL можно масштабировать путем увеличения или уменьшения единиц использования хранилища данных. В статье "Краткое руководство. Создание хранилища данных SQL Azure на портале Azure и отправка запросов к этому хранилищу данных" мы создали хранилище **mySampleDataWarehouse** и инициализировали его со значением 400 DWU. Ниже описаны шаги по изменению числа единиц DWU для **mySampleDataWarehouse**.

Изменить число единиц использования хранилища данных можно так:

1. На портале Azure в области слева щелкните **Azure Synapse Analytics (ранее — Хранилище данных SQL)** .
2. Выберите **mySampleDataWarehouse** на странице **Azure Synapse Analytics (ранее — Хранилище данных SQL)** . Откроется пул SQL.
3. Щелкните пункт **Масштаб**.

    ![Щелкните пункт Масштаб](media/quickstart-scale-compute-portal/click-scale.png)

2. В области "Масштаб" передвиньте ползунок влево или вправо, чтобы изменить число единиц DWU. Затем выберите масштаб.

    ![Переместите ползунок](media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Дальнейшие действия
Вы научились масштабировать вычислительные ресурсы для пула SQL. Чтобы узнать больше о пуле SQL, перейдите к учебнику по загрузке данных.

> [!div class="nextstepaction"]
>[Загрузка данных в пул SQL](load-data-from-azure-blob-storage-using-polybase.md)
