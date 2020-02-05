---
title: Создание пулов ресурсов с помощью сопоставления подписок Azure
description: В этой статье описывается создание пулов ресурсов для частного облака AVS с помощью сопоставления подписок Azure.
titleSuffix: Azure VMware Solution by CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab7549650e4e20d27d3ad11a96d77ba943797f88
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77014969"
---
# <a name="create-resource-pools-for-your-avs-private-cloud-with-azure-subscription-mapping"></a>Создание пулов ресурсов для частного облака AVS с помощью сопоставления подписок Azure
Сопоставление подписок Azure позволяет создавать пулы ресурсов для частного облака AVS из доступных пулов ресурсов vSphere. На портале AVS вы можете просматривать подписку Azure для частных облаков AVS и управлять ею.

> [!NOTE]
> Сопоставление пула ресурсов также сопоставляет все дочерние пулы ресурсов. Родительский пул ресурсов не может быть сопоставлен, если уже сопоставлены все дочерние пулы ресурсов.

1. [Доступ к порталу AVS](access-cloudsimple-portal.md).
2. Откройте страницу **ресурсы** и выберите **сопоставление подписок Azure**.  
3. Щелкните **изменить сопоставление подписок Azure**.  
4. Чтобы отобразить доступные пулы ресурсов, выберите их слева и щелкните стрелку вправо. 
5. Чтобы удалить сопоставления, выберите их справа и щелкните стрелку влево. 

    ![Подписки Azure](media/resources-azure-mapping.png)

6. Нажмите кнопку **ОК**.
