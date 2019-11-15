---
title: 'Краткое руководство. Масштабирование вычислительных ресурсов на портале Azure '
description: Масштабирование вычислительных ресурсов в хранилище данных SQL Azure с помощью портала Azure Масштабируйте вычислительные ресурсы, чтобы повысить производительность, или выполняйте обратное масштабирование, чтобы сократить расходы.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019
ms.openlocfilehash: 59d929165ac9618d68707e2f13741e7bbba7b37f
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685965"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>Краткое руководство. Масштабирование вычислительных ресурсов в хранилище данных SQL Azure с помощью портала Azure

Масштабирование вычислительных ресурсов в хранилище данных SQL Azure с помощью портала Azure [Горизонтально масштабируйте вычислительные ресурсы](sql-data-warehouse-manage-compute-overview.md), чтобы повысить производительность, или уменьшайте их масштаб, чтобы сократить затраты. 

Если у вас еще нет подписки Azure, создайте [бесплатную](https://azure.microsoft.com/free/) учетную запись Azure, прежде чем начинать работу.

## <a name="sign-in-to-the-azure-portal"></a>Вход на портал Azure

Войдите на [портале Azure](https://portal.azure.com/).

## <a name="before-you-begin"></a>Перед началом работы

Вы можете масштабировать уже существующее хранилище данных или создать хранилище данных с именем **mySampleDataWarehouse**, воспользовавшись кратким руководством [Создание хранилища данных SQL Azure на портале Azure и отправка запросов к этому хранилищу данных](create-data-warehouse-portal.md).  В этом кратком руководстве масштабируется хранилище **mySampleDataWarehouse**.

>[!Note]
>Для масштабирования это хранилище данных должно работать. 

## <a name="scale-compute"></a>Масштабирование вычислительных ресурсов

Вычислительные ресурсы хранилища данных SQL можно масштабировать путем увеличения или уменьшения единиц использования хранилища данных. В статье "Краткое руководство. Создание хранилища данных SQL Azure на портале Azure и отправка запросов к этому хранилищу данных" мы создали хранилище **mySampleDataWarehouse** и инициализировали его со значением 400 DWU. Ниже описаны шаги по изменению числа единиц DWU для **mySampleDataWarehouse**.

Изменить число единиц использования хранилища данных можно так:

1. На портале Azure на странице слева щелкните **Хранилища данных SQL**.
2. На странице **Хранилища данных SQL** выберите **mySampleDataWarehouse**. Откроется хранилище данных.
3. Щелкните пункт **Масштаб**.

    ![Щелкните пункт Масштаб](media/quickstart-scale-compute-portal/click-scale.png)

2. В области "Масштаб" передвиньте ползунок влево или вправо, чтобы изменить число единиц DWU.

    ![Переместите ползунок](media/quickstart-scale-compute-portal/scale-dwu.png)

3. Выберите команду **Сохранить**. Появится сообщение с подтверждением. Щелкните **Да** для подтверждения или **Нет** для отмены.

    ![Щелкните Сохранить](media/quickstart-scale-compute-portal/confirm-change.png)



## <a name="next-steps"></a>Дополнительная информация
Вы научились масштабировать вычислительные ресурсы для хранилища данных. Чтобы узнать больше о хранилище данных SQL Azure, перейдите к руководству по загрузке данных.

> [!div class="nextstepaction"]
>[Загрузка данных в Хранилище данных SQL](load-data-from-azure-blob-storage-using-polybase.md)
