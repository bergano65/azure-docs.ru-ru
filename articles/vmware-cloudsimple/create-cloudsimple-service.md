---
title: Решение VMware для Azure от Клаудсимпле — создание службы Клаудсимпле
description: Описание процесса создания службы Клаудсимпле в портал Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 0f589adf5cbcd04b9642dfe1362fd13d5be1f9aa
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2019
ms.locfileid: "69640633"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Создание решения Azure VMware с помощью службы Клаудсимпле

Чтобы приступить к работе с решением VMware для Azure с помощью Клаудсимпле, создайте решение Azure VMware с помощью службы Клаудсимпле в портал Azure.

> [!IMPORTANT]
> Перед созданием службы Клаудсимпле необходимо зарегистрировать поставщик ресурсов Microsoft. Вмвареклаудсимпле в подписке Azure. Выполните действия, описанные в разделе [Включение поставщика ресурсов Microsoft. вмвареклаудсимпле в подписке Azure](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Войдите в Azure

Войдите на [портале Azure](http://portal.azure.com).

## <a name="create-the-service"></a>Создание службы

1. Выбор пункта **Все службы**.
2. Выполните поиск по запросу **Клаудсимпле Services**.
    ![Поиск службы Клаудсимпле](media/create-cloudsimple-service-search.png)
3. Выберите **Клаудсимпле Services (службы**).
4. Нажмите кнопку **Добавить** , чтобы создать новую службу.
    ![Добавление службы Клаудсимпле](media/create-cloudsimple-service-add.png)
5. Выберите подписку, в которой вы хотите создать службу Клаудсимпле.
6. Выберите группу ресурсов для службы. Чтобы добавить новую группу ресурсов, щелкните **создать**.
7. Введите имя для идентификатора службы.
8. Введите CIDR для шлюза службы. Укажите подсеть/28, которая не пересекается с любыми локальными подсетями, подсетями Azure или запланированными подсетями Клаудсимпле. После создания службы невозможно изменить CIDR.

    ![Создание службы Клаудсимпле](media/create-cloudsimple-service.png)
9. Нажмите кнопку **ОК**.

Служба будет создана и добавлена в список служб.

## <a name="next-steps"></a>Следующие шаги

* Узнайте, как [создать частное облако](create-private-cloud.md)
* Узнайте, как [настроить среду частного облака](quickstart-create-private-cloud.md)
