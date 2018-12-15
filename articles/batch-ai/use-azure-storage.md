---
title: Хранение входных и выходных данных задания Batch AI с помощью службы хранилища Azure | Документы Майкрософт
description: Использование хранилища Azure вместе с Batch AI для быстрого и простого облачного хранения входных и выходных файлов
services: batch-ai
documentationcenter: ''
author: kevwang1
manager: jeconnoc
editor: ''
ms.service: batch-ai
ms.topic: article
ms.date: 08/14/2018
ms.author: danlep
ms.custom: mvc
ROBOTS: NOINDEX
ms.openlocfilehash: 1e9a4c6355c60b18bb78aae362c1e2f142e2d864
ms.sourcegitcommit: c37122644eab1cc739d735077cf971edb6d428fe
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53408006"
---
# <a name="store-batch-ai-job-input-and-output-with-azure-storage"></a>Хранение входных и выходных данных задания Batch AI с помощью хранилища Azure

[!INCLUDE [batch-ai-retiring](../../includes/batch-ai-retiring.md)]

В этом руководстве описывается использование службы хранилища Azure для хранения входных и выходных файлов при выполнении задания. Хранилище Azure — один из нескольких вариантов хранения, поддерживаемых службой Batch AI. Batch AI интегрируется с хранилищем Azure при помощи подключения систем хранения данных Azure к заданию Batch AI или файловой системе кластера, обеспечивая прозрачный доступ к файлам, хранящимся в облаке. 

## <a name="introduction-to-azure-storage"></a>Общие сведения о службе хранилища Azure

