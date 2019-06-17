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
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: 7f0806a1d68cd2cede1ae51f0a50a8125c1e7c8b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66016529"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces"></a>Создание рабочих областей службы машинного обучения Azure и управление ими

В этой статье вы выполните создание, просмотр и удаление [**рабочих областей службы машинного обучения Azure**](concept-workspace.md) на портале Azure для [службы "Машинное обучение Azure"](overview-what-is-azure-ml.md).  Можно также создавать и удалять рабочие области, [с помощью интерфейса командной строки](reference-azure-machine-learning-cli.md), [с кодом Python](https://aka.ms/aml-sdk) или [через расширение VS Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning).

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
