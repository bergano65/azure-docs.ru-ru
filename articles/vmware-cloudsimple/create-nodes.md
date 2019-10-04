---
title: Предоставление узлов для решения VMware с помощью Клаудсимпле — Azure
description: Узнайте, как добавить узлы в VMWare с помощью развертывания Клаудсимпле
author: dikamath
ms.author: dikamath
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: eb033425d18b472c9da1a2d6a1bb6f166702905e
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828184"
---
# <a name="provision-nodes-for-azure-vmware-solution-by-cloudsimple"></a>Подготавливаете узлы для решения VMware для Azure с помощью Клаудсимпле

Подготавливайте узлы в портал Azure. Затем можно настроить емкость с оплатой по мере использования для среды частного облака Клаудсимпле.

## <a name="sign-in-to-azure"></a>Вход в Azure

Войдите на портал Azure по адресу [https://portal.azure.com](https://portal.azure.com).

## <a name="add-a-node-to-your-cloudsimple-private-cloud"></a>Добавление узла в частное облако Клаудсимпле

1. Выбор пункта **Все службы**.
2. Выполните поиск **узлов клаудсимпле**.

   ![Поиск узлов Клаудсимпле](media/create-cloudsimple-node-search.png)

3. Выберите **узлы клаудсимпле**.
4. Нажмите кнопку **Добавить** , чтобы создать узлы.

    ![Добавление узлов Клаудсимпле](media/create-cloudsimple-node-add.png)

5. Выберите подписку, в которой нужно подготавливать узлы Клаудсимпле.
6. Выберите группу ресурсов для узлов. Чтобы добавить новую группу ресурсов, щелкните **создать**.
7. Введите префикс для обозначения узлов.
8. Выберите расположение ресурсов узла.
9. Выберите выделенное расположение для размещения ресурсов узла.
10. Выберите [тип узла](cloudsimple-node.md).
11. Выберите число узлов для инициализации.
12. Выберите **Review + Create** (Просмотреть и создать).
13. Проверьте параметры. Чтобы изменить параметры, нажмите кнопку **назад**.
14. Нажмите кнопку **Создать**.

## <a name="next-steps"></a>Следующие шаги

* [Создание частного облака](create-private-cloud.md)
