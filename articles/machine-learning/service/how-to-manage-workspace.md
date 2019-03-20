---
title: Создание рабочих областей и управление ими
titleSuffix: Azure Machine Learning service
description: Узнайте, как создавать, просматривать и удалять рабочие области службы машинного обучения Azure на портале Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 09/24/2018
ms.custom: seodec18
ms.openlocfilehash: c7dd2fba852881680b43ceabafec2ac6b2751df4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57839599"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Создание рабочих областей службы машинного обучения Azure и управление ими

В этой статье вы выполните создание, просмотр и удаление [**рабочих областей службы машинного обучения Azure**](concept-azure-machine-learning-architecture.md#workspace) на портале Azure для [службы "Машинное обучение Azure"](overview-what-is-azure-ml.md).  Можно также создавать и удалять рабочие области, [используя интерфейс командной строки (CLI)](reference-azure-machine-learning-cli.md) или [код Python](https://aka.ms/aml-sdk).

## <a name="create-a-workspace"></a>Создание рабочей области 

Для создания рабочей области понадобится подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view-a-workspace"></a>Просмотр рабочей области

1. В верхнем левом углу окна портала выберите **Все службы**. 

1. В поле фильтра **Все службы** введите **Рабочая область службы машинного обучения**.  

   ![поиск рабочей области службы машинного обучения Azure](media/how-to-manage-workspace/allservices-search1.png)

1. В результатах фильтра выберите **Рабочая область службы машинного обучения** для отображения списка рабочих областей. 

   ![Вывод списка рабочих областей Службы машинного обучения Azure](media/how-to-manage-workspace/allservices-search.PNG)

1. Просмотрите список найденных рабочих областей. Можно выполнять фильтрацию на основе подписки, групп ресурсов и расположений.  

   ![Просмотр рабочих областей](media/how-to-manage-workspace/allservices_view_workspace.PNG)

1. Выберите только что созданную рабочую область для отображения ее свойств.

   ![Свойства рабочей области](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Удаление рабочей области

Используйте кнопку "Удалить" в верхней части рабочей области, которую нужно удалить.

  ![Кнопка "Удалить"](media/how-to-manage-workspace/delete-workspace.png)


## <a name="clean-up-resources"></a>Очистка ресурсов 

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Дальнейшие действия

Воспользуйтесь полным руководством, чтобы научиться применять рабочую область для создания, обучения и развертывания моделей, используя службу "Машинное обучение Azure".

> [!div class="nextstepaction"]
> [Руководство. Обучение моделей](tutorial-train-models-with-aml.md)
