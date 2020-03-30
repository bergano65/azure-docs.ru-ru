---
title: Рекомендации по хранению данных & - Центр безопасности Azure
description: В этом документе рассматриваются рекомендации из центра безопасности Azure, которые помогают защитить ваши данные и службу SQL Azure, а также обеспечить соответствие политикам безопасности.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: bcae6987-05d0-4208-bca8-6a6ce7c9a1e3
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/19/2019
ms.author: memildin
ms.openlocfilehash: 74ed55e1d460495bfa8d3d4c00bd37bb7f05260e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75552871"
---
# <a name="protect-azure-data-and-storage-services"></a>Защита служб данных и хранения данных Azure
Когда Центр безопасности Azure определяет потенциальные уязвимости безопасности, он создает рекомендации, которые помогут вам в процессе настройки необходимых элементов управления для затвердевания и защиты ресурсов.

В этой статье объясняется **страница безопасности данных** в разделе безопасности ресурсов Центра безопасности.

Полный список рекомендаций, которые вы можете увидеть на этой странице, смотрите [рекомендации по данным и хранению.](recommendations-reference.md#recs-datastorage)


## <a name="view-your-data-security-information"></a>Просмотр информации о безопасности данных

1. В разделе **гигиены безопасности ресурсов** нажмите **«Данные» и ресурсы хранения.**

    ![Ресурсы хранения данных &](./media/security-center-monitoring/click-data.png)

    Страница **безопасности данных** открывается рекомендациями для ресурсов данных.

    [![Ресурсы данных](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    На этой странице можно выполнить следующие действия:

    * Нажмите на вкладку **«Обзор»,** в списке всех рекомендаций по ресурсам данных, которые необходимо исделать. 
    * Нажмите на каждую вкладку и просмотрите рекомендации по типу ресурса.

    > [!NOTE]
    > Для получения дополнительной информации [Azure Storage encryption for data at rest](../storage/common/storage-service-encryption.md)о шифровании хранилища см.


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Исправление рекомендации на ресурсе данных

1. С любой из вкладок ресурса щелкните ресурс. Информационная страница открывает список рекомендаций, которые должны быть исправлены.

    ![Информация о ресурсах](./media/security-center-monitoring/sql-recommendations.png)

2. Нажмите на рекомендацию. Страница Рекомендации открывается и отображает **шаги по восстановлению** для выполнения рекомендации.

   ![Шаги восстановления](./media/security-center-monitoring/remediate1.png)

3. Нажмите **Принять меры**. Отображается страница настроек ресурса.

    ![Включить рекомендацию](./media/security-center-monitoring/remediate2.png)

4. Следуйте **шагам восстановления** и нажмите **Сохранить**.


## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать больше о рекомендациях, применимых к другим типам ресурсов Azure, см.

* [Полный справочный список рекомендаций центра безопасности Azure](recommendations-reference.md)
* [Защита приложений и виртуальных машин в центре безопасности Azure](security-center-virtual-machine-protection.md)
* [Защита сети в центре безопасности Azure.](security-center-network-recommendations.md)