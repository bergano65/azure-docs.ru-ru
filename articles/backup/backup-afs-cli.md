---
title: Резервное копирование файлов Azure с помощью Azure CLI
description: Узнайте, как использовать Azure CLI для резервного копирования акций файлов Azure в Хранилище служб восстановления
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76844047"
---
# <a name="back-up-azure-file-shares-with-cli"></a>Резервное копирование акций файлов Azure с ПОМОЩЬю CLI

Интерфейс командной строки Azure (CLI) обеспечивает взаимодействие с командной строкой для управления ресурсами Azure. Это отличный инструмент для создания пользовательской автоматизации для использования ресурсов Azure. В этой статье подробно описывается, как создать резервное копирование файлов Azure с помощью Azure CLI. Эти действия также можно выполнить с помощью [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) или [портала Azure](backup-afs.md).

К концу этого урока вы узнаете, как выполнять ниже операции с Azure CLI:

* Создание хранилища служб восстановления
* Включить резервное копирование для акций файлов Azure
* Триггер резервного копирования по требованию для файловых акций

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать интерфейс командной строки локально, необходимо использовать Azure CLI версии 2.0.18 или более поздней. Чтобы найти версию `run az --version`CLI, . Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-recovery-services-vault"></a>Создание Хранилища служб восстановления

Хранилище службы восстановления — это сущность, которая предоставляет консолидированное представление и возможности управления во всех элементах резервного копирования. Когда выполняется задание резервного копирования для защищенного ресурса, в хранилище служб восстановления создается точка восстановления. Позже вы сможете использовать одну из этих точек восстановления, чтобы восстановить данные до определенной точки во времени.

Выполните следующие действия, чтобы создать хранилище служб восстановления:

1. Хранилище помещается в группу ресурсов. Если у вас нет существующей группы ресурсов, создайте новую группу с [помощью группы az.](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) В этом учебнике мы создаем новую группу ресурсов *azurefiles* в восточном регионе США.

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. Используйте [хранилище резервного копирования az для создания](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) cmdlet для создания хранилища. Разместите хранилище там же, где находится группа ресурсов.

    В следующем примере создается хранилище служб восстановления под названием *azurefilesvault* в восточном регионе США.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. Укажите тип избыточности для использования для хранения хранилища. [локально избыточное](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) или [геоизбыточное](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

    В следующем примере устанавливается опция избыточности хранилища для *azurefilesvault* для **Georedundant** с помощью резервного [хранилища АЗ-свойства резервного копирования, установленного](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) cmdlet.

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    Чтобы проверить, успешно ли хранилище создано, можно использовать [хранилище резервного копирования az,](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) чтобы получить подробную информацию о хранилище. В следующем примере отображаются детали *лазурного файла,* который мы создали на приведенных выше шагах.

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    Выход будет аналогичен следующему ответу:

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Включить резервное копирование для акций файлов Azure

В этом разделе предполагается, что у вас уже есть общий файл Azure, для которого требуется настроить резервную. Если его нет, создайте раздел файла Azure, используя [команду создания элемента обмена хранилищами.](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create)

Для обеспечения резервного копирования файлов необходимо создать политику защиты, которая определяет, когда выполняется задание резервного копирования и как долго сохраняются точки восстановления. Можно создать политику резервного копирования с помощью [политики резервного копирования az, создающих](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) cmdlet.

В следующем примере используется [защита резервного копирования az enable-for-azurefileshare](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) cmdlet для резервного копирования для раздела файла *azurefiles* в учетной записи хранения *afsaccount* с помощью политики резервного копирования *графика 1:*

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

Атрибут **имени** в выводе соответствует названию задания, созданного службой резервного копирования для операции **резервного копирования.** Чтобы отследить состояние задания, используйте [задание резервного копирования az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Триггер резервного копирования по требованию для общего элемента файла

Если вы хотите запустить резервную по требованию резервную систему для вашей доли файлов вместо того, чтобы ждать, пока политика резервного копирования запустит задание в запланированное время, используйте теперь шмдлет резервного копирования [аз-защиты.](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now)

Для запуска резервного копирования по требованию необходимо определить следующие параметры:

* **--контейнер-имя** — это название учетной записи хранения, в которой размещается доля файлов. Чтобы получить **имя** или **дружественное имя** контейнера, используйте команду [списка резервного копирования АЗ.](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--имя элемента** — это имя доли файла, для которого требуется запустить резервную по требованию резервную. Чтобы получить **имя** или **дружеское имя** резервного элемента, используйте команду [списка резервного копирования az.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--сохранить-до тех пор, пока** определяет дату до тех пор, пока вы хотите сохранить точку восстановления. Значение должно быть установлено в формате времени UTC (dd-mm-yyyy).

Следующий пример запускает резервное копирование по требованию для файлохранилища *azuresfiles* в учетной записи хранения *afsaccount* с сохранением до *20-01-2020*.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

Атрибут **имени** в выходе соответствует названию задания, созданного службой резервного копирования для операции резервного копирования по требованию. Чтобы отследить состояние задания, используйте [задание резервного копирования az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [восстановить файлы Azure с помощью CLI](restore-afs-cli.md)
* Узнайте, как [управлять ackups файлов Azure с CLI](manage-afs-backup-cli.md)
