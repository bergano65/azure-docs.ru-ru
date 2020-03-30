---
title: Поезд и развертывание моделей из CLI
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать расширение машинного обучения для Azure CLI для обучения, регистрации и развертывания модели из командной строки.
ms.author: larryfr
author: Blackmist
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.date: 03/26/2020
ms.openlocfilehash: 401ce2aed2c783169592f0dc664a3a7baea415b6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336625"
---
# <a name="tutorial-train-and-deploy-a-model-from-the-cli"></a>Учебник: Подготовка и развертывание модели из CLI
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этом учебнике используется расширение машинного обучения для Azure CLI для обучения, регистрации и развертывания модели.

В обучающие скрипты Python в этом учебнике используют [scikit-learn](https://scikit-learn.org/) для обучения базовой модели. Основное внимание в этом учебнике уделяется не сценариям или модели, а процессу использования CLI для работы с Azure Machine Learning.

Узнайте, как выполнять следующие действия:

> [!div class="checklist"]
> * Установка расширения машинного обучения
> * Создание рабочей области машинного обучения Azure
> * Создание вычислительного ресурса, используемого для обучения модели
> * Определение и регистрация набора данных, используемого для обучения модели
> * Начало тренировочного забега
> * Регистрация и загрузка модели
> * Развертывание модели в качестве веб-службы.
> * Оценка данных с помощью веб-службы

## <a name="prerequisites"></a>Предварительные требования

* Подписка Azure. Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree) уже сегодня.

