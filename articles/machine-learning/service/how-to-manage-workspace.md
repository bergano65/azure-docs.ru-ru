---
title: Создание рабочих областей машинного обучения Azure на портале
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
ms.openlocfilehash: df015df3c20cdb99512167e3dc3e455da2762c52
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744787"
---
# <a name="create-and-manage-azure-machine-learning-service-workspaces-in-the-azure-portal"></a>Создание рабочих областей служб Машинное обучение Azure и управление ими в портал Azure

В этой статье вы выполните создание, просмотр и удаление [**рабочих областей службы машинного обучения Azure**](concept-workspace.md) на портале Azure для [службы "Машинное обучение Azure"](overview-what-is-azure-ml.md).  Портал — самый простой способ начать работу с рабочими областями, но по мере необходимости изменения или требования к автоматизации можно также создавать и удалять рабочие области [с помощью интерфейса командной строки](reference-azure-machine-learning-cli.md) [с кодом Python](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) или с [помощью расширения VS Code](how-to-vscode-tools.md#get-started-with-azure-machine-learning).

## <a name="create-a-workspace"></a>Создать рабочую область

Для создания рабочей области понадобится подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

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

   ![Поиск рабочей области службы Машинное обучение Azure](media/how-to-manage-workspace/all-services.png)

1. Просмотрите список найденных рабочих областей. Можно выполнять фильтрацию на основе подписки, групп ресурсов и расположений.  

1. Выберите рабочую область, чтобы отобразить ее свойства.
   ![Свойства рабочей области](media/how-to-manage-workspace/allservices_view_workspace_full.PNG)

## <a name="delete-a-workspace"></a>Удаление рабочей области

Используйте кнопку "Удалить" в верхней части рабочей области, которую нужно удалить.

  ![Кнопка "Удалить"](media/how-to-manage-workspace/delete-workspace.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Следующие шаги

Воспользуйтесь полным руководством, чтобы научиться применять рабочую область для создания, обучения и развертывания моделей, используя службу "Машинное обучение Azure".

> [!div class="nextstepaction"]
> [Руководство. Обучение моделей](tutorial-train-models-with-aml.md)
