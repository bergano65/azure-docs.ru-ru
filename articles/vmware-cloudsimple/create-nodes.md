---
title: Подготовка узлов для решения VMware с CloudSimple - Azure
description: Узнайте, как добавлять узлы в VMWare с помощью CloudSimple развертывания
author: dikamath
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 33de07663c91f12d4e10c4661b841cd2dbe5a162
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67165257"
---
# <a name="provision-nodes-for-vmware-solution-by-cloudsimple---azure"></a>Подготовка узлов для решения VMware с CloudSimple - Azure

Подготовка узлов на портале Azure. Затем можно настроить оплаты как то перейдите емкости для вашей среды CloudSimple частного облака.

## <a name="add-a-provisioned-node-to-your-cloudsimple-private-cloud"></a>Добавление узла подготовленного в частном облаке CloudSimple

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

* [Создание частного облака](https://docs.azure.cloudsimple.com/create-private-cloud/)