Служба хранилища Azure — это решение облачного хранилища от Майкрософт. Batch AI поддерживает подключение контейнеров BLOB-объектов Azure и файловых ресурсов Azure к заданиям Batch AI или кластерам, позволяя получать доступ к файлам из задания, как если бы они находились в собственной файловой системе. Batch AI подключает контейнеры BLOB-объектов Azure с помощью инструмента [blobfuse](https://github.com/Azure/azure-storage-fuse), а файловые ресурсы Azure — по протоколу SMB. Дополнительные сведения о службе хранилища Azure см. в статье [Общие сведения о службе хранилища Azure](../storage/common/storage-introduction.md).

## <a name="store-datasets-and-input-scripts-in-azure-storage"></a>Хранение наборов данных и входных сценариев в службе хранилища Azure

При выборе хранилища Azure для вашей среды Batch AI, мы рекомендуем хранить входные файлы (такие как наборы данных) в контейнере BLOB-объектов, который имеет более высокую пропускную способность, а выходные учебные данные — в общей папке, которая поддерживает потоковую передачу (что позволяет читать журналы вывода параллельно с выполнением задания). 

Прежде чем использовать службу хранилища Azure, необходимо [создать учетную запись хранилища Azure](../storage/common/storage-quickstart-create-account.md). Batch AI поддерживает подключение томов из учетных записей хранения Azure общего назначения версии 1 (GPv1) и версии 2 (GPv2). Учетная запись хранения Azure может содержать несколько контейнеров BLOB-объектов или экземпляров общих папок. При выборе типа создаваемой учетной записи хранения учитывайте требования к расходам и производительности. Дополнительные сведения см. в статье [Общие сведения об учетной записи хранения Azure](../storage/common/storage-account-overview.md). 

Чтобы создать контейнер BLOB-объектов Azure и передать в него набор данных, выберите один из следующих методов:
- [Портал Azure](../storage/blobs/storage-quickstart-blobs-portal.md) для отправки с графическим веб-интерфейсом. Для отправки небольшого количества файлов удобнее всего воспользоваться порталом Azure.
- [Azure CLI для хранилища](../storage/blobs/storage-quickstart-blobs-cli.md) для передачи с помощью командной строки (поддерживается отправка каталога). Чтобы загрузить каталоги файлов, используйте команду `az storage blob upload-batch`.
- [другие инструменты](../storage/common/storage-moving-data.md), включая использование пакетов SDK для приложений.

Аналогичным образом, чтобы создать файловый ресурс Azure, выберите один из следующих методов:
- [портал Azure](../storage/files/storage-how-to-use-files-portal.md)
- [Azure CLI для хранилища](../storage/files/storage-how-to-use-files-cli.md)
- [другие инструменты](../storage/common/storage-moving-data.md)

### <a name="auto-storage-with-batch-ai"></a>Автоматическое хранилище для Batch AI

Кроме того, можно создать учетную запись хранилища Azure с файловым ресурсом Azure и контейнером BLOB-объектов (и автоматически подключить эти тома к кластеру Batch AI) с помощью параметра `--use-auto-storage` в команде `az batchai cluster create`. Дополнительные сведения см. [здесь](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#auto-storage-account).

## <a name="mount-azure-storage"></a>Подключение хранилища Azure 

### <a name="mount-volumes-to-a-job"></a>Подключение томов к заданию

Подключение тома хранилища Azure позволяет получать доступ к нему с помощью файловой системы, созданной для каждого задания. Таким образом, задание может прозрачно считывать и записывать файлы в облачное хранилище, как если бы они были локальными файлами.

Чтобы подключить для задания том хранилища Azure, созданный с помощью Azure CLI, используйте свойство `mountVolumes` в файле `job.json` при запуске команды `az batchai job create`. Подробные сведения см. в [примере распределенных вычислений в библиотеке TensorFlow с использованием GPU](https://github.com/Azure/BatchAI/blob/master/recipes/TensorFlow/TensorFlow-GPU-Distributed/job.json). Схема для `mountVolumes` выглядит следующим образом:
```json
{
    "mountVolumes": {
        "azureFileShares": [{
            "azureFileUrl": "https://<STORAGE_ACCOUNT_NAME>.file.core.windows.net/<FILE_SHARE_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }],
        "azureBlobFileSystems": [{
            "accountName": "<STORAGE_ACCOUNT_NAME>",
            "containerName": "<BLOB_CONTAINER_NAME>",
            "relativeMountPath": "<RELATIVE_MOUNT_PATH>"
        }]
    }
}
```

Параметры `azureFileShares` и `azureBlobFileSystems` оба являются массивами объектов, представляющих тома для подключения. Описания заполнителей:

- <RELATIVE_MOUNT_PATH> — том будет подключен по этому пути. Например, в том случае, если параметр `relativeMountPath` равен `jobs`, том будет размещаться в папке `$AZ_BATCHAI_JOB_MOUNT_ROOT/jobs`
- <STORAGE_ACCOUNT_NAME> — имя учетной записи хранилища Azure, которая содержит общую папку или контейнер BLOB-объектов
- <FILE_SHARE_NAME> — имя общей папки
- <BLOB_CONTAINER_NAME> — имя контейнера BLOB-объектов

Чтобы подключить тома хранилища Azure с помощью пакетов SDK Azure Batch AI, задайте свойство `mount_volumes` (Python) или `MountVolumes` (C#, Java) в `JobCreateParameters`. При подключении тома с помощью пакетов SDK Azure Batch AI, необходимо предоставить учетные данные учетной записи хранения. Ознакомьтесь со схемами для подключения томов в [Python](https://docs.microsoft.com/python/api/azure-mgmt-batchai/azure.mgmt.batchai.models.MountVolumes?view=azure-python), [C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.batchai.models.mountvolumes?view=azure-dotnet) и [Java](https://docs.microsoft.com/java/api/com.microsoft.azure.management.batchai._mount_volumes?view=azure-java-stable).

### <a name="mount-volumes-to-a-cluster"></a>Подключение томов к кластеру

Batch AI также поддерживает подключение томов хранилища Azure к кластеру Batch AI. При подключении тома к кластеру все задания, выполняемые в этом кластере, могут использовать тома, подключенные к этому кластеру. Подключение на уровне задания обеспечивает наибольшую гибкость (позволяя каждому заданию иметь различные подключенные тома), подключение на уровне кластера может быть оправдано для простых сценариев.

Чтобы подключить для кластера том хранилища Azure, созданный с помощью Azure CLI, используйте свойство `mountVolumes` в файле `cluster.json` при запуске команды `az batchai cluster create`. Схема для `mountVolumes` при подключении кластера совпадает со схемой при подключении к заданию. 

Аналогично, можно использовать свойство `mount_volumes` (Python) или `MountVolumes` (C#, Java) в `ClusterCreateParameters` при подключении с помощью пакетов SDK Azure Batch AI. 

## <a name="access-mounted-filesystem-in-a-job"></a>Доступ к подключенной файловой системе в задании

### <a name="azbatchaijobmountroot-environment-variable"></a>Переменная среды $AZ_BATCHAI_JOB_MOUNT_ROOT

Внутри среды выполнения задания доступ к каталогу, содержащему подключенную систему хранилищ, может осуществляться с помощью переменной среды `$AZ_BATCHAI_JOB_MOUNT_ROOT` (при использовании подключения на уровне задания). Если вы используете подключение на уровне кластера, используйте переменную среды `$AZ_BATCHAI_MOUNT_ROOT`. В следующих примерах предполагается, что вы используете подключение на уровне задания.

Для предоставления пути к данным в подключенном томе используйте переменную среды `$AZ_BATCHAI_JOB_MOUNT_ROOT` вместе с путем подключения. Например, если учебный сценарий `train.py` был загружен в общую папку Azure, подключенную по относительному пути `scripts`, то файл будет доступен по пути `$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py`.

Если учебный сценарий требует сведений о пути, их следует передавать в виде аргумента командной строки. Например, если вы сохранили данные в папку с именем `train_data` в контейнере BLOB-объектов Azure, подключенному по пути `data`, можно передать аргумент командной строки `--data-dir=$AZ_BATCHAI_JOB_MOUNT_ROOT/data/train_data` в ваш сценарий. Соответственно, необходимо изменить ваш сценарий, чтобы он мог принимать аргументы командной строки.

### <a name="abbreviate-input-paths"></a>Сокращение путей входных данных

Для сокращения путей входных данных в виде переменной среды используйте свойство `inputDirectories` файла `job.json` (или `models.JobCreateParameters.input_directories` при использовании пакета SDK Batch AI). Схема `inputDirectories` выглядит следующим образом:

```json
{
    "inputDirectories": [{
        "id": "<ID>",
        "path": "<PATH>" 
    }]
}
```

Каждый указанный путь помещается в переменную среды с именем `$AZ_BATCHAI_INPUT_<ID>`. С помощью этого метода можно упростить пути к входным файлам или каталогам. Например, для сокращения пути к учебному сценарию: если `"id"` равно `"SCRIPT"` и `"path"` равно `"$AZ_BATCHAI_JOB_MOUNT_ROOT/scripts/train.py"`, то сценарий будет доступен по пути `$AZ_BATCHAI_INPUT_SCRIPT` в среде выполнения задания.

Дополнительные сведения см. [здесь](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#input-directories).

### <a name="abbreviate-output-paths"></a>Сокращение путей выходных данных

Для сокращения путей выходных данных в виде переменной среды используйте свойство `outputDirectories` файла `job.json` (или `models.JobCreateParameters.output_directories` при использовании пакета SDK Batch AI). С помощью этого метода можно упростить пути к выходным файлам. Схема `outputDirectories` выглядит следующим образом:

```json
{
    "outputDirectories": [{
        "id": "<ID>",
        "pathPrefix": "<PATH_PREFIX>",
        "pathSuffix": "<PATH_SUFFIX>"
    }]
}
```

Каждый указанный путь помещается в переменную среды с именем `$AZ_BATCHAI_OUTPUT_<ID>`. Параметр `pathPrefix` определяет подключенный том для хранения выходных данных (например, `"$AZ_BATCHAI_JOB_MOUNT_ROOT/output"`). Параметр `pathSuffix` определяет имя папки выходных данных (например, `"logs"`, `"checkpoints"`). Фактический путь выходных данных представляет собой объединение `pathPrefix`, `jobOutputDirectoryPathSegment` (автоматически генерируется для каждого задания) и `pathSuffix`.

Дополнительные сведения см. [здесь](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#output-directories).

## <a name="retrieve-job-output-from-azure-storage"></a>Извлечение выходных данных задания из хранилища Azure

### <a name="use-azure-portal"></a>Использование портала Azure

Портал Azure предоставляет удобный способ просмотра выходных данных задания с помощью проводника с графическим интерфейсом. Тем не менее, если вы хотите просмотреть выходные данные из Stdout и Stderr или из пути в `outputDirectories`, то эти файлы помещаются в автоматически сгенерированном пути в вашем томе хранилища Azure. Дополнительную информацию см. ниже.

### <a name="access-stdout-and-stderr-output"></a>Доступ к выходным данным из Stdout и Stderr

Используйте свойство `stdOutErrPathPrefix` в `job.json`, чтобы сообщить заданию, куда поместить журналы выполнения задания и выходные данные из Stdout и Stderr. Например, если подключить общую папку по относительному пути `outputs` и указать `"$AZ_BATCHAI_JOB_MOUNT_ROOT/outputs"` в качестве значения `stdOutErrPathPrefix`, то выходные данные задания Stdout и Stderr будут доступны по пути `{subscription id}/{resource group}/workspaces/{workspace name}/experiments/{experiment name}/jobs/{job name}/{job uuid}/stdouterr` в этом подключенном томе. Этот автоматически сгенерированный путь используется для предотвращения коллизий выходных данных заданий с одинаковыми именами.

Дополнительные сведения см. [здесь](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#standard-and-error-output).

### <a name="list-the-output-files"></a>Список выходных файлов

Azure CLI можно использовать для получения списка доступных выходных файлов задания с помощью команды `az batchai job file list`. Например, чтобы получить список файлов в стандартном каталоге выходных данных задания, используйте `az batchai job file list -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME>`.

Дополнительные сведения и примеры см. [здесь](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories).

### <a name="stream-output-files"></a>Потоковая передача выходных файлов

Azure CLI можно использовать для потоковой передачи файлов с помощью команды `az batchai job file stream`. Например, для просмотра:
- Stdout: `az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stdout.txt`
- Stderr: `az batchai job file stream -j <JOB_NAME> -g <RESOURCE_GROUP_NAME> -w <WORKSPACE_NAME> -e <EXPERIMENT_NAME> -f stderr.txt`

Дополнительные сведения и примеры см. [здесь](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#stream-files-from-output-directories).

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения о командах CLI для взаимодействия со службой хранилища Azure см. в [документации по Azure CLI для Batch AI](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md).
- Дополнительные примеры использования Batch AI, включая подключение хранилища и чтение выходных файлов, см. в [примерах Jupyter Notebook для Batch AI](https://github.com/Azure/BatchAI).
- Ознакомьтесь с другими вариантами подключения хранилищ, включая [подключение в качестве NFS-сервера](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs) и [подключение собственного кластера NFS, CIFS или GlusterFS](https://github.com/Azure/BatchAI/blob/master/documentation/using-azure-cli-20.md#mounting-nfs)