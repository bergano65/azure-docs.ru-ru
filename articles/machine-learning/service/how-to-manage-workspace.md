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
ms.openlocfilehash: e65f739a9641181381205c7255d0472325e8055c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60819576"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Создание рабочих областей службы машинного обучения Azure и управление ими

В этой статье вы выполните создание, просмотр и удаление [**рабочих областей службы машинного обучения Azure**](concept-azure-machine-learning-architecture.md#workspace) на портале Azure для [службы "Машинное обучение Azure"](overview-what-is-azure-ml.md).  Можно также создавать и удалять рабочие области, [используя интерфейс командной строки (CLI)](reference-azure-machine-learning-cli.md) или [код Python](https://aka.ms/aml-sdk).

## <a name="create-a-workspace"></a>Создание рабочей области 

Для создания рабочей области понадобится подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

## <a name="view"></a>Просмотреть рабочую область

1. В верхнем левом углу окна портала выберите **Все службы**. 

1. В **все службы** поле фильтра, тип **машинного обучения службы**.  

1. Выберите **рабочих областей службы машинного обучения**.

   ![Поиск рабочей области службы машинного обучения Azure](media/how-to-manage-workspace/all-services.png)

1. Просмотрите список найденных рабочих областей. Можно выполнять фильтрацию на основе подписки, групп ресурсов и расположений.  

1. Выберите рабочую область, чтобы отобразить ее свойства.
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
