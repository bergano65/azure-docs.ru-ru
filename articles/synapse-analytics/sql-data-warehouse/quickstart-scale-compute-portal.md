---
title: Масштабирование вычислительных ресурсов пула SQL Synapse (портал Azure)
description: Вы можете масштабировать вычислительные ресурсы для пула SQL Synapse (хранилища данных) с помощью портал Azure.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/28/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 96c3c05fea912433731dc7a6a8ab705ab76d3058
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82232229"
---
# <a name="quickstart-scale-compute-for-synapse-sql-pool-with-the-azure-portal"></a>Краткое руководство. Масштабирование вычислительных ресурсов пула SQL Synapse с помощью портала Azure

Вы можете масштабировать вычислительные ресурсы для пула SQL Synapse (хранилища данных) с помощью портал Azure. [Горизонтально увеличивайте масштаб вычислительных ресурсов](sql-data-warehouse-manage-compute-overview.md), чтобы повысить производительность, или уменьшайте их масштаб, чтобы сократить затраты. 

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портал Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Перед началом

Вы можете масштабировать уже существующий пул SQL или создать его с именем **mySampleDataWarehouse**, воспользовавшись кратким руководством по [созданию и подключению на портале](create-data-warehouse-portal.md). В этом кратком руководстве масштабируется хранилище **mySampleDataWarehouse**.

>[!IMPORTANT] 
>Чтобы можно было изменить масштаб пула SQL, он должен быть подключен к сети. 

## <a name="scale-compute"></a>Масштабирование вычислительных ресурсов

Вычислительные ресурсы пула SQL можно масштабировать путем увеличения или уменьшения единиц использования хранилища данных. В [руководстве по созданию и подключению портала](create-data-warehouse-portal.md) создается **mySampleDataWarehouse** и инициализируется с помощью 400 DWU. Ниже описаны шаги по изменению числа единиц DWU для **mySampleDataWarehouse**.

Изменить число единиц использования хранилища данных можно так:

1. На портале Azure в области слева щелкните **Azure Synapse Analytics (ранее — Хранилище данных SQL)** .
2. Выберите **mySampleDataWarehouse** на странице **Azure Synapse Analytics (ранее — Хранилище данных SQL)** . Откроется пул SQL.
3. Щелкните пункт **Масштаб**.

    ![Щелкните пункт Масштаб](./media/quickstart-scale-compute-portal/click-scale.png)

2. В области "Масштаб" передвиньте ползунок влево или вправо, чтобы изменить число единиц DWU. Затем выберите масштаб.

    ![Переместите ползунок](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Дальнейшие действия
Чтобы узнать больше о пуле SQL, перейдите к руководству по [загрузке данных в пул SQL](load-data-from-azure-blob-storage-using-polybase.md). 
