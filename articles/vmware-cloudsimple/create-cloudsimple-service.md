---
title: Создание решения Azure VMware с CloudSimple - службы
description: Описывает создание CloudSimple службы на портале Azure
author: sharaths-cs
ms.author: b-shsury
ms.date: 06/04/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: fc08ce48b3b5a4b2cd28cb66e6dd4d9eae3f3885
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66676958"
---
# <a name="create-azure-vmware-solution-by-cloudsimple---service"></a>Создайте решение VMware в Azure с CloudSimple - службы

Чтобы приступить к работе с решением Azure VMware, CloudSimple, создайте решение Azure VMware службой CloudSimple на портале Azure.

> [!NOTE]
> Перед созданием службы CloudSimple, необходимо зарегистрировать поставщик ресурсов Microsoft.VMwareCloudSimple в вашей подписке Azure. Выполните действия, описанные в [включить Microsoft.VMwareCloudSimple поставщика ресурсов в вашей подписке Azure](enable-cloudsimple-service.md).

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Создание службы

1. Выбор пункта **Все службы**.

2. Поиск **CloudSimple служб**.

    ![Служба поиска CloudSimple](media/create-cloudsimple-service-search.png)

3. Выберите **CloudSimple служб**.

4. Нажмите кнопку **добавить** для создания новой службы.

    ![Добавление службы CloudSimple](media/create-cloudsimple-service-add.png)

5. Выберите подписку, где вы хотите создать службу CloudSimple.

6. Выберите группу ресурсов для службы. Чтобы добавить новую группу ресурсов, щелкните **Create New**.

7. Введите имя для идентификации службы.

8. Введите CIDR для службы шлюза. Укажите/28 подсети, которая не пересекается с любым из существующей подсети.  К ним относятся в локальной подсети, Azure, или любой запланированный CloudSimple подсетей. CIDR невозможно изменить после создания службы.

    ![Создание службы CloudSimple](media/create-cloudsimple-service.png)

9. Последовательно выберите **ОК**.

Служба создается и добавляется в список служб.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [Создание частного облака](https://docs.azure.cloudsimple.com/create-private-cloud/)
* Узнайте, как [Настройка среды с частным облаком](quickstart-create-private-cloud.md)
