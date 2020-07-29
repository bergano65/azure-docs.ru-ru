---
title: Рекомендации по хранилищу данных & — центр безопасности Azure
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "75552871"
---
# <a name="protect-azure-data-and-storage-services"></a>Защита данных и служб хранилища Azure
Когда центр безопасности Azure выявляет потенциальные уязвимости системы безопасности, он создает рекомендации по настройке необходимых элементов управления для защиты ресурсов.

В этой статье описывается **Страница «Безопасность данных** » в разделе «Безопасность ресурсов» центра безопасности.

Полный список рекомендаций, которые могут отображаться на этой странице, см. в разделе [рекомендации по данным и хранению](recommendations-reference.md#recs-datastorage).


## <a name="view-your-data-security-information"></a>Просмотр сведений о безопасности данных

1. В разделе **безопасность ресурсов санации** щелкните **ресурсы данных и хранилища**.

    ![Ресурсы хранилища & данных](./media/security-center-monitoring/click-data.png)

    Откроется страница **безопасность данных** с рекомендациями по ресурсам данных.

    [![Ресурсы данных](./media/security-center-monitoring/sql-overview.png)](./media/security-center-monitoring/sql-overview.png#lightbox)

    На этой странице можно выполнить следующие действия:

    * Перейдите на вкладку **Обзор** , в которой перечислены все рекомендации по ресурсам данных для исправления. 
    * Щелкните каждую вкладку и просмотрите рекомендации по типу ресурса.

    > [!NOTE]
    > Дополнительные сведения о шифровании хранилища см. [в статье шифрование службы хранилища Azure для неактивных данных](../storage/common/storage-service-encryption.md).


## <a name="remediate-a-recommendation-on-a-data-resource"></a>Исправление рекомендации для ресурса данных

1. На любой из вкладок ресурсов щелкните ресурс. Откроется страница сведений с перечнем рекомендаций для исправления.

    ![Сведения о ресурсах](./media/security-center-monitoring/sql-recommendations.png)

2. Щелкните рекомендацию. Откроется страница рекомендации с **инструкциями по исправлению** , чтобы реализовать рекомендацию.

   ![Действия по исправлению](./media/security-center-monitoring/remediate1.png)

3. Щелкните **выполнить действие**. Откроется страница «Параметры ресурсов».

    ![Включить рекомендацию](./media/security-center-monitoring/remediate2.png)

4. Выполните **действия по исправлению** и нажмите кнопку **сохранить**.


## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о рекомендациях, относящихся к другим типам ресурсов Azure, см. в следующих разделах:

* [Полный список ссылок на рекомендации по безопасности центра безопасности Azure](recommendations-reference.md)
* [Защита виртуальных машин в центре безопасности Azure](security-center-virtual-machine-protection.md)
* [Защита сети в центре безопасности Azure.](security-center-network-recommendations.md)