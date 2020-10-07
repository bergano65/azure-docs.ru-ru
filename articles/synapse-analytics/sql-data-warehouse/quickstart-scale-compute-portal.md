---
title: Краткое руководство. Масштабирование вычислительных ресурсов пула SQL Synapse (портал Azure)
description: Вы можете масштабировать вычислительные ресурсы для пула SQL Synapse (хранилища данных) с помощью портал Azure.
services: synapse-analytics
author: Antvgski
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: sql-dw
ms.date: 04/28/2020
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: b8a39220bda788cf6dac0be5151c2cdf5385e342
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91570280"
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

Вычислительные ресурсы пула SQL можно масштабировать путем увеличения или уменьшения единиц использования хранилища данных. В статье [Краткое руководство. Создание пула SQL Synapse и отправка в него запросов, используя портал Azure](create-data-warehouse-portal.md) показано, как создать хранилище **mySampleDataWarehouse** и инициализировать его со значением 400 DWU. Ниже описаны шаги по изменению числа единиц DWU для **mySampleDataWarehouse**.

Изменить число единиц использования хранилища данных можно так:

1. На портале Azure в области слева щелкните **Azure Synapse Analytics (ранее — Хранилище данных SQL)** .
2. Выберите **mySampleDataWarehouse** на странице **Azure Synapse Analytics (ранее — Хранилище данных SQL)** . Откроется пул SQL.
3. Щелкните пункт **Масштаб**.

    ![Щелкните пункт Масштаб](./media/quickstart-scale-compute-portal/click-scale.png)

2. В области "Масштаб" передвиньте ползунок влево или вправо, чтобы изменить число единиц DWU. Затем выберите масштаб.

    ![Переместите ползунок](./media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>Дальнейшие действия
Чтобы узнать больше о пуле SQL, перейдите к руководству по [загрузке данных в пул SQL](load-data-from-azure-blob-storage-using-polybase.md). 
