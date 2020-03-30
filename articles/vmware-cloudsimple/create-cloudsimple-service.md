---
title: Решение Azure VMware от CloudSimple - Создание сервиса CloudSimple
description: Описывает, как создать сервис CloudSimple на портале Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8648f2c9cc0175050d4b7642f5235d47159ecfaf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024829"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Создание решения Azure VMware с помощью сервиса CloudSimple

Чтобы начать работу с решением Azure VMware от CloudSimple, создайте решение Azure VMware с помощью сервиса CloudSimple на портале Azure.

## <a name="before-you-begin"></a>Перед началом

Выделите блок CIDR /28 для подсети шлюза. Подсеть шлюза необходима для службы CloudSimple и уникальна для региона, в котором она создана. Подсеть шлюза используется для услуг сети краев и требует блока CIDR /28. Пространство адреса шлюза должно быть уникальным. Он не должен пересекаться с любой сетью, которая общается с средой CloudSimple. Сети, взаимодействуют с CloudSimple, включают в себя прикрытые сети и виртуальные сети Azure.

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на [портал Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Создание службы

1. Выберите **все услуги**.
2. Поиск **облачных сервисов CloudSimple**.
    ![Поиск облачных сервисов](media/create-cloudsimple-service-search.png)
3. Выберите **облачные сервисы.**
4. Нажмите **Добавить,** чтобы создать новую службу.
    ![Добавить сервис CloudSimple](media/create-cloudsimple-service-add.png)
5. Выберите подписку, где вы хотите создать сервис CloudSimple.
6. Выберите группу ресурсов для службы. Чтобы добавить новую группу ресурсов, нажмите **Создать новый**.
7. Введите имя для идентификации службы.
8. Введите CIDR для шлюза обслуживания. Укажите подсеть /28, которая не пересекается ни с одной из ваших подсетей, подсетями Azure или запланированными подсетями CloudSimple. Вы не можете изменить CIDR после создания службы.

    ![Создание сервиса CloudSimple](media/create-cloudsimple-service.png)
9. Нажмите кнопку **ОК**.

Услуга создается и добавляется в список услуг.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [обеспечить узлы](create-nodes.md)
* Узнайте, как [создать частное облако](create-private-cloud.md)
* Узнайте, как [настроить среду частного облака](quickstart-create-private-cloud.md)
