---
title: Инициализация узлов для решений VMware (AVS) — Azure
description: Узнайте, как добавлять узлы в VMWare с развертыванием AVS.
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 94ca681153f1e3ccd9ff628b41a9d0e547be5802
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024812"
---
# <a name="provision-nodes-for-azure-vmware-solutions-avs"></a>Инициализация узлов для решений VMware для Azure (AVS)

Подготавливайте узлы в портал Azure. Затем можно настроить емкость с оплатой по мере использования для среды частного облака AVS.

## <a name="sign-in-to-azure"></a>Войдите в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="add-a-node-to-your-avs-private-cloud"></a>Добавление узла в частное облако AVS

1. Выбор пункта **Все службы**.
2. Поиск **узлов AVS**.

   ![Поиск узлов AVS](media/create-cloudsimple-node-search.png)

3. Выберите **узлы AVS**.
4. Нажмите кнопку **Добавить** , чтобы создать узлы.

    ![Добавление узлов AVS](media/create-cloudsimple-node-add.png)

5. Выберите подписку, в которой нужно подготавливать узлы AVS.
6. Выберите группу ресурсов для узлов. Чтобы добавить новую группу ресурсов, щелкните **создать**.
7. Введите префикс для обозначения узлов.
8. Выберите расположение ресурсов узла.
9. Выберите выделенное расположение для размещения ресурсов узла.
10. Выберите [тип узла](cloudsimple-node.md).
11. Выберите число узлов для инициализации.
12. Выберите **Review + Create** (Просмотреть и создать).
13. Проверьте параметры. Чтобы изменить параметры, нажмите кнопку **назад**.
14. Щелкните **Создать**.

## <a name="next-steps"></a>Дальнейшие действия

* [Создание частного облака AVS](create-private-cloud.md)
