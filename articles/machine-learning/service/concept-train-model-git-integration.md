---
title: Интеграция с Git для Машинное обучение Azure
titleSuffix: Azure Machine Learning
description: Узнайте, как Машинное обучение Azure интегрируется с локальным репозиторием Git. При отправке обучающего запуска из локального каталога, который является репозиторием Git, сведения о репозитории, ветви и текущей фиксации отправляются в ходе выполнения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 10/11/2019
ms.openlocfilehash: c8b2407b18f0d7115ce51fc28b956e7fd764c71e
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/22/2019
ms.locfileid: "72756016"
---
# <a name="git-integration-for-azure-machine-learning"></a>Интеграция с Git для Машинное обучение Azure

[Git](https://git-scm.com/) — это популярная система управления версиями, которая позволяет совместно использовать проекты и совместно работать с ними. При отправке учебного задания на Машинное обучение Azure, если обучающие файлы хранятся в локальном репозитории Git, сведения о репозитории будут прослеживаться в рамках процесса обучения.

Поскольку Машинное обучение Azure отслеживает сведения из локального репозитория Git, он не привязан к какому-либо конкретному центральному репозиторию. Репозиторий можно клонировать из GitHub, GitLab, BitBucket, Azure DevOps или любой другой службы, совместимой с Git.

## <a name="how-does-git-integration-work"></a>Как работает интеграция с Git?

При отправке обучающего запуска из пакета SDK для Python или Машинное обучение CLI файлы, необходимые для обучения модели, отправляются в рабочую область. Если команда `git` доступна в среде разработки, процесс отправки использует его для проверки того, хранятся ли файлы в репозитории Git. Если да, то сведения из репозитория Git также передаются в ходе обучающего запуска. Эти сведения хранятся в следующих свойствах для обучающего запуска:

| Свойство | Команда git, используемая для получения значения | Описание |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | Универсальный код ресурса (URI), из которого был клонирован репозиторий. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | Универсальный код ресурса (URI), из которого был клонирован репозиторий. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Активная ветвь при отправке выполнения. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Активная ветвь при отправке выполнения. |
| `azureml.git.commit` | `git rev-parse HEAD` | Хэш фиксации кода, отправленного для выполнения. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Хэш фиксации кода, отправленного для выполнения. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, если ветвь или фиксация являются грязными; в противном случае `false`. |

Эти сведения отправляются для запусков, использующих оценщик, конвейер машинного обучения или запуск скрипта.

Если файлы обучения не находятся в репозитории Git в среде разработки или команда `git` недоступна, сведения, связанные с Git, не будут отслеживаниться.

## <a name="view-the-logged-information"></a>Просмотр записанных данных

Сведения о git хранятся в свойствах для обучающего запуска. Эти сведения можно просмотреть с помощью портал Azure, пакета SDK для Python и интерфейса командной строки. 

### <a name="azure-portal"></a>портала Azure

1. В [портал Azure](https://portal.azure.com)выберите свою рабочую область.
1. Выберите __эксперименты__и выберите один из экспериментов.
1. Выберите один из запусков из столбца __номер запуска__ .
1. Выберите __журналы__, а затем разверните __журналы__ и записи __azureml__ . Выберите ссылку, которая начинается с __### \_azure__.

    ![Запись # # #_azure на портале](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

Записанные в журнал данные содержат текст, похожий на следующий код JSON:

```json
"properties": {
    "_azureml.ComputeTargetType": "batchai",
    "ContentSnapshotId": "5ca66406-cbac-4d7d-bc95-f5a51dd3e57e",
    "azureml.git.repository_uri": "git@github.com:azure/machinelearningnotebooks",
    "mlflow.source.git.repoURL": "git@github.com:azure/machinelearningnotebooks",
    "azureml.git.branch": "master",
    "mlflow.source.git.branch": "master",
    "azureml.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "mlflow.source.git.commit": "4d2b93784676893f8e346d5f0b9fb894a9cf0742",
    "azureml.git.dirty": "True",
    "AzureML.DerivedImageName": "azureml/azureml_9d3568242c6bfef9631879915768deaf",
    "ProcessInfoFile": "azureml-logs/process_info.json",
    "ProcessStatusFile": "azureml-logs/process_status.json"
}
```

### <a name="python-sdk"></a>Пакет Python SDK

После отправки обучающего запуска возвращается объект [запуска](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) . Атрибут `properties` этого объекта содержит зарегистрированные сведения о git. Например, следующий код получает хэш фиксации:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

Команда CLI `az ml run` может использоваться для получения свойств из запуска. Например, следующая команда возвращает свойства для последнего запуска в эксперименте с именем `train-on-amlcompute`:

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Дополнительные сведения см. в справочной документации по [AZ ML по запуску](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) .

## <a name="next-steps"></a>Дальнейшие действия

* [Настройка и использование целевых объектов вычислений для обучения модели](how-to-set-up-training-targets.md)