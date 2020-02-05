---
title: Удаление узлов для решений VMware (AVS) — Azure
description: Сведения об удалении узлов из VMWare с помощью развертывания AVS
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 30d128d6bd2f2e1e2705a7b742f02d11fd947a03
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/05/2020
ms.locfileid: "77024744"
---
# <a name="delete-nodes-from-azure-vmware-solution-by-avs"></a>Удаление узлов из решения VMware для Azure с помощью AVS

Узлы AVS измеряются после их создания. Чтобы отключить отслеживание узлов, необходимо удалить узлы. Вы удаляете узлы, которые не используются из портал Azure.

## <a name="before-you-begin"></a>Перед началом работы

Узел можно удалить только при следующих условиях:

* Будет удалено частное облако AVS, созданное с помощью узлов. Сведения об удалении частного облака AVS см. в статье [Удаление решения VMware для Azure с помощью частного облака](delete-private-cloud.md).
* Узел удален из частного облака AVS путем сжатия частного облака AVS. Сведения о сжатии частного облака AVS см. в статье [Сжатие решения VMware для Azure с помощью частного облака AVS](shrink-private-cloud.md).

## <a name="sign-in-to-azure"></a>Войдите в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-avs-node"></a>Удалить узел AVS

1. Выбор пункта **Все службы**.

2. Поиск **узлов AVS**.

   ![Поиск узлов AVS](media/create-cloudsimple-node-search.png)

3. Выберите **узлы AVS**.

4. Для удаления выберите узлы, которые не принадлежат частному облаку AVS. В столбце **имя частного облака AVS** отображается имя частного облака AVS, к которому принадлежит узел. Если узел не используется частным облаком AVS, значение будет пустым. 

    ![Выбор узлов AVS](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Удалить можно только узлы, которые не входят в частное облако AVS.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о [частном облаке AVS](cloudsimple-private-cloud.md)
