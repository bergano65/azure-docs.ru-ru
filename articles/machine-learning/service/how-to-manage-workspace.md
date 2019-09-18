---
title: Создание рабочих областей машинного обучения Azure на портале
titleSuffix: Azure Machine Learning
description: Узнайте, как создавать, просматривать и удалять рабочие области Машинное обучение Azure в портал Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: shipatel
author: shivp950
ms.date: 05/10/2019
ms.custom: seodec18
ms.openlocfilehash: 776b873e089c331f2f2867c94e542903075be4f7
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71034511"
---
# <a name="create-and-manage-azure-machine-learning-workspaces-in-the-azure-portal"></a>Создание рабочих областей Машинное обучение Azure и управление ими в портал Azure

В этой статье вы создадите, просмотрите и удалите [**машинное обучение Azure рабочие области**](concept-workspace.md) в портал Azure для [машинное обучение Azure](overview-what-is-azure-ml.md).  Портал — самый простой способ начать работу с рабочими областями, но по мере необходимости изменения или требования к автоматизации можно также создавать и удалять рабочие области [с помощью интерфейса командной строки](reference-azure-machine-learning-cli.md) [с кодом Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) или с [помощью расширения VS Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning).

## <a name="create-a-workspace"></a>Создать рабочую область

Для создания рабочей области понадобится подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию машинное обучение Azure](https://aka.ms/AMLFree) уже сегодня.

[!INCLUDE [aml-create-portal](../../../includes/aml-create-in-portal.md)]

### <a name="download-a-configuration-file"></a>Скачать файл конфигурации

1. Если вы будете создавать [виртуальную машину записной книжки](tutorial-1st-experiment-sdk-setup.md#azure), пропустите этот шаг.

1. Если вы планируете использовать код в локальной среде, которая ссылается на эту рабочую область, выберите **Скачать config.json** в разделе **Обзор** рабочей области.  

   ![Скачивание config.json](./media/how-to-manage-workspace/configure.png)
   
   Поместите файл в структуру каталогов со скриптами Python или приложениями Jupyter Notebook. Он может находиться в том же каталоге, подкаталоге с именем *.azureml* или родительском каталоге. При создании виртуальной машины записной книжки этот файл добавляется в правильный каталог на виртуальной машине.


## <a name="view"></a>Просмотр рабочей области

1. В верхнем левом углу окна портала выберите **Все службы**.

1. В поле фильтра **все службы** введите **Служба машинного обучения**.  

1. Выберите **рабочие области службы машинное обучение**.

   ![Поиск Машинное обучение Azure рабочей области](media/how-to-manage-workspace/all-services.png)

1. Просмотрите список найденных рабочих областей. Можно выполнять фильтрацию на основе подписки, групп ресурсов и расположений.  

1. Выберите рабочую область, чтобы отобразить ее свойства.
   ![Свойства рабочей области](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Удаление рабочей области

Используйте кнопку "Удалить" в верхней части рабочей области, которую нужно удалить.

  ![Кнопка "Удалить"](media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Следующие шаги

Ознакомьтесь с руководством по полной длине, чтобы узнать, как использовать рабочую область для создания, обучения и развертывания моделей с помощью Машинное обучение Azure.

> [!div class="nextstepaction"]
> [Учебник. Обучение моделей](tutorial-train-models-with-aml.md)
