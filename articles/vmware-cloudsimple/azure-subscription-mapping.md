---
title: Создание пулов ресурсов с помощью сопоставления подписок Azure
titleSuffix: Azure VMware Solution by CloudSimple
description: Описание создания пулов ресурсов для частного облака с помощью сопоставления подписок Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 474ef03d482288b6bf7b5a8b1c224349a8e2d3a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "77014969"
---
# <a name="create-resource-pools-for-your-private-cloud-with-azure-subscription-mapping"></a>Создание пулов ресурсов для частного облака с помощью сопоставления подписок Azure
Сопоставление подписок Azure позволяет создавать пулы ресурсов для частного облака из доступных пулов ресурсов vSphere. На портале Клаудсимпле вы можете просматривать подписку Azure для частных облаков и управлять ею.

> [!NOTE]
> Сопоставление пула ресурсов также сопоставляет все дочерние пулы ресурсов. Родительский пул ресурсов не может быть сопоставлен, если уже сопоставлены все дочерние пулы ресурсов.

1. [Доступ к порталу клаудсимпле](access-cloudsimple-portal.md).
2. Откройте страницу **ресурсы** и выберите **сопоставление подписок Azure**.  
3. Щелкните **изменить сопоставление подписок Azure**.  
4. Чтобы отобразить доступные пулы ресурсов, выберите их слева и щелкните стрелку вправо. 
5. Чтобы удалить сопоставления, выберите их справа и щелкните стрелку влево. 

    ![Подписки Azure](media/resources-azure-mapping.png)

6. Нажмите кнопку **ОК**.
