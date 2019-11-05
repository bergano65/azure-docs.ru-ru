---
title: Расширение интерфейса командной строки для Машинного обучения
titleSuffix: Azure Machine Learning
description: Узнайте о расширении интерфейса командной строки для службы Машинного обучения Azure. Azure CLI — это кроссплатформенная программа командной строки, которая позволяет работать с ресурсами в облаке Azure. Расширение Машинное обучение позволяет работать с Машинное обучение Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: jmartens
ms.author: jordane
author: jpe316
ms.date: 10/22/2019
ms.custom: seodec18
ms.openlocfilehash: dcbe535bb92f7755980929956093b0f0121ef41b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73476446"
---
# <a name="use-the-cli-extension-for-azure-machine-learning"></a>Использование расширения CLI для Машинное обучение Azure
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

CLI для Машинного обучения Azure является расширением кроссплатформенного интерфейса командной строки для платформы Azure ([Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)). Это расширение предоставляет команды для работы с Машинное обучение Azure. Она позволяет автоматизировать действия машинного обучения. В следующем списке приведены некоторые примеры действий, которые можно выполнить с помощью расширения CLI.

+ выполнять эксперименты для создания моделей машинного обучения;

+ регистрировать модели машинного обучения для использования клиентами;

+ упаковывать, развертывать и отслеживать жизненный цикл моделей машинного обучения.

Интерфейс командной строки не является заменой пакета SDK для Машинного обучения Azure. Это дополнительный инструмент, оптимизированный для обработки строго параметризованных задач, которые прекрасно подходят для автоматизации.

## <a name="prerequisites"></a>Технические условия

