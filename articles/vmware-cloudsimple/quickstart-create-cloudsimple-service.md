---
title: Решение Azure VMware, CloudSimple краткое руководство. Создание службы
description: Узнайте, как создать службу CloudSimple Подготовка узлов и резерв узлы
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5732ea726bdecc10d0757224870ee5d8be83a2b2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164222"
---
# <a name="quickstart---create-service"></a>Краткое руководство. Создание службы

Чтобы приступить к работе, создайте решению Azure VMware с помощью CloudSimple на портале Azure.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>Решение VMware, CloudSimple - Общие сведения о службе

Служба CloudSimple позволяет использовать решения Azure VMware с CloudSimple.  Создание службы позволяет подготовить узлы резерв узлы и создание частных облаков.  Добавьте службу CloudSimple в каждом регионе Azure, где доступна служба CloudSimple.  Служба определяет сети периметра Azure VMware решения по CloudSimple.  Эта сеть edge используется для служб, которые предоставляют возможность подключения VPN, ExpressRoute и Интернета для ваших частных облаков.

Чтобы добавить службу CloudSimple, необходимо создать подсеть шлюза. Подсеть шлюза используется при создании сети периметра и требует/28 блок CIDR. Адресное пространство подсети шлюза должно быть уникальным. Он не может перекрывать адресное пространство локальной сети или адресное пространство виртуальной сети Azure.

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>Включить поставщик ресурсов Microsoft.VMwareCloudSimple

Выполните следующие действия, чтобы включить поставщик ресурсов для службы CloudSimple.

1. Выбор пункта **Все службы**.
2. Найдите и выберите **подписок**.

    ![Выбор пункта "Подписки"](media/cloudsimple-service-select-subscriptions.png)

3. Выберите подписку, на котором вы хотите включить службу CloudSimple
4. Щелкните **поставщиков ресурсов** для подписки
5. Используйте **Microsoft.VMwareCloudSimple** для фильтрации поставщика ресурсов
6. Выберите **Microsoft.VMwareCloudSimple** поставщика ресурсов и щелкните **регистрации**

    ![Регистрация поставщика ресурсов](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>Создание службы

1. Выбор пункта **Все службы**.
2. Поиск **CloudSimple службы**.

    ![Служба поиска CloudSimple](media/create-cloudsimple-service-search.png)

3. Выберите **CloudSimple служб**.
4. Нажмите кнопку **добавить** для создания новой службы.

    ![Добавление службы CloudSimple](media/create-cloudsimple-service-add.png)

5. Выберите подписку, где вы хотите создать службу CloudSimple.
6. Выберите группу ресурсов для службы. Чтобы добавить новую группу ресурсов, щелкните **Create New**.
7. Введите имя для идентификации службы.
8. Введите CIDR для службы шлюза. Укажите/28 подсети, которая не пересекается с другими в локальной подсети, подсетей Azure или планового CloudSimple подсетей. CIDR невозможно изменить после создания службы.

    ![Создание службы CloudSimple](media/create-cloudsimple-service.png)

9. Последовательно выберите **ОК**.

Служба создается и добавляется в список служб.

## <a name="provision-nodes"></a>Подготовка узлов

Настройте оплаты как то перейдите емкость для среды CloudSimple частного облака, необходимо сначала подготовить узлы на портале Azure.

1. Выбор пункта **Все службы**.
2. Поиск **CloudSimple узлы**.

    ![Узлы CloudSimple поиска](media/create-cloudsimple-node-search.png)

3. Выберите **CloudSimple узлы**.
4. Нажмите кнопку **добавить** для создания узлов.

    ![Добавление узлов CloudSimple](media/create-cloudsimple-node-add.png)

5. Выберите подписку, которой вы хотите подготовить CloudSimple узлов.
6. Выберите группу ресурсов для узлов. Чтобы добавить новую группу ресурсов, щелкните **Create New**.
7. Введите префикс для идентификации узлов.
8. Выберите расположение для ресурсов узла.
9. Выберите расположение, выделенный для размещения ресурсов узла.
10. Выберите тип узла. Вы можете выбрать [CS28 или CS36](cloudsimple-node.md). Последний вариант включает в себя Максимальная емкость вычислительных ресурсов и памяти.
11. Выберите количество узлов для подготовки.
12. Выберите **Review + Create** (Просмотреть и создать).
13. Проверьте параметры. Чтобы изменить любые параметры, нажмите кнопку **Назад**.
14. Нажмите кнопку **Создать**.

## <a name="next-steps"></a>Дальнейшие действия

* [Создание частного облака и настройка среды](quickstart-create-private-cloud.md)
* Дополнительные сведения о [CloudSimple службы](https://docs.azure.cloudsimple.com/cloudsimple-service)
