---
author: gvashishtha
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: include
ms.date: 07/31/2020
ms.author: gopalv
ms.openlocfilehash: 38b346b4296ea72f964717220adf762e8d0d6d91
ms.sourcegitcommit: 8def3249f2c216d7b9d96b154eb096640221b6b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/03/2020
ms.locfileid: "87542815"
---
## <a name="prerequisites"></a>Предварительные требования

- Рабочая область машинного обучения Azure. Дополнительные сведения см. в статье [создание машинное обучение Azure рабочей области](../articles/machine-learning/how-to-manage-workspace.md).
- Модель. Если у вас нет обученной модели, можно использовать модель и файлы зависимостей, предоставленные в [этом руководстве](https://aka.ms/azml-deploy-cloud).
- [Расширение интерфейса командной строки Azure (CLI) для службы машинное обучение](../articles/machine-learning/reference-azure-machine-learning-cli.md)


## <a name="connect-to-your-workspace"></a>Подключение к рабочей области

Следуйте указаниям в документации по Azure CLI для [настройки контекста подписки](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription).

Затем выполните следующие действия.

```azurecli-interactive
az ml workspace list --resource-group=<my resource group>
```

для просмотра рабочих областей, к которым у вас есть доступ.

## <a name="register-your-model"></a><a id="registermodel"></a>Регистрация модели

Зарегистрированная модель — это логический контейнер для одного или нескольких файлов, составляющих эту модель. Например, если имеется модель, которая хранится в нескольких файлах, можно зарегистрировать их как единую модель в рабочей области. После регистрации файлов можно скачать или развернуть зарегистрированную модель и получить все зарегистрированные файлы.

> [!TIP]
> При регистрации модели вы предоставляете путь к облачному расположению (из обучающего запуска) или к локальному каталогу. Этот путь предназначен только для поиска файлов для отправки в ходе процесса регистрации. Он не должен соответствовать пути, используемому в скрипте записи. Дополнительные сведения см. [в разделе Поиск файлов модели в скрипте записи](../articles/machine-learning/how-to-deploy-advanced-entry-script.md#load-registered-models).

Модели машинного обучения регистрируются в рабочей области Машинное обучение Azure. Модель может поступать из Машинное обучение Azure или в другом месте. При регистрации модели можно дополнительно предоставить метаданные о модели. `tags` `properties` Словари и, применяемые к регистрации модели, затем можно использовать для фильтрации моделей.

В следующих примерах показано, как зарегистрировать модель.

### <a name="register-a-model-from-an-azure-ml-training-run"></a>Регистрация модели из запуска обучения Azure ML

```azurecli-interactive
az ml model register -n sklearn_mnist  --asset-path outputs/sklearn_mnist_model.pkl  --experiment-name myexperiment --run-id myrunid --tag area=mnist
```

[!INCLUDE [install extension](machine-learning-service-install-extension.md)]

`--asset-path`Параметр ссылается на расположение в облаке модели. В этом примере используется путь к одному файлу. Чтобы включить несколько файлов в регистрацию модели, задайте `--asset-path` путь к папке, содержащей файлы.

### <a name="register-a-model-from-a-local-file"></a>Регистрация модели из локального файла

```azurecli-interactive
az ml model register -n onnx_mnist -p mnist/model.onnx
```

Чтобы включить несколько файлов в регистрацию модели, задайте `-p` путь к папке, содержащей файлы.

Дополнительные сведения о `az ml model register` см. в [справочной документации](/cli/azure/ext/azure-cli-ml/ml/model).

## <a name="define-an-entry-script"></a>Определение скрипта записи

[!INCLUDE [write entry script](machine-learning-entry-script.md)]

## <a name="define-an-inference-configuration"></a>Определение конфигурации вывода

[!INCLUDE [inference config](machine-learning-service-inference-config.md)]

Следующая команда демонстрирует развертывание модели с помощью интерфейса командной строки.

```azurecli-interactive
az ml model deploy -n myservice -m mymodel:1 --ic inferenceconfig.json
```

В этом примере конфигурация задает следующие параметры.

* , Что для модели требуется Python
* [Скрипт записи](#define-an-entry-script), используемый для обработки веб-запросов, отправляемых в развернутую службу
* Файл Conda, описывающий пакеты Python, необходимые для вывода.

Сведения об использовании пользовательского образа DOCKER с конфигурацией вывода см. в статье [развертывание модели с помощью пользовательского образа DOCKER](../articles/machine-learning/how-to-deploy-custom-docker-image.md).

## <a name="choose-a-compute-target"></a>Выбор целевого объекта вычислений

[!INCLUDE [aml-compute-target-deploy](aml-compute-target-deploy.md)]

## <a name="define-a-deployment-configuration"></a>Определение конфигурации развертывания

Параметры, доступные для конфигурации развертывания, зависят от выбранного целевого объекта вычислений.

[!INCLUDE [aml-local-deploy-config](machine-learning-service-local-deploy-config.md)]

Дополнительные сведения см. в документации по [AZ ml для развертывания модели](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-deploy) .

## <a name="deploy-your-model"></a>Развертывание модели

Теперь вы готовы к развертыванию модели.

### <a name="using-a-registered-model"></a>Использование зарегистрированной модели

Если вы зарегистрировали модель в рабочей области Машинное обучение Azure, замените "MyModel: 1" именем модели и ее номером версии.

```azurecli-interactive
az ml model deploy -m mymodel:1 --ic inferenceconfig.json --dc deploymentconfig.json
```

### <a name="using-a-local-model"></a>Использование локальной модели

Если вы предпочитаете не регистрировать модель, можно передать параметр "sourceDirectory" в inferenceconfig.js, чтобы указать локальный каталог, из которого будет обслуживаться модель.

```azurecli-interactive
az ml model deploy --ic inferenceconfig.json --dc deploymentconfig.json
```
## <a name="delete-resources"></a>Удаление ресурсов

Чтобы удалить развернутую WebService, используйте `az ml service <name of webservice>` .

Чтобы удалить зарегистрированную модель из рабочей области, используйте`az ml model delete <model id>`

Узнайте больше об [удалении WebService](/cli/azure/ext/azure-cli-ml/ml/service#ext-azure-cli-ml-az-ml-service-delete) и [удалении модели](/cli/azure/ext/azure-cli-ml/ml/model#ext-azure-cli-ml-az-ml-model-delete) .