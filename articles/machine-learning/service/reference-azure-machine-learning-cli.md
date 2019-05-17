---
title: Расширение интерфейса командной строки для Машинного обучения
titleSuffix: Azure Machine Learning service
description: Узнайте о расширении интерфейса командной строки для службы Машинного обучения Azure. Azure CLI — это кроссплатформенная программа командной строки, которая позволяет работать с ресурсами в облаке Azure. Расширение Машинного обучения позволяет работать со службой "Машинное обучение Azure".
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 05/02/2019
ms.custom: seodec18
ms.openlocfilehash: 163b8e1f68b8d5a102465022c67f7d0da57a7215
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65596959"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Использование расширения интерфейса командной строки для Службы машинного обучения Azure

CLI для Машинного обучения Azure является расширением кроссплатформенного интерфейса командной строки для платформы Azure ([Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)). Это расширение предоставляет команды для работы со службой машинного обучения Azure. Он позволяет автоматизировать машинного обучения действий. В следующем списке приведены некоторые действия, которые можно выполнять с расширением интерфейса командной строки в примере:

+ выполнять эксперименты для создания моделей машинного обучения;

+ регистрировать модели машинного обучения для использования клиентами;

+ упаковывать, развертывать и отслеживать жизненный цикл моделей машинного обучения.

Интерфейс командной строки не является заменой пакета SDK для Машинного обучения Azure. Это средство, оптимизированный для обработки высокой параметризованные задачи, которые сами соответствия для автоматизации.

## <a name="prerequisites"></a>Технические условия

* Для использования интерфейса командной строки необходима подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

* [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="full-reference-docs"></a>Полная справочная документация

Найти [Полная справочная документация по azure-cli-ml расширение Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Установить расширение

Чтобы установить расширение интерфейса командной строки Машинного обучения, выполните следующую команду:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Примеры файлов, которые можно использовать с помощью приведенных ниже команд можно найти [здесь](https://aka.ms/azml-deploy-cloud).

При появлении запроса выберите `y`, чтобы установить расширение.

Чтобы убедиться, что расширение установлено, используйте следующую команду для отображения списка подкоманд Машинного обучения:

```azurecli-interactive
az ml -h
```

## <a name="remove-the-extension"></a>Удаление расширения

Чтобы удалить расширение интерфейса командной строки, используйте следующую команду:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Управление ресурсами

Следующие команды демонстрируют использование интерфейса командной строки для управления ресурсами, используемыми службой "Машинное обучение Azure".

+ Если вы не сделали этого, создайте группу ресурсов:

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Создайте рабочую область Службы машинного обучения Azure.

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    Дополнительные сведения см. в разделе [az рабочей области машинного обучения создайте](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ Присоединение конфигурации рабочей области в папку, чтобы включить отслеживание контекста интерфейса командной строки.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Эта команда создает `.azureml` подкаталог, который содержит файлы примеров runconfig и conda среды. Он также содержит `config.json` файл, который используется для связи с рабочей областью машинного обучения Azure.

    Дополнительные сведения см. в разделе [присоединить az ml папку](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Свяжите контейнер BLOB-объектов Azure в хранилище данных.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Дополнительные сведения см. в разделе [az ml хранилище данных присоединения-BLOB-объектов](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

    
+ Подключите кластер службы контейнеров AZURE в качестве целевого объекта вычислений.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Дополнительные сведения см. в разделе [az ml computetarget присоединить aks](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ Создайте новую цель AMLcompute.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    Дополнительные сведения см. в разделе [az ml computetarget создать amlcompute](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

## <a id="experiments"></a>Запускать эксперименты

* Запустите выполнение эксперимента. При использовании этой команды, укажите имя файла runconfig (текста до \*.runconfig, если вы ищете систему) для параметра - c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > `az ml folder attach` Команда создает `.azureml` подкаталог, который содержит два файла runconfig в примере. 
    >
    > Если у вас есть скрипт Python, который создает объект конфигурации при запуске программными средствами, можно использовать [RunConfig.save()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) сохранить объект как runconfig-файл.
    >
    > Дополнительные примеры runconfig файлов, см. в разделе [ https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml ](https://github.com/MicrosoftDocs/pipelines-azureml/tree/master/.azureml).

    Дополнительные сведения см. в разделе [az ml запуска отправить сценарий](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Просмотрите в списке экспериментов:

    ```azurecli-interactive
    az ml experiment list
    ```

    Дополнительные сведения см. в разделе [az ml поэкспериментировать списка](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="model-registration-profiling-deployment"></a>Регистрация модели, профилирование, развертывание

Следующие команды демонстрируют регистрацию обученной модели, а затем ее развертывание в качестве рабочей службы:

+ Зарегистрируйте модель с помощью Машинного обучения Azure.

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Дополнительные сведения см. в разделе [az ml модели register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **НЕОБЯЗАТЕЛЬНЫЙ** профиля вашей модели, чтобы получить оптимальные значения ЦП и памяти для развертывания.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Дополнительные сведения см. в разделе [профиль модели az ml](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ Развертывание модели в AKS

    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
    ```

    Ниже приведен пример `inferenceconfig.json` документа:

    ```json
    {
    "entryScript": "score.py",
    "runtime": "python",
    "condaFile": "myenv.yml",
    "extraDockerfileSteps": null,
    "sourceDirectory": null,
    "enableGpu": false,
    "baseImage": null,
    "baseImageRegistry": null
    }
    ```

    Дополнительные сведения см. в разделе [развертывание модели машинного обучения az](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).


## <a name="next-steps"></a>Дальнейшие действия

* [Справочник по расширению Machine Learning CLI команду](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [Обучать и развертывать модели машинного обучения с помощью Azure конвейеров](/azure/devops/pipelines/targets/azure-machine-learning)