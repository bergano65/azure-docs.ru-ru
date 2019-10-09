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
ms.openlocfilehash: 99194c42dbc6ef07301be517021bf0fb4b4e7c23
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173503"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Создание решения Azure VMware с помощью службы Клаудсимпле

Чтобы приступить к работе с решением VMware для Azure с помощью Клаудсимпле, создайте решение Azure VMware с помощью службы Клаудсимпле в портал Azure.

> [!IMPORTANT]
> Перед созданием службы Клаудсимпле необходимо зарегистрировать поставщик ресурсов Microsoft. Вмвареклаудсимпле в подписке Azure. Выполните действия, описанные в разделе [Включение поставщика ресурсов Microsoft. вмвареклаудсимпле в подписке Azure](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на [портале Azure](https://portal.azure.com).

## <a name="create-the-service"></a>Создание службы

1. Выбор пункта **Все службы**.
2. Выполните поиск по запросу **Клаудсимпле Services**.
    @no__t 0Search Клаудсимпле Service @ no__t-1
3. Выберите **Клаудсимпле Services (службы**).
4. Нажмите кнопку **Добавить** , чтобы создать новую службу.
    @no__t 0Add Клаудсимпле Service @ no__t-1
5. Выберите подписку, в которой вы хотите создать службу Клаудсимпле.
6. Выберите группу ресурсов для службы. Чтобы добавить новую группу ресурсов, щелкните **создать**.
7. Введите имя для идентификатора службы.
8. Введите CIDR для шлюза службы. Укажите подсеть/28, которая не пересекается с любыми локальными подсетями, подсетями Azure или запланированными подсетями Клаудсимпле. После создания службы невозможно изменить CIDR.

    ![Создание службы Клаудсимпле](media/create-cloudsimple-service.png)
9. Нажмите кнопку **ОК**.

Служба будет создана и добавлена в список служб.

## <a name="next-steps"></a>Следующие шаги

* Сведения о [подготовке узлов](create-nodes.md)
* Узнайте, как [создать частное облако](create-private-cloud.md)
* Узнайте, как [настроить среду частного облака](quickstart-create-private-cloud.md)
