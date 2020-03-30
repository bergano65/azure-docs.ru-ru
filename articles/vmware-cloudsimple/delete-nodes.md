---
title: Удаление узлов для решения VMware по Облачным Простым - Azure
description: Узнайте, как удалить узлы из VMWare с помощью развертывания CloudSimple
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 122e0636f54e066ae86ed2d19cefe5863b026293
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77024744"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-cloudsimple"></a>Удаление узлов из решения Azure VMware от CloudSimple

Узлы CloudSimple измеряются после их создания.  Узлы должны быть удалены, чтобы остановить измерение узлов.  Удаляются узлы, которые не используются с портала Azure.

## <a name="before-you-begin"></a>Перед началом

Узла можно удалить только при следующих условиях:

* Частное облако, созданное с помощью узлов, удаляется.  Чтобы удалить приватное облако, [см.](delete-private-cloud.md)
* Узла удалили из приватного облака, ужав приватное облако.  Чтобы уменьшить частное облако, см. [Решение ShrinkUre VMware от CloudSimple Private Cloud.](shrink-private-cloud.md)

## <a name="sign-in-to-azure"></a>Вход в Azure

Войти на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-cloudsimple-node"></a>Удаление узла CloudSimple

1. Выберите **все услуги**.

2. Поиск **облачных простые узлы**.

   ![Поиск облачных простые узлы](media/create-cloudsimple-node-search.png)

3. Выберите **облачные простыни**.

4. Выберите узлы, которые не принадлежат частному облаку для удаления.  **Колонка PRIVATE CLOUD NAME** показывает имя частного облака, к которому принадлежит узла.  Если узел не используется приватным облаком, значение будет пустым. 

    ![Выберите облачные простыни](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Удаляны могут быть удалены только узлы, не входят в частное облако.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте о [частном облаке](cloudsimple-private-cloud.md)