* Для использования интерфейса командной строки необходима подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию машинное обучение Azure](https://aka.ms/AMLFree) уже сегодня.

* [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="full-reference-docs"></a>Полные справочные документы

Найдите [полную справочную документацию по расширению Azure-CLI-ml Azure CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Установка расширения

Чтобы установить расширение интерфейса командной строки Машинного обучения, выполните следующую команду:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Примеры файлов, которые можно использовать с приведенными ниже командами, можно найти [здесь](https://aka.ms/azml-deploy-cloud).

При появлении запроса выберите `y`, чтобы установить расширение.

Чтобы убедиться, что расширение установлено, используйте следующую команду для отображения списка подкоманд Машинного обучения:

```azurecli-interactive
az ml -h
```

## <a name="update-the-extension"></a>Обновление расширения

Чтобы обновить расширение CLI Машинное обучение, используйте следующую команду:

```azurecli-interactive
az extension update -n azure-cli-ml
```


## <a name="remove-the-extension"></a>Удаление расширения

Чтобы удалить расширение интерфейса командной строки, используйте следующую команду:

```azurecli-interactive
az extension remove -n azure-cli-ml
```

## <a name="resource-management"></a>Управление ресурсами

Следующие команды демонстрируют использование интерфейса командной строки для управления ресурсами, используемыми службой "Машинное обучение Azure".

+ Создайте группу ресурсов, если она еще не создана.

    ```azurecli-interactive
    az group create -n myresourcegroup -l westus2
    ```

+ Создайте рабочую область Машинное обучение Azure:

    ```azurecli-interactive
    az ml workspace create -w myworkspace -g myresourcegroup
    ```

    Дополнительные сведения см. в разделе о [создании рабочей области AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/workspace?view=azure-cli-latest#ext-azure-cli-ml-az-ml-workspace-create).

+ Подключите конфигурацию рабочей области к папке, чтобы включить отображение контекста CLI.

    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

    Эта команда создает подкаталог `.azureml`, который содержит примеры файлов среды runconfig и conda. Он также содержит файл `config.json`, который используется для взаимодействия с рабочей областью Машинное обучение Azure.

    Дополнительные сведения см. в разделе [AZ ML Folder Attach](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/folder?view=azure-cli-latest#ext-azure-cli-ml-az-ml-folder-attach).

+ Подключите контейнер больших двоичных объектов Azure в качестве хранилища данных.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```

    Дополнительные сведения см. в разделе [AZ ML DataStore Attach-BLOB](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-attach-blob).

+ Передача файлов в хранилище данных.

    ```azurecli-interactive
    az ml datastore upload  -n datastorename -p sourcepath
    ```

    Дополнительные сведения см. в статье [AZ ML DataStore upload](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/datastore?view=azure-cli-latest#ext-azure-cli-ml-az-ml-datastore-upload).

+ Подключите кластер AKS в качестве целевого объекта вычислений.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myresourcegroup -w myworkspace
    ```

    Дополнительные сведения см. в статье [AZ ML computetarget Attach AKS](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/attach?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-attach-aks)

+ Создайте новый целевой объект Амлкомпуте.

    ```azurecli-interactive
    az ml computetarget create amlcompute -n cpu --min-nodes 1 --max-nodes 1 -s STANDARD_D3_V2
    ```

    Дополнительные сведения см. в разделе [AZ ML computetarget Create амлкомпуте](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/computetarget/create?view=azure-cli-latest#ext-azure-cli-ml-az-ml-computetarget-create-amlcompute).

## <a id="experiments"></a>Запуск экспериментов

* Запустите выполнение эксперимента. При использовании этой команды укажите имя файла runconfig (текст перед \*. runconfig, если вы ищете файловую систему) в параметре-c.

    ```azurecli-interactive
    az ml run submit-script -c sklearn -e testexperiment train.py
    ```

    > [!TIP]
    > Команда `az ml folder attach` создает подкаталог `.azureml`, который содержит два примера файлов runconfig. 
    >
    > При наличии скрипта Python, который создает объект конфигурации запуска программно, можно использовать [RunConfig. Save ()](https://docs.microsoft.com/python/api/azureml-core/azureml.core.runconfiguration?view=azure-ml-py#save-path-none--name-none--separate-environment-yaml-false-) , чтобы сохранить его как файл RunConfig.
    >
    > Полную схему runconfig можно найти в этом [JSON файле](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json).

    Дополнительные сведения см. в разделе [AZ ML Run Submit-Script](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-script).

* Просмотреть список экспериментов:

    ```azurecli-interactive
    az ml experiment list
    ```

    Дополнительные сведения см. в статье [AZ ML эксперимент List](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/experiment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-experiment-list).

## <a name="environment-management"></a>Управление средой

Следующие команды демонстрируют создание, регистрацию и перечисление Машинное обучение Azure [окружений](how-to-configure-environment.md) для рабочей области.

+ Создание файлов формирования шаблонов для среды:

    ```azurecli-interactive
    az ml environment scaffold -n myenv -d myenvdirectory
    ```

    Дополнительные сведения см. в статье [AZ ML Environment (формирование шаблонов среды](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-scaffold)).

+ Регистрация среды:

    ```azurecli-interactive
    az ml environment register -d myenvdirectory
    ```

    Дополнительные сведения см. в статье [AZ ML Environment Register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-register).

+ Список зарегистрированных сред:

    ```azurecli-interactive
    az ml environment list
    ```

    Дополнительные сведения см. в статье [AZ ML Environment List](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-list).

+ Скачайте зарегистрированную среду:

    ```azurecli-interactive
    az ml environment download -n myenv -d downloaddirectory
    ```

    Дополнительные сведения см. в статье [AZ ML Environment download](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/environment?view=azure-cli-latest#ext-azure-cli-ml-az-ml-environment-download).

## <a name="ml-pipeline-management"></a>Управление конвейером ML

Следующие команды демонстрируют работу с конвейерами машинного обучения.

+ Создайте конвейер машинного обучения.

    ```azurecli-interactive
    az ml pipeline create -n mypipeline -y mypipeline.yml
    ```

    Дополнительные сведения см. в разделе о [создании конвейера AZ ML](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create).

    Дополнительные сведения о файле конвейера YAML см. [в разделе Определение конвейеров машинного обучения в YAML](reference-pipeline-yaml.md).

+ Запуск конвейера:

    ```azurecli-interactive
    az ml run submit-pipeline -n myexperiment -y mypipeline.yml
    ```

    Дополнительные сведения см. в разделе [AZ ML Run Submit-конвейер](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest#ext-azure-cli-ml-az-ml-run-submit-pipeline).

    Дополнительные сведения о файле конвейера YAML см. [в разделе Определение конвейеров машинного обучения в YAML](reference-pipeline-yaml.md).

+ Запланируйте конвейер:

    ```azurecli-interactive
    az ml pipeline create-schedule -n myschedule -e myexpereiment -i mypipelineid -y myschedule.yml
    ```

    Дополнительные сведения см. в статье [AZ ML Pipeline Create-Schedule](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/pipeline?view=azure-cli-latest#ext-azure-cli-ml-az-ml-pipeline-create-schedule).

    Дополнительные сведения о YAML файле расписания конвейера см. [в разделе Определение конвейеров машинного обучения в YAML](reference-pipeline-yaml.md#schedules).

## <a name="model-registration-profiling-deployment"></a>Регистрация модели, профилирование, развертывание

Следующие команды демонстрируют регистрацию обученной модели, а затем ее развертывание в качестве рабочей службы:

+ Зарегистрируйте модель с помощью Машинного обучения Azure.

    ```azurecli-interactive
    az ml model register -n mymodel -p sklearn_regression_model.pkl
    ```

    Дополнительные сведения см. в статье [AZ ML Model Register](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-register).

+ **Необязательно** Профиле модели, чтобы получить оптимальные значения ЦП и памяти для развертывания.
    ```azurecli-interactive
    az ml model profile -n myprofile -m mymodel:1 --ic inferenceconfig.json -d "{\"data\": [[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}" -t myprofileresult.json
    ```

    Дополнительные сведения см. в статье [AZ ML Model Profile](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-profile).

+ Развертывание модели в AKS
    ```azurecli-interactive
    az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json --ct akscomputetarget
    ```
    
    Дополнительные сведения о схеме файла конфигурации вывода см. в разделе [Схема конфигурации вывода](#inferenceconfig).
    
    Дополнительные сведения о схеме файла конфигурации развертывания см. в разделе [Схема конфигурации развертывания](#deploymentconfig).

    Дополнительные сведения см. в разделе [AZ ML Model Deploy](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/model?view=azure-cli-latest#ext-azure-cli-ml-az-ml-model-deploy).

<a id="inferenceconfig"></a>

## <a name="inference-configuration-schema"></a>Схема конфигурации вывода

[!INCLUDE [inferenceconfig](../../../includes/machine-learning-service-inference-config.md)]

<a id="deploymentconfig"></a>

## <a name="deployment-configuration-schema"></a>Схема конфигурации развертывания

### <a name="local-deployment-configuration-schema"></a>Схема конфигурации локального развертывания

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-local-deploy-config.md)]

### <a name="azure-container-instance-deployment-configuration-schema"></a>Схема конфигурации развертывания экземпляра контейнера Azure 

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aci-deploy-config.md)]

### <a name="azure-kubernetes-service-deployment-configuration-schema"></a>Схема конфигурации развертывания службы Kubernetes Azure

[!INCLUDE [deploymentconfig](../../../includes/machine-learning-service-aks-deploy-config.md)]

## <a name="next-steps"></a>Дальнейшие действия

* [Справочник по командам для машинное обучение расширения CLI](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml?view=azure-cli-latest).

* [Обучение и развертывание моделей машинного обучения с помощью Azure Pipelines](/azure/devops/pipelines/targets/azure-machine-learning)
