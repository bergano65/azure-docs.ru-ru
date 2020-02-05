---
title: Решения Azure VMware (AVS) — создание службы AVS
description: Описание процесса создания службы AVS в портал Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 468ef8751438812422d7eee83d98acc9e3aedb82
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024829"
---
# <a name="create-the-azure-vmware-solutions-avs-service"></a>Создание службы решений Azure VMware (AVS)

Чтобы приступить к работе с решениями Azure VMware (AVS), создайте службу Azure VMware Solutions (AVS) в портал Azure.

## <a name="before-you-begin"></a>Перед началом работы

Выделите блок/28 CIDR для подсети шлюза. Для каждой службы AVS требуется подсеть шлюза, которая является уникальной для региона, в котором она создана. Подсеть шлюза используется для сетевых служб пограничной сети и требует блока/28 CIDR. Адресное пространство подсети шлюза должно быть уникальным. Она не должна пересекаться с сетью, которая взаимодействует с средой AVS. Сети, взаимодействующие с AVS, включают в себя локальные сети и виртуальные сети Azure.

## <a name="sign-in-to-azure"></a>Войдите в Azure

Войдите на [портал Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Создание службы

1. Выбор пункта **Все службы**.
2. Поиск **служб AVS**.
    ![поиска](media/create-cloudsimple-service-search.png) службы AVS
3. Выберите **службы AVS**.
4. Нажмите кнопку **Добавить** , чтобы создать новую службу.
    ![добавить службу AVS](media/create-cloudsimple-service-add.png)
5. Выберите подписку, в которой вы хотите создать службу AVS.
6. Выберите группу ресурсов для службы. Чтобы добавить новую группу ресурсов, щелкните **создать**.
7. Введите имя для идентификатора службы.
8. Введите CIDR для шлюза службы. Укажите подсеть/28, которая не пересекается с любыми локальными подсетями, подсетями Azure или запланированными подсетями AVS. После создания службы невозможно изменить CIDR.

    ![Создание службы AVS](media/create-cloudsimple-service.png)
9. Нажмите кнопку **ОК**.

Служба будет создана и добавлена в список служб.

## <a name="next-steps"></a>Дальнейшие действия

* Сведения о [подготовке узлов](create-nodes.md)
* Узнайте, как [создать частное облако AVS](create-private-cloud.md)
* Узнайте, как [настроить среду частного облака AVS](quickstart-create-private-cloud.md)
