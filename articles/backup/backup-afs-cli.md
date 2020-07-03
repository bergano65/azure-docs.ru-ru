---
title: Резервное копирование файловых ресурсов Azure с помощью Azure CLI
description: Узнайте, как использовать Azure CLI для резервного копирования файловых ресурсов Azure в хранилище служб восстановления.
ms.topic: conceptual
ms.date: 01/14/2020
ms.openlocfilehash: ff1d8c6245521d2d0262b0440177d65713058742
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76844047"
---
# <a name="back-up-azure-file-shares-with-cli"></a>Резервное копирование файловых ресурсов Azure с помощью интерфейса командной строки

Интерфейс командной строки Azure (CLI) предоставляет возможности командной строки для управления ресурсами Azure. Это отличный инструмент для создания пользовательской автоматизации для использования ресурсов Azure. В этой статье подробно описано, как создавать резервные копии файловых ресурсов Azure с помощью Azure CLI. Эти действия также можно выполнить с помощью [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) или [портала Azure](backup-afs.md).

По завершении работы с этим руководством вы узнаете, как выполнять приведенные ниже операции с Azure CLI.

* Создание хранилища служб восстановления
* Включение резервного копирования для файловых ресурсов Azure
* Активация резервной копии по запросу для файловых ресурсов

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать интерфейс командной строки локально, необходимо использовать Azure CLI версии 2.0.18 или более поздней. Чтобы найти версию CLI, `run az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="create-a-recovery-services-vault"></a>Создание хранилища служб восстановления

Хранилище службы восстановления — это сущность, которая предоставляет объединенное представление и возможности управления для всех элементов резервного копирования. Когда выполняется задание резервного копирования для защищенного ресурса, в хранилище служб восстановления создается точка восстановления. Позже вы сможете использовать одну из этих точек восстановления, чтобы восстановить данные до определенной точки во времени.

Чтобы создать хранилище служб восстановления, выполните следующие действия.

1. Хранилище помещается в группу ресурсов. Если у вас нет группы ресурсов, создайте ее с помощью команды [AZ Group Create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create) . В этом руководстве мы создадим новую группу ресурсов *azurefiles* в регионе "Восточная часть США".

    ```azurecli-interactive
    az group create --name AzureFiles --location eastus --output table
    ```

    ```output
    Location    Name
    ----------  ----------
    eastus      AzureFiles
    ```

2. Для создания хранилища используйте командлет [AZ Backup Vault Create](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-create) . Разместите хранилище там же, где находится группа ресурсов.

    В следующем примере создается хранилище служб восстановления с именем *азурефилесваулт* в регионе Восточная часть США.

    ```azurecli-interactive
    az backup vault create --resource-group azurefiles --name azurefilesvault --location eastus --output table
    ```

    ```output
    Location    Name                ResourceGroup
    ----------  ----------------    ---------------
    eastus      azurefilesvault     azurefiles
    ```

3. Укажите тип избыточности, используемый для хранения хранилища. [локально избыточное](https://docs.microsoft.com/azure/storage/common/storage-redundancy-lrs) или [геоизбыточное](https://docs.microsoft.com/azure/storage/common/storage-redundancy-grs).

    В следующем примере параметр избыточности хранилища для *азурефилесваулт* задается для **геоизбыточного** использования с помощью команды [AZ Backup Vault Backup-Properties Set](https://docs.microsoft.com/cli/azure/backup/vault/backup-properties?view=azure-cli-latest#az-backup-vault-backup-properties-set) .

    ```azurecli-interactive
    az backup vault backup-properties set --name azurefilesvault --resource-group azurefiles --backup-storage-redundancy Georedundant
    ```

    Чтобы проверить, успешно ли создано хранилище, можно использовать командлет [AZ Backup Vault](https://docs.microsoft.com/cli/azure/backup/vault?view=azure-cli-latest#az-backup-vault-show) , чтобы получить сведения о хранилище. В следующем примере отображаются сведения о *азурефилесваулт* , созданном в описанных выше действиях.

    ```azurecli-interactive
    az backup vault show --name azurefilesvault --resource-group azurefiles --output table
    ```

    Выходные данные будут похожи на следующий ответ:

    ```output
    Location     Name               ResourceGroup
    ----------   ---------------    ---------------
    eastus       azurefilesvault    azurefiles
    ```

## <a name="enable-backup-for-azure-file-shares"></a>Включение резервного копирования для файловых ресурсов Azure

В этом разделе предполагается, что у вас уже есть файловый ресурс Azure, для которого требуется настроить резервное копирование. Если у вас ее нет, создайте общую папку Azure с помощью команды [AZ Storage Share Create](https://docs.microsoft.com/cli/azure/storage/share?view=azure-cli-latest#az-storage-share-create) .

Чтобы включить резервное копирование для файловых ресурсов, необходимо создать политику защиты, определяющую время выполнения задания резервного копирования и время хранения точек восстановления. Политику архивации можно создать с помощью командлета [AZ Backup Policy Create](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-create) .

В следующем примере используется командлет [AZ Backup Protection Enable-for-azurefileshare](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-enable-for-azurefileshare) , чтобы включить резервное копирование для файлового ресурса *azurefiles* в учетной записи хранения *афсаккаунт* с помощью политики архивации по *расписанию 1* :

```azurecli-interactive
az backup protection enable-for-azurefileshare --vault-name azurefilesvault --resource-group  azurefiles --policy-name schedule1 --storage-account afsaccount --azure-file-share azurefiles  --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
0caa93f4-460b-4328-ac1d-8293521dd928  azurefiles
```

Атрибут **Name** в выходных данных соответствует имени задания, созданного службой резервного копирования для операции **включения резервного копирования** . Чтобы отвести состояние задания, используйте командлет [AZ Backup Job](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) #.

## <a name="trigger-an-on-demand-backup-for-file-share"></a>Активация резервной копии по запросу для файлового ресурса

Если вы хотите активировать резервное копирование по запросу для общей папки, а не ждать, пока политика архивации запустит задание в запланированное время, используйте командлет [AZ Backup Protection Backup-Now](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-backup-now) .

Чтобы активировать резервное копирование по запросу, необходимо определить следующие параметры:

* **--Container-Name** — это имя учетной записи хранения, в которой размещена общая папка. Чтобы получить **имя** или **понятное имя** контейнера, используйте команду [AZ Backup Container List](/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list) .
* **--Item-Name** — это имя общего файлового ресурса, для которого требуется активировать резервное копирование по запросу. Чтобы получить **имя** или **понятное имя** архивированного элемента, используйте команду [AZ Backup Item List](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list) .
* **--Сохранение** до указывает дату до тех пор, пока не будет храниться точка восстановления. Значение должно быть задано в формате времени в формате UTC (дд-мм-гггг).

Следующий пример активирует резервную копию по запросу для общей папки *азуресфилес* в учетной записи хранения *афсаккаунт* с периодом хранения до *20-01-2020*.

```azurecli-interactive
az backup protection backup-now --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --retain-until 20-01-2020 --output table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
9f026b4f-295b-4fb8-aae0-4f058124cb12  azurefiles
```

Атрибут **Name** в выходных данных соответствует имени задания, созданного службой архивации для операции резервного копирования по требованию. Чтобы отвести состояние задания, используйте командлет [AZ Backup Job показывать](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) .

## <a name="next-steps"></a>Дальнейшие шаги

* Узнайте, как [восстановить файловые ресурсы Azure с помощью интерфейса командной строки](restore-afs-cli.md) .
* Узнайте, как [управлять файловыми ресурсами Azure аккупс с помощью интерфейса командной строки](manage-afs-backup-cli.md)
