---
title: Изменение ключей доступа к учетной записи хранилища
titleSuffix: Azure Machine Learning
description: Узнайте, как изменить ключи доступа к учетной записи хранения Azure, используемой в рабочем пространстве. Azure Machine Learning использует учетную запись хранения данных и моделей Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 03/06/2020
ms.openlocfilehash: f1541c177cea2d223a5e7df576d95fab7eafb310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80296941"
---
# <a name="regenerate-storage-account-access-keys"></a>Регенерация ключей доступа к учетной записи хранилища
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Узнайте, как изменить ключи доступа к учетным записям хранения Azure, используемые в Azure Machine Learning. Azure Machine Learning может использовать учетные записи хранения данных или обученные модели.

В целях безопасности может потребоваться изменить ключи доступа к учетной записи хранилища Azure. При регенерации ключа доступа необходимо обновить машинное обучение Azure, чтобы использовать новый ключ. Azure Machine Learning может использовать учетную запись хранения как для хранения моделей, так и в качестве хранилища данных.

## <a name="prerequisites"></a>Предварительные требования

* Рабочая область машинного обучения Azure. Для получения дополнительной информации смотрите [статью «Создание рабочей области».](how-to-manage-workspace.md)

* [Azure машинного обучения SDK](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* Расширение [Azure Machine Learning CLI.](reference-azure-machine-learning-cli.md)

> [!NOTE]
> Фрагменты кода в этом документе были протестированы с версией 1.0.83 Python SDK.

<a id="whattoupdate"></a> 

## <a name="what-needs-to-be-updated"></a>Что необходимо обновить

Учетные записи хранения могут использоваться в рабочем пространстве Azure Machine Learning (хранение журналов, моделей, моментальных снимков и т.д.) и в качестве хранилища данных. Процесс обновления рабочего пространства является единой командой Azure CLI и может быть выработан после обновления ключа хранения. Процесс обновления хранилищ данных более активно участвует и требует выяснения того, какие хранилища данных в настоящее время используют учетную запись хранения, а затем перерегистрирует их.

> [!IMPORTANT]
> Обновляйте рабочее пространство с помощью Azure CLI и хранилищ данных с помощью Python одновременно. Обновление только одного или другого недостаточно, и может привести к ошибкам до тех пор, пока оба не будут обновлены.

Чтобы обнаружить учетные записи хранения, используемые в хранилищах данных, используйте следующий код:

```python
import azureml.core
from azureml.core import Workspace, Datastore

ws = Workspace.from_config()

default_ds = ws.get_default_datastore()
print("Default datstore: " + default_ds.name + ", storage account name: " +
      default_ds.account_name + ", container name: " + default_ds.container_name)

datastores = ws.datastores
for name, ds in datastores.items():
    if ds.datastore_type == "AzureBlob":
        print("Blob store - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
    if ds.datastore_type == "AzureFile":
        print("File share - datastore name: " + name + ", storage account name: " +
              ds.account_name + ", container name: " + ds.container_name)
```

Этот код ищет все зарегистрированные хранилища данных, которые используют Хранилище Azure, и перечисляет следующую информацию:

* Имя хранилища данных: Название хранилища данных, под которым зарегистрирована учетная запись хранилища.
* Имя учетной записи хранилища: Имя учетной записи хранилища Azure.
* Контейнер: Контейнер в учетной записи хранилища, который используется этой регистрацией.

В нем также указывается, предназначен ли хранилище данных для раздела Azure Blob или a Azure File, поскольку существуют различные методы перерегистрации каждого типа хранилища данных.

Если для учетной записи хранилища существует запись, для просмотра которую вы планируете регенерировать ключи доступа, сохраните имя хранилища данных, имя учетной записи хранилища и имя контейнера.

## <a name="update-the-access-key"></a>Обновление ключа доступа

Для обновления машинного обучения Azure для использования нового ключа используйте следующие действия:

> [!IMPORTANT]
> Выполните все действия, обновляя как рабочее пространство с помощью CLI, так и хранилища данных с помощью Python. Обновление только одного или другого может привести к ошибкам до тех пор, пока оба не будут обновлены.

1. Регенерировать ключ. Для получения информации о регенерации ключа доступа [см.](../storage/common/storage-account-keys-manage.md) Сохраните новый ключ.

1. Для обновления рабочего пространства для использования нового ключа используйте следующие действия:

    1. Войти в подписку Azure, содержащую рабочее пространство, с помощью следующей команды Azure CLI:

        ```azurecli-interactive
        az login
        ```

        [!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

    1. Чтобы обновить рабочее пространство, чтобы использовать новый ключ, используйте следующую команду. Замените `myworkspace` рабочее пространство Azure Machine `myresourcegroup` Learning и замените название группы ресурсов Azure, содержащей рабочее пространство.

        ```azurecli-interactive
        az ml workspace sync-keys -w myworkspace -g myresourcegroup
        ```

        [!INCLUDE [install extension](../../includes/machine-learning-service-install-extension.md)]

        Эта команда автоматически синхронизирует новые ключи для учетной записи хранения Azure, используемой рабочим пространством.

1. Для перерегистрации хранилища данных (ы), используюго учетную запись хранилища, используйте значения из раздела [«Что необходимо обновить»](#whattoupdate) и ключ со ступени 1 со следующим кодом:

    ```python
    # Re-register the blob container
    ds_blob = Datastore.register_azure_blob_container(workspace=ws,
                                              datastore_name='your datastore name',
                                              container_name='your container name',
                                              account_name='your storage account name',
                                              account_key='new storage account key',
                                              overwrite=True)
    # Re-register file shares
    ds_file = Datastore.register_azure_file_share(workspace=ws,
                                          datastore_name='your datastore name',
                                          file_share_name='your container name',
                                          account_name='your storage account name',
                                          account_key='new storage account key',
                                          overwrite=True)
    
    ```

    Поскольку `overwrite=True` этот код записывает существующую регистрацию и обновляет ее для использования нового ключа.

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о [`Datastore`](https://docs.microsoft.com/python/api/azureml-core/azureml.core.datastore(class)?view=azure-ml-py) регистрации хранилинов данных см.
