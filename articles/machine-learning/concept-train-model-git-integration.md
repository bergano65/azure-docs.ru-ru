---
title: Интеграция Git для машинного обучения Azure
titleSuffix: Azure Machine Learning
description: Узнайте, как Azure Machine Learning интегрируется с локальным репозиторием Git. При отправке обучающегося запуска из локального каталога, являющегося репозиторием Git, информация о репо, ветке и текущем коммите отслеживается как часть выполнения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: jordane
author: jpe316
ms.date: 03/05/2020
ms.openlocfilehash: 7cc2e346a35cd1cdf1278b527dc451a903d60f89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78402823"
---
# <a name="git-integration-for-azure-machine-learning"></a>Интеграция Git для машинного обучения Azure

[Git](https://git-scm.com/) является популярной системой управления версиями, которая позволяет обмениваться и сотрудничать над своими проектами. 

Azure Machine Learning полностью поддерживает репозитории Git для отслеживания работы - вы можете клонировать репозитории непосредственно в вашей общей файловой системе рабочего пространства, использовать Git на локальной рабочей станции или использовать Git из конвейера CI/CD.

При отправке задания в Azure Machine Learning, если исходные файлы хранятся в локальном репозитории git, то информация о репо отслеживается как часть учебного процесса.

Поскольку Azure Machine Learning отслеживает информацию из локального репо git, она не привязана к какому-либо конкретному центральному репозиторию. Ваш репозиторий может быть клонирован из GitHub, GitLab, Bitbucket, Azure DevOps или любой другой службы, совместимой с git.

## <a name="clone-git-repositories-into-your-workspace-file-system"></a>Репозитории клонирования Git в файловую систему рабочего пространства
Azure Machine Learning предоставляет общую файловую систему для всех пользователей в рабочем пространстве.
Чтобы клонировать репозиторий Git в эту общую кадо, мы рекомендуем создать вычисляемый экземпляр & открыть терминал.
Как только терминал открыт, у вас есть доступ к полному клиенту Git и вы можете клонировать и работать с Git через опыт Git CLI.

Мы рекомендуем клонировать репозиторий в каталог пользователей, чтобы другие не добились коллизий непосредственно в рабочей ветке.

Вы можете клонировать любой репозиторий Git, на который вы можете проверить подлинность (GitHub, Azure Repos, BitBucket и т.д.)

Для руководства о том, как использовать Git CLI, читайте [здесь](https://guides.github.com/introduction/git-handbook/).

## <a name="track-code-that-comes-from-git-repositories"></a>Отслеживайте код, который исходит от репозиториев Git

При отправке обучающего запуска из Python SDK или Machine Learning CLI файлы, необходимые для обучения модели, загружаются в рабочее пространство. Если `git` команда доступна в среде разработки, процесс загрузки использует ее для проверки, хранятся ли файлы в репозитории git. Если это так, то информация из репозитория git также загружается в рамках учебного запуска. Эта информация хранится в следующих свойствах для учебного запуска:

| Свойство | Команда Git используется для получения значения | Описание |
| ----- | ----- | ----- |
| `azureml.git.repository_uri` | `git ls-remote --get-url` | URI, из которого был клонирован ваш репозиторий. |
| `mlflow.source.git.repoURL` | `git ls-remote --get-url` | URI, из которого был клонирован ваш репозиторий. |
| `azureml.git.branch` | `git symbolic-ref --short HEAD` | Активная ветвь при отправке запуска. |
| `mlflow.source.git.branch` | `git symbolic-ref --short HEAD` | Активная ветвь при отправке запуска. |
| `azureml.git.commit` | `git rev-parse HEAD` | Хэш коммита кода, который был отправлен для выполнения. |
| `mlflow.source.git.commit` | `git rev-parse HEAD` | Хэш коммита кода, который был отправлен для выполнения. |
| `azureml.git.dirty` | `git status --porcelain .` | `True`, если ветка/коммит является грязной; в `false`противном случае, . |

Эта информация отправляется для выполнения, в течение которого используется оценщик, конвейер машинного обучения или запуск скрипта.

Если ваши обучающие файлы не находятся в репозитории `git` git в среде разработки или команда недоступна, то информация, связанная с git, не отслеживается.

> [!TIP]
> Чтобы проверить, доступна ли команда git в среде разработки, откройте сеанс оболочки, запрос команды, интерфейс PowerShell или другой интерфейс командной строки и введите следующую команду:
>
> ```
> git --version
> ```
>
> Если установлен, и в пути, вы `git version 2.4.1`получите ответ похож на . Для получения дополнительной информации об установке git в среде разработки, [см.](https://git-scm.com/)

## <a name="view-the-logged-information"></a>Просмотр зарегистрированной информации

Информация git хранится в свойствах для тренировочного запуска. Просмотреть эту информацию можно с помощью портала Azure, Python SDK и CLI. 

### <a name="azure-portal"></a>Портал Azure

1. На [портале Azure](https://portal.azure.com)выберите рабочее пространство.
1. Выберите __Эксперименты,__ а затем выберите один из ваших экспериментов.
1. Выберите один из пробежек из столбца __RUN NUMBER.__
1. Выберите __журналы,__ а затем расширить __журналы__ и __лазурные__ записи. Выберите ссылку, которая начинается с __ ### \_azure.__

    ![Вход #_azure на портале](./media/concept-train-model-git-integration/azure-machine-learning-logs.png)

Зарегистрированная информация содержит текст, похожий на следующий JSON:

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

### <a name="python-sdk"></a>Пакет SDK для Python

После отправки учебного запуска объект [Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run%28class%29?view=azure-ml-py) возвращается. Атрибут `properties` этого объекта содержит зарегистрированную информацию git. Например, следующий код извлекает хэш коммита:

```python
run.properties['azureml.git.commit']
```

### <a name="cli"></a>CLI

Команда `az ml run` CLI может быть использована для извлечения свойств из выполнения. Например, следующая команда возвращает свойства для последнего `train-on-amlcompute`запуска в эксперименте под названием:

```azurecli-interactive
az ml run list -e train-on-amlcompute --last 1 -w myworkspace -g myresourcegroup --query '[].properties'
```

Для получения дополнительной [az ml run](https://docs.microsoft.com/cli/azure/ext/azure-cli-ml/ml/run?view=azure-cli-latest) информации см.

## <a name="next-steps"></a>Дальнейшие действия

* [Настройка и использование вычислительных целей для обучения модели](how-to-set-up-training-targets.md)
