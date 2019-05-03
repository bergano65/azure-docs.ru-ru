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
ms.openlocfilehash: 9cc6ad4f7b33de4d132efe63ff11c34f10b614af
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65023386"
---
# <a name="use-the-cli-extension-for-azure-machine-learning-service"></a>Использование расширения интерфейса командной строки для Службы машинного обучения Azure

CLI для Машинного обучения Azure является расширением кроссплатформенного интерфейса командной строки для платформы Azure ([Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)). Это расширение предоставляет команды для работы со службой "Машинное обучение Azure" из командной строки. Он позволяет автоматизировать машинного обучения рабочих процессов. Например можно выполнить следующие действия:

+ выполнять эксперименты для создания моделей машинного обучения;

+ регистрировать модели машинного обучения для использования клиентами;

+ упаковывать, развертывать и отслеживать жизненный цикл моделей машинного обучения.

Интерфейс командной строки не является заменой пакета SDK для Машинного обучения Azure. Это средство, оптимизированный для обработки высокой параметризованные задачи, которые сами соответствия для автоматизации.

## <a name="prerequisites"></a>Технические условия

* Для использования интерфейса командной строки необходима подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

* [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

## <a name="install-the-extension"></a>Установка расширения

Чтобы установить расширение интерфейса командной строки Машинного обучения, выполните следующую команду:

```azurecli-interactive
az extension add -n azure-cli-ml
```

> [!TIP]
> Примеры файлов, которые можно использовать с помощью приведенных ниже команд можно найти [здесь](http://aka.ms/azml-deploy-cloud).

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

+ Присоединение конфигурации рабочей области в папку, чтобы включить отслеживание контекста интерфейса командной строки.
    ```azurecli-interactive
    az ml folder attach -w myworkspace -g myresourcegroup
    ```

+ Свяжите контейнер BLOB-объектов Azure в хранилище данных.

    ```azurecli-interactive
    az ml datastore attach-blob  -n datastorename -a accountname -c containername
    ```
    
+ Подключите кластер службы контейнеров AZURE в качестве целевого объекта вычислений.

    ```azurecli-interactive
    az ml computetarget attach aks -n myaks -i myaksresourceid -g myrg -w myworkspace
    ```

## <a id="experiments"></a>Запускать эксперименты

* Запустите выполнение эксперимента. При использовании этой команды, укажите имя файла runconfig (текста до \*.runconfig, если вы ищете систему) для параметра - c.

    ```azurecli-interactive
    az ml run submit-script -c local -e testexperiment train.py
    ```

* Просмотрите в списке экспериментов:

    ```azurecli-interactive
    az ml experiment list
    ```

## <a name="model-registration-profiling--deployment"></a>Модель регистрации, профилирование и развертывания

Следующие команды демонстрируют регистрацию обученной модели, а затем ее развертывание в качестве рабочей службы:

+ Зарегистрируйте модель с помощью Машинного обучения Azure.

  ```azurecli-interactive
  az ml model register -n mymodel -p sklearn_regression_model.pkl
  ```

+ Развертывание модели в AKS

  ```azurecli-interactive
  az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
  ```