* Для использования команд CLI в этом документе из **локальной среды**необходим [Azure CLI.](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)

    Если вы используете [облачную оболочку Azure,](https://azure.microsoft.com//features/cloud-shell/)то доступ к CLI осуществляется через браузер и живет в облаке.

## <a name="download-the-example-project"></a>Скачать пример проекта

Для этого учебника, скачать [https://github.com/microsoft/MLOps](https://github.com/microsoft/MLOps) проект. Файлы в `examples/cli-train-deploy` каталоге используются шагами в этом учебнике.

Чтобы получить локальную копию файлов, [загрузите архив .zip](https://github.com/microsoft/MLOps/archive/master.zip)или используйте следующую команду Git для клонирования репозитория:

```azurecli-interactive
git clone https://github.com/microsoft/MLOps.git
```

### <a name="training-files"></a>Учебные файлы

Каталог `examples/cli-train-deploy` проекта содержит следующие файлы, которые используются при обучении модели:

* `.azureml\mnist.runconfig`: Файл __конфигурации выполнения.__ Этот файл определяет среду выполнения, необходимую для обучения модели. В этом примере он также устанавливает данные, используемые для обучения модели в учебной среде.
* `scripts\train.py`: Сценарий обучения. Этот файл тренирует модель.
* `scripts\utils.py`: Файл помощника, используемый скриптом обучения.
* `.azureml\conda_dependencies.yml`: Определяет программные зависимости, необходимые для выполнения сценария обучения.
* `dataset.json`Определение набора данных. Используется для регистрации набора данных MNIST в рабочем пространстве Машинного Обучения Azure.

### <a name="deployment-files"></a>Файлы развертывания

Репозиторий содержит следующие файлы, которые используются для развертывания подготовленной модели в качестве веб-службы:

* `aciDeploymentConfig.yml`: Файл __конфигурации развертывания.__ Этот файл определяет среду хостинга, необходимую для модели.
* `inferenceConfig.json`: Файл __конфигурации выводов.__ Этот файл определяет среду программного обеспечения, используемую службой для оценки данных с помощью модели.
* `score.py`: Скрипт python, который принимает входящие данные, оценивает их с помощью модели, а затем возвращает ответ.
* `scoring-env.yml`: Зависимости conda, необходимые `score.py` для запуска модели и скрипта.
* `testdata.json`: Файл данных, который может быть использован для тестирования развернутого веб-сервиса.

## <a name="connect-to-your-azure-subscription"></a>Подключение к подписке Azure

Существует несколько способов проверки подлинности подписки Azure с CLI. Наиболее основным является интерактивная аутентификативная проверка с помощью браузера. Чтобы проверить подлинность в интерактивном режиме, откройте командную строку или терминал и используйте следующую команду:

```azurecli-interactive
az login
```

Если CLI сможет запустить браузер по умолчанию, он откроет в браузере страницу входа. В противном случае вам нужно открыть браузер и следовать инструкциям по командной строке. Инструкции включают просмотр [https://aka.ms/devicelogin](https://aka.ms/devicelogin) и ввод кода авторизации.

[!INCLUDE [select-subscription](../../includes/machine-learning-cli-subscription.md)]

## <a name="install-the-machine-learning-extension"></a>Установка расширения машинного обучения

Для установки расширения машинного обучения используйте следующую команду:

```azurecli-interactive
az extension add -n azure-cli-ml
```

Если вы получаете сообщение о том, что расширение уже установлено, используйте следующую команду для обновления до последней версии:

```azurecli-interactive
az extension update -n azure-cli-ml
```

## <a name="create-a-resource-group"></a>Создание группы ресурсов

Группа ресурсов является основным контейнером ресурсов на платформе Azure. При работе с Azure Machine Learning группа ресурсов будет содержать рабочее пространство Машинного обучения Azure. Он также будет содержать другие службы Azure, используемые рабочим пространством. Например, если вы тренируете модель с помощью облачного вычислительного ресурса, этот ресурс создается в группе ресурсов.

Для __создания новой группы ресурсов__используйте следующую команду. Замените `<resource-group-name>` имя для использования для этой группы ресурсов. Заменить `<location>` область Azure для использования в этой группе ресурсов:

> [!TIP]
> Следует выбрать область, в которой доступно машинное обучение Azure. Для получения информации [см.](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-service)

```azurecli-interactive
az group create --name <resource-group-name> --location <location>
```

Ответ от этой команды аналогичен следующему JSON:

```json
{
  "id": "/subscriptions/<subscription-GUID>/resourceGroups/<resourcegroupname>",
  "location": "<location>",
  "managedBy": null,
  "name": "<resource-group-name>",
  "properties": {
    "provisioningState": "Succeeded"
  },
  "tags": null,
  "type": null
}
```

Для получения дополнительной информации о работе с группами ресурсов [см.](https://docs.microsoft.com//cli/azure/group?view=azure-cli-latest)

## <a name="create-a-workspace"></a>Создание рабочей области

Чтобы создать новое рабочее пространство, используйте следующую команду. Замените `<workspace-name>` имя, которое вы хотите использовать для этого рабочего пространства. Заменить `<resource-group-name>` название группы ресурсов:

```azurecli-interactive
az ml workspace create -w <workspace-name> -g <resource-group-name>
```

Выход этой команды аналогичен следующему JSON:

```json
{
  "applicationInsights": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<application-insight-name>",
  "containerRegistry": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.containerregistry/registries/<acr-name>",
  "creationTime": "2019-08-30T20:24:19.6984254+00:00",
  "description": "",
  "friendlyName": "<workspace-name>",
  "id": "/subscriptions/<service-GUID>/resourceGroups/<resource-group-name>/providers/Microsoft.MachineLearningServices/workspaces/<workspace-name>",
  "identityPrincipalId": "<GUID>",
  "identityTenantId": "<GUID>",
  "identityType": "SystemAssigned",
  "keyVault": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.keyvault/vaults/<key-vault-name>",
  "location": "<location>",
  "name": "<workspace-name>",
  "resourceGroup": "<resource-group-name>",
  "storageAccount": "/subscriptions/<service-GUID>/resourcegroups/<resource-group-name>/providers/microsoft.storage/storageaccounts/<storage-account-name>",
  "type": "Microsoft.MachineLearningServices/workspaces",
  "workspaceid": "<GUID>"
}
```

## <a name="connect-local-project-to-workspace"></a>Подключение локального проекта к рабочему пространству

Из запроса терминала или команды используйте следующие `cli-train-deploy` команды изменения каталогов в каталог, а затем подключитесь к рабочему пространству:

```azurecli-interactive
cd ~/MLOps/examples/cli-train-deploy
az ml folder attach -w <workspace-name> -g <resource-group-name>
```

Выход этой команды аналогичен следующему JSON:

```json
{
  "Experiment name": "model-training",
  "Project path": "/home/user/MLOps/examples/cli-train-deploy",
  "Resource group": "<resource-group-name>",
  "Subscription id": "<subscription-id>",
  "Workspace name": "<workspace-name>"
}
```

Эта команда `.azureml/config.json` создает файл, содержащий информацию, необходимую для подключения к рабочему пространству. Остальные `az ml` команды, используемые в этом учебнике, будут использовать этот файл, так что вам не придется добавлять рабочую область и группу ресурсов ко всем командам.

## <a name="create-the-compute-target-for-training"></a>Создание вычислительной цели для обучения

В этом примере используется кластер Azure Machine Learning Compute для обучения модели. Для создания нового вычислительного кластера используйте следующую команду:

```azurecli-interactive
az ml computetarget create amlcompute -n cpu-cluster --max-nodes 4 --vm-size Standard_D2_V2
```

Выход этой команды аналогичен следующему JSON:

```json
{
  "location": "<location>",
  "name": "cpu-cluster",
  "provisioningErrors": null,
  "provisioningState": "Succeeded"
}
```

Эта команда создает новую `cpu-cluster`вычислительную цель с именем, с максимум четырьмя узлами. Выбранный размер VM обеспечивает VM с ресурсом GPU. Для получения информации о размере VM см.

> [!IMPORTANT]
> Важно езначение вычислительной`cpu-cluster` цели (в данном случае); он ссылается на `.azureml/mnist.runconfig` файл, используемый в следующем разделе.

## <a name="define-the-dataset"></a>Определение набора данных

Для обучения модели можно предоставить обучающие данные с помощью набора данных. Чтобы создать набор данных из CLI, необходимо предоставить файл определения набора данных. Файл, `dataset.json` представленный в репо, создает новый набор данных с использованием данных MNIST. Создается набор `mnist-dataset`данных.

Чтобы зарегистрировать набор данных с помощью файла, `dataset.json` используйте следующую команду:

```azurecli-interactive
az ml dataset register -f dataset.json --skip-validation
```

Выход этой команды аналогичен следующему JSON:

```json
{
  "definition": [
    "GetFiles"
  ],
  "registration": {
    "description": "mnist dataset",
    "id": "a13a4034-02d1-40bd-8107-b5d591a464b7",
    "name": "mnist-dataset",
    "tags": {
      "sample-tag": "mnist"
    },
    "version": 1,
    "workspace": "Workspace.create(name='myworkspace', subscription_id='mysubscriptionid', resource_group='myresourcegroup')"
  },
  "source": [
    "http://yann.lecun.com/exdb/mnist/train-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/train-labels-idx1-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-images-idx3-ubyte.gz",
    "http://yann.lecun.com/exdb/mnist/t10k-labels-idx1-ubyte.gz"
  ]
}
```

> [!IMPORTANT]
> Копируйте значение `id` записи, так как она используется в следующем разделе.

Чтобы увидеть более полный шаблон для набора данных, используйте следующую команду:

```azurecli-interactive
az ml dataset register --show-template
```

## <a name="reference-the-dataset"></a>Ссылка на набор данных

Чтобы сделать набор данных доступным в среде обучения, необходимо ссылаться на него из файла runconfig. Файл `.azureml/mnist.runconfig` содержит следующие записи YAML:

```yaml
# The arguments to the script file.
arguments:
- --data-folder
- DatasetConsumptionConfig:mnist

.....

# The configuration details for data.
data:
  mnist:
# Data Location
    dataLocation:
# the Dataset used for this run.
      dataset:
# Id of the dataset.
        id: a13a4034-02d1-40bd-8107-b5d591a464b7
# the DataPath used for this run.
      datapath:
# Whether to create new folder.
    createOutputDirectories: false
# The mode to handle
    mechanism: mount
# Point where the data is download or mount or upload.
    environmentVariableName: mnist
# relative path where the data is download or mount or upload.
    pathOnCompute:
# Whether to overwrite the data if existing.
    overwrite: false
```

Измените значение `id` записи в соответствии с значением, возвращенным при регистрации набора данных. Это значение используется для загрузки данных в вычислительную цель во время обучения.

Это YAML приводит к следующим действиям во время обучения:

* Устанавливает набор данных (на основе идентификатора набора данных) в среде `mnist` обучения и сохраняет путь к точке крепления в переменной среды.
* С помощью аргумента `--data-folder` местоположение данных (точка крепления) внутри учебной среды передается скрипту.

Файл runconfig также содержит информацию, используемую для настройки среды, используемой в учебном запуске. Если вы проверите этот файл, вы `cpu-compute` увидите, что он ссылается на вычисляемую цель, созданную ранее. Он также перечисляет количество узлов для`"nodeCount": "4"`использования при `"condaDependencies"` обучении (), и содержит раздел, в котором перечислены пакеты Python, необходимые для выполнения сценария обучения.

> [!TIP]
> Хотя можно вручную создать файл runconfig, тот, который находится `generate-runconfig.py` в этом примере, был создан с помощью файла, включенного в репозиторий. Этот файл получает ссылку на зарегистрированный набор данных, создает конфигурацию запуска программно, а затем сохраняет его для файла.

Для получения дополнительной информации о файлах конфигурации выполнения [см.](how-to-set-up-training-targets.md#create-run-configuration-and-submit-run-using-azure-machine-learning-cli) Для полной ссылки JSON, [см.](https://github.com/microsoft/MLOps/blob/b4bdcf8c369d188e83f40be8b748b49821f71cf2/infra-as-code/runconfigschema.json)

## <a name="submit-the-training-run"></a>Отправить учебный забег

Для начала учебного `cpu-cluster` запуска на вычислительной цели используйте следующую команду:

```azurecli-interactive
az ml run submit-script -c mnist -e myexperiment --source-directory scripts -t runoutput.json
```

Эта команда определяет название эксперимента ().`myexperiment` Эксперимент хранит информацию об этом запуске в рабочем пространстве.

Параметр `-c mnist` определяет `.azureml/mnist.runconfig` файл.

Параметр `-t` хранит ссылку на этот запуск в файле JSON и будет использоваться в следующих шагах для регистрации и загрузки модели.

По мере того как процессы запуска обучения передают информацию из тренинга на ресурсе удаленного вычисления. Часть информации аналогична следующему тексту:

```output
Predict the test set
Accuracy is 0.9185
```

Этот текст регистрируется из сценария обучения и отображает точность модели. Другие модели будут иметь различные показатели производительности.

Если вы проверите сценарий обучения, вы заметите, что он `outputs/sklearn_mnist_model.pkl`также использует альфа-значение, когда он хранит обученную модель.

Модель была сохранена `./outputs` в каталоге на вычислительной цели, где она была обучена. В этом случае экземпляр Машинного обучения Azure compute в облаке Azure. Учебный процесс автоматически загружает содержимое `./outputs` каталога из вычислительной цели, где обучение происходит в вашем рабочем пространстве Azure Machine Learning. Он хранится в рамках эксперимента`myexperiment` (в данном примере).

## <a name="register-the-model"></a>Регистрация модели

Чтобы зарегистрировать модель непосредственно из сохраненной версии в эксперименте, используйте следующую команду:

```azurecli-interactive
az ml model register -n mymodel -f runoutput.json --asset-path "outputs/sklearn_mnist_model.pkl" -t registeredmodel.json
```

Эта команда регистрирует `outputs/sklearn_mnist_model.pkl` файл, созданный обучающий запуск, `mymodel`как новая регистрация модели под названием. Ссылки `--assets-path` на путь в эксперименте. В этом случае информация о эксперименте `runoutput.json` и запуске загружается из файла, созданного командой обучения. Файл `-t registeredmodel.json` JSON, который ссылается на новую зарегистрированную модель, созданную этой командой, и используется другими командами CLI, которые работают с зарегистрированными моделями.

Выход этой команды аналогичен следующему JSON:

```json
{
  "createdTime": "2019-09-19T15:25:32.411572+00:00",
  "description": "",
  "experimentName": "myexperiment",
  "framework": "Custom",
  "frameworkVersion": null,
  "id": "mymodel:1",
  "name": "mymodel",
  "properties": "",
  "runId": "myexperiment_1568906070_5874522d",
  "tags": "",
  "version": 1
}
```

### <a name="model-versioning"></a>управления версиями моделей;

Обратите внимание на номер версии, возвращенный для модели. Версия приращена каждый раз, когда вы регистрируете новую модель с этим именем. Например, можно загрузить модель и зарегистрировать ее из локального файла, используя следующие команды:

```azurecli-interactive
az ml model download -i "mymodel:1" -t .
az ml model register -n mymodel -p "sklearn_mnist_model.pkl"
```

Первая команда загружает зарегистрированную модель в текущий каталог. Имя файла, `sklearn_mnist_model.pkl`которое является файлом, на который ссылаются при регистрации модели. Вторая команда регистрирует локальную`-p "sklearn_mnist_model.pkl"`модель () с тем`mymodel`же названием, что и предыдущая регистрация (). На этот раз возвращенные данные JSON перечисляет версию как 2.

## <a name="deploy-the-model"></a>Развертывание модели

Для развертывания модели используйте следующую команду:

```azurecli-interactive
az ml model deploy -n myservice -m "mymodel:1" --ic inferenceConfig.json --dc aciDeploymentConfig.yml
```

> [!NOTE]
> Вы можете получить предупреждение о "Не удалось проверить существование LocalWebservice" или "Не удалось создать клиента Docker". Вы можете спокойно игнорировать это, так как вы не развертываете локальный веб-сервис.

Эта команда развертывает новую службу под названием, `myservice`используя версию 1 модели, которую вы зарегистрировали ранее.

Файл `inferenceConfig.yml` содержит информацию о том, как использовать модель для выводов. Например, он ссылается на`score.py`сценарий входа () и программные зависимости.

Для получения дополнительной информации о структуре этого [файла](reference-azure-machine-learning-cli.md#inference-configuration-schema)см. Для получения дополнительной информации о скриптах входа см. [Модели развертываний с помощью машинного обучения Azure.](how-to-deploy-and-where.md#prepare-to-deploy)

Описывает `aciDeploymentConfig.yml` среду развертывания, используемую для размещения службы. Конфигурация развертывания специфична для типа вычислений, который используется для развертывания. В этом случае используется экземпляр контейнеров Azure. Для получения дополнительной [Deployment configuration schema](reference-azure-machine-learning-cli.md#deployment-configuration-schema)информации см.

Это займет несколько минут, прежде чем процесс развертывания завершается.

> [!TIP]
> В этом примере используются экземпляры контейнеров Azure. Развертывание ACI автоматически создает необходимый ресурс ACI. Вместо развертывания службы Azure Kubernetes необходимо заблаговременно создать кластер AKS и указать `az ml model deploy` его как часть команды. Например, развертывание для AKS см. [Развертывание модели в кластере службы Azure Kubernetes.](how-to-deploy-azure-kubernetes-service.md)

Через несколько минут возвращается информация, аналогичная следующему JSON:

```json
ACI service creation operation finished, operation "Succeeded"
{
  "computeType": "ACI",
  {...ommitted for space...}
  "runtimeType": null,
  "scoringUri": "http://6c061467-4e44-4f05-9db5-9f9a22ef7a5d.eastus2.azurecontainer.io/score",
  "state": "Healthy",
  "tags": "",
  "updatedAt": "2019-09-19T18:22:32.227401+00:00"
}
```

### <a name="the-scoring-uri"></a>Забив уНИ

Возвращение `scoringUri` из развертывания — это конечная точка REST для модели, развернутой в качестве веб-службы. Вы также можете получить этот URI, используя следующую команду:

```azurecli-interactive
az ml service show -n myservice
```

Эта команда возвращает тот же документ `scoringUri`JSON, включая .

Конечная точка REST может быть использована для отправки данных в службу. Для получения информации о создании клиентского приложения, отправляющего данные в службу, [см.](how-to-consume-web-service.md)

### <a name="send-data-to-the-service"></a>Отправка данных в службу

В то время как вы можете создать клиентское приложение для вызова конечной точки, CLI машинного обучения обеспечивает утилиту, которая может выступать в качестве тестового клиента. Используйте следующую команду для `testdata.json` отправки данных в файле в службу:

```azurecli-interactive
az ml service run -n myservice -d @testdata.json
```

> [!TIP]
> Если вы используете PowerShell, используйте следующую команду вместо этого:
>
> ```azurecli-interactive
> az ml service run -n myservice -d `@testdata.json
> ```

Ответ от команды похож `[ 3 ]`на .

## <a name="clean-up-resources"></a>Очистка ресурсов

> [!IMPORTANT]
> Созданные ресурсы могут использоваться в качестве необходимых компонентов при работе с другими руководствами по Машинному обучению Azure.

### <a name="delete-deployed-service"></a>Удаление развернутого сервиса

Если вы планируете продолжать использовать рабочее пространство Azure Machine Learning, но хотите избавиться от развернутой службы для снижения затрат, используйте следующую команду:

```azurecli-interactive
az ml service delete -n myservice
```

Эта команда возвращает документ JSON, содержащий имя удаленной службы. Это может занять несколько минут, прежде чем служба будет удалена.

### <a name="delete-the-training-compute"></a>Удаление обучаемого вычисления

Если вы планируете продолжать использовать рабочее пространство Azure Machine Learning, но хотите избавиться от `cpu-cluster` вычислительной цели, созданной для обучения, используйте следующую команду:

```azurecli-interactive
az ml computetarget delete -n cpu-cluster
```

Эта команда возвращает документ JSON, содержащий идентификатор удаленной цели вычислений. Это может занять несколько минут, прежде чем вычислительная цель была удалена.

### <a name="delete-everything"></a>Удаление всех ресурсов

Если вы не планируете использовать созданные ресурсы, удалите их, чтобы не взимать дополнительных расходов.

Чтобы удалить группу ресурсов и все ресурсы Azure, созданные в этом документе, используйте следующую команду. Замените `<resource-group-name>` имя группы ресурсов, созданной ранее:

```azurecli-interactive
az group delete -g <resource-group-name> -y
```

## <a name="next-steps"></a>Дальнейшие действия

В этом учебнике по машинного обучения Azure вы использовали CLI машинного обучения для следующих задач:

> [!div class="checklist"]
> * Установка расширения машинного обучения
> * Создание рабочей области машинного обучения Azure
> * Создание вычислительного ресурса, используемого для обучения модели
> * Определение и регистрация набора данных, используемого для обучения модели
> * Начало тренировочного забега
> * Регистрация и загрузка модели
> * Развертывание модели в качестве веб-службы.
> * Оценка данных с помощью веб-службы

Для получения дополнительной информации об использовании CLI [см.](reference-azure-machine-learning-cli.md)
