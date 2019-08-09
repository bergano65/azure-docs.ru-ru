---
title: Удаление узлов для решения VMware с помощью Клаудсимпле — Azure
description: Сведения об удалении узлов из VMWare с помощью развертывания Клаудсимпле
author: sharaths-cs
ms.author: dikamath
ms.date: 08/05/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 9dbe1751ac77f370991af75ea161247c21700ff2
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886992"
---
# <a name="delete-nodes-from-vmware-solution-by-cloudsimple---azure"></a>Удаление узлов из решения VMware с помощью Клаудсимпле — Azure

Узлы Клаудсимпле измеряются после их создания.  Чтобы отключить отслеживание узлов, необходимо удалить узлы.  Вы удаляете узлы, которые не используются из портал Azure. 

## <a name="before-you-begin"></a>Перед началом работы

Узел можно удалить только при следующих условиях:

* Частное облако, созданное с помощью узлов, удаляется.  Сведения об удалении частного облака см. в статье [Удаление решения VMware для Azure с помощью клаудсимпле](delete-private-cloud.md).
* Узел был удален из частного облака путем сжатия частного облака.  Сведения о сжатии частного облака см. в статье [Сжатие решения VMware для Azure с помощью Клаудсимпле Private Cloud](shrink-private-cloud.md).


## <a name="sign-in-to-azure"></a>Войдите в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="delete-cloudsimple-node"></a>Удалить узел Клаудсимпле

1. Выбор пункта **Все службы**.

2. Выполните поиск **узлов клаудсимпле**.

   ![Поиск узлов Клаудсимпле](media/create-cloudsimple-node-search.png)

3. Выберите **узлы клаудсимпле**.

4. Выберите узлы, которые не принадлежат частному облаку для удаления.  В столбце **имя частного облака** отображается имя частного облака, к которому принадлежит узел.  Если узел не используется частным облаком, значение будет пустым. 

    ![Выбор узлов Клаудсимпле](media/select-delete-cloudsimple-node.png)

> [!NOTE]
> Удалить можно только узлы, которые не являются частью частного облака.

## <a name="next-steps"></a>Следующие шаги

* Сведения о [частном облаке](cloudsimple-private-cloud.md)
