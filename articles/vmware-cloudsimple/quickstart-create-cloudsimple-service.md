---
title: 'Быстрый запуск: Создайте сервис VMware CloudSimple'
titleSuffix: Azure VMware Solution by CloudSimple
description: Узнайте, как создать сервис CloudSimple, приобрести узлы и зарезервировать узлы
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8ca8c5cacd2b1a1a7b4f70615831d2901510045e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024438"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>Быстрый запуск - Создайте решение Azure VMware с помощью сервиса CloudSimple

Чтобы начать работу, создайте решение Azure VMware по cloudSimple на портале Azure.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>Решение VMware от CloudSimple - обзор сервиса

Сервис CloudSimple позволяет использовать решение Azure VMware от CloudSimple.  Создание сервиса позволяет предоставлять узлы, резервировать узлы и создавать частные облака.  Вы добавляете службу CloudSimple в каждом регионе Azure, где доступна служба CloudSimple.  Служба определяет край сети решений Azure VMware по CloudSimple.  Эта краевая сеть используется для служб, включающих VPN, ExpressRoute и подключение к Интернету в личных облаках.

Чтобы добавить службу CloudSimple, необходимо создать подсеть шлюза. Подсеть шлюза используется при создании краевой сети и требует блока CIDR /28. Пространство адреса шлюза должно быть уникальным. Он не может перекрываться ни с одной из ваших внутренних сетевых адресных пространств или пространством адресов виртуальной сети Azure.

## <a name="before-you-begin"></a>Перед началом

Выделите блок CIDR /28 для подсети шлюза.  Подсеть шлюза необходима для службы CloudSimple и уникальна для региона, в котором она создана. Подсеть шлюза используется для решения Azure VMware службами сети CloudSimple и требует блокCIDR /28. Пространство адреса шлюза должно быть уникальным. Он не должен пересекаться с любой сетью, которая общается с средой CloudSimple.  Сети, взаимодействуют с CloudSimple, включают в себя прикрытые сети и виртуальные сети Azure.

Обзор [сетевых предпосылок](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Вход в Azure

Войти на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Создание службы

1. Выберите **все услуги**.
2. Поиск **службы CloudSimple**.

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

## <a name="provision-nodes"></a>Подготовка узлов

Для настройки возможностей с оплатой по мере вхаддля среды облачного облака Cloud первые узлы обеспечения на портале Azure.

1. Выберите **все услуги**.
2. Поиск **облачных простые узлы**.

    ![Поиск облачных простые узлы](media/create-cloudsimple-node-search.png)

3. Выберите **облачные простыни**.
4. Нажмите **Добавить** для создания узлов.

    ![Добавить облачные простыни](media/create-cloudsimple-node-add.png)

5. Выберите подписку, где требуется предоставить узлы CloudSimple.
6. Выберите группу ресурсов для узлов. Чтобы добавить новую группу ресурсов, нажмите **Создать новый**.
7. Введите префикс для идентификации узлов.
8. Выберите место для ресурсов узлов.
9. Выберите выделенное место для размещения ресурсов узла.
10. Выберите [тип узла.](cloudsimple-node.md)
11. Выберите количество узлов для обеспечения.
12. Выберите **Обзор и Создайте**.
13. Просмотрите настройки. Чтобы изменить любые настройки, нажмите **Предыдущий**.
14. Выберите **Создать**.

## <a name="next-steps"></a>Дальнейшие действия

* [Создание частного облака и настройка среды](quickstart-create-private-cloud.md)
* Подробнее о [сервисе CloudSimple](https://docs.azure.cloudsimple.com/cloudsimple-service)
