---
title: Управление резервными ожиданиями совместного файла Azure с помощью ClI Azure
description: Узнайте, как использовать Azure CLI для управления и мониторинга файлов Azure, поддерживаемых резервным копированием Azure.
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 44a49913abd99b285397b8b78ad9d4c0f9df52ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76934876"
---
# <a name="manage-azure-file-share-backups-with-the-azure-cli"></a>Управление резервными ожиданиями совместного файла Azure с помощью ClI Azure

Azure CLI предоставляет командную строку для управления ресурсами Azure. Это отличный инструмент для создания пользовательской автоматизации для использования ресурсов Azure. В этой статье объясняется, как выполнять задачи по управлению и мониторингу файлов Azure, которые поддерживаются [резервным копированием Azure.](https://docs.microsoft.com/azure/backup/backup-overview) Вы также можете выполнить эти действия с [порталом Azure](https://portal.azure.com/). 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Для установки и использования CLI локально необходимо запустить версию Azure CLI 2.0.18 или позже. Чтобы получить необходимую версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что у вас уже есть доля файлов Azure, подкрепленная [резервным копированием Azure.](https://docs.microsoft.com/azure/backup/backup-overview) Если у вас его нет, см. [Резервное копирование файлов Azure с ПОМОЩЬю CLI](backup-afs-cli.md) для настройки резервной копирования для ваших файлов. Для этой статьи используются следующие ресурсы:

* **Группа ресурсов**: *лазуриты*
* **RecoveryServicesVault**: *лазуриции*
* **Учетная запись хранения**: *afsaccount*
* **Доля файлов**: *лазурные файлы*

## <a name="monitor-jobs"></a>Мониторинг заданий

При запуске резервного копирования или восстановлении операций служба резервного копирования создает задание для отслеживания. Для мониторинга завершенных или в настоящее время запущенных заданий используйте [список вакансий резервного копирования az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-list) cmdlet. С помощью CLI вы также можете приостановить работу, которая в [настоящее время работает,](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-stop) или ждать завершения [задания.](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-wait)

В следующем примере отображается состояние задания резервного копирования для хранилища служб восстановления *azurefilesvault:*

```azurecli-interactive
az backup job list --resource-group azurefiles --vault-name azurefilesvault
```

```output
[
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "location": null,
    "name": "d477dfb6-b292-4f24-bb43-6b14e9d06ab5",
    "properties": {
      "actionsInfo": null,
      "activityId": "3cef43ed-0af4-43e2-b9cb-1322c496ccb4",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:29.718011",
      "endTime": "2020-01-13T08:05:29.180606+00:00",
      "entityFriendlyName": "azurefiles",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.462595+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  },
  {
    "eTag": null,
    "id": "/Subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/azurefiles/providers/Microsoft.RecoveryServices/vaults/azurefilesvault/backupJobs/1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "location": null,
    "name": "1b9399bf-c23c-4caa-933a-5fc2bf884519",
    "properties": {
      "actionsInfo": null,
      "activityId": "2663449c-94f1-4735-aaf9-5bb991e7e00c",
      "backupManagementType": "AzureStorage",
      "duration": "0:00:28.145216",
      "endTime": "2020-01-13T08:05:27.519826+00:00",
      "entityFriendlyName": "azurefilesresource",
      "errorDetails": null,
      "extendedInfo": null,
      "jobType": "AzureStorageJob",
      "operation": "Backup",
      "startTime": "2020-01-13T08:04:59.374610+00:00",
      "status": "Completed",
      "storageAccountName": "afsaccount",
      "storageAccountVersion": "MicrosoftStorage"
    },
    "resourceGroup": "azurefiles",
    "tags": null,
    "type": "Microsoft.RecoveryServices/vaults/backupJobs"
  }
]
```

## <a name="modify-policy"></a>Изменение политики

Можно изменить политику резервного копирования, чтобы изменить частоту резервного копирования или диапазон удержания, используя [политику настройки элемента резервного копирования az.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-set-policy)

Чтобы изменить политику, определите следующие параметры:

* **--контейнер-имя**: Имя учетной записи хранилища, в котором размещаются общие файлы. Чтобы получить **имя** или **дружественное имя** контейнера, используйте команду [списка резервного копирования АЗ.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--имя**: Имя раздела файла, для которого вы хотите изменить политику. Чтобы получить **имя** или **дружеское имя** резервного элемента, используйте команду [списка резервного копирования az.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--имя политики**: Имя политики резервного копирования, которое вы хотите установить для вашей доли файла. Для просмотра всех политик для хранилища можно использовать [список политик резервного копирования az.](https://docs.microsoft.com/cli/azure/backup/policy?view=azure-cli-latest#az-backup-policy-list)

В следующем примере приводится политика резервного копирования *schedule2* для общего файла *azurefiles,* присутствующее в учетной записи хранения *afsaccount.*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --name "AzureFileShare;azurefiles" --backup-management-type azurestorage --out table
```

Вы также можете запустить предыдущую команду, используя дружественные имена для контейнера и элемента, предоставив следующие два дополнительных параметра:

* **--резервное копирование-управление типа**: *лазурный хранения*
* **--рабочий тип**нагрузки : *лазурила*

```azurecli-interactive
az backup item set-policy --policy-name schedule2 --name azurefiles --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --name azurefiles --backup-management-type azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Атрибут **имени** в выводе соответствует названию задания, созданного службой резервного копирования для операции политики изменения. Чтобы отследить состояние задания, используйте [задание резервного копирования az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="stop-protection-on-a-file-share"></a>Остановка защиты файлового ресурса

Есть два способа отключить защиту файловых ресурсов Azure:

* Остановите все будущие задания резервного копирования и *удалите* все точки восстановления.
* Остановите все будущие задания резервного копирования, но *оставьте* точки восстановления.

Возможно, это может привести к удорожанию точек восстановления в хранилище, поскольку основные моменты, созданные резервным копированием Azure, будут сохранены. Преимуществом выхода из точек восстановления является возможность восстановления доли файла позже, если вы хотите. Подробную информацию о стоимости выхода из пунктов восстановления можно узнать в [деталях ценообразования.](https://azure.microsoft.com/pricing/details/storage/files) Если вы решите удалить все точки восстановления, вы не сможете восстановить общий доступ файла.

Чтобы остановить защиту для общего файла, определите следующие параметры:

* **--контейнер-имя**: Имя учетной записи хранилища, в котором размещаются общие файлы. Чтобы получить **имя** или **дружественное имя** контейнера, используйте команду [списка резервного копирования АЗ.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--элемент-имя**: Имя файла доля, для которой вы хотите, чтобы остановить защиту. Чтобы получить **имя** или **дружеское имя** резервного элемента, используйте команду [списка резервного копирования az.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

### <a name="stop-protection-and-retain-recovery-points"></a>Остановить защиту и сохранить точки восстановления

Чтобы остановить защиту при сохранении данных, используйте [систему защиты резервного копирования az, отогнанный](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet.

Следующий пример останавливает защиту для общего файла *azurefiles,* но сохраняет все точки восстановления.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --out table
```

Вы также можете запустить предыдущую команду, используя дружественное имя для контейнера и элемента, предоставив следующие два дополнительных параметра:

* **--резервное копирование-управление типа**: *лазурный хранения*
* **--рабочий тип**нагрузки : *лазурила*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
fec6f004-0e35-407f-9928-10a163f123e5  azurefiles
```

Атрибут **имени** в выводе соответствует названию задания, созданного службой резервного копирования для операции защиты стопа. Чтобы отследить состояние задания, используйте [задание резервного копирования az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="stop-protection-without-retaining-recovery-points"></a>Остановить защиту без сохранения точек восстановления

Чтобы остановить защиту, не сохраняя точек восстановления, используйте [систему защиты резервного копирования az, отключающую](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-disable) cmdlet с опцией **удаления-резервного копирования данных,** установленной на **истину.**

Следующий пример останавливает защиту совместного файла *azurefiles* без сохранения точек восстановления.

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name “AzureFileShare;azurefiles” --delete-backup-data true --out table
```

Вы также можете запустить предыдущую команду, используя дружественное имя для контейнера и элемента, предоставив следующие два дополнительных параметра:

* **--резервное копирование-управление типа**: *лазурный хранения*
* **--рабочий тип**нагрузки : *лазурила*

```azurecli-interactive
az backup protection disable --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --delete-backup-data true --out table
```

## <a name="resume-protection-on-a-file-share"></a>Возобновление защиты файлового ресурса

Если вы прекратили защиту совместной акции файлов Azure, но сохранили точки восстановления, вы можете возобновить защиту позже. Если вы не сохраните точки восстановления, вы не можете возобновить защиту.

Чтобы возобновить защиту для общего файла, определите следующие параметры:

* **--контейнер-имя**: Имя учетной записи хранилища, в котором размещаются общие файлы. Чтобы получить **имя** или **дружественное имя** контейнера, используйте команду [списка резервного копирования АЗ.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--имя элемента**: Имя файла, для которого вы хотите возобновить защиту. Чтобы получить **имя** или **дружеское имя** резервного элемента, используйте команду [списка резервного копирования az.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)
* **--имя политики**: Имя политики резервного копирования, для которой вы хотите возобновить защиту для общего файла.

В следующем примере используется [резюме защиты аз-резервного копирования](https://docs.microsoft.com/cli/azure/backup/protection?view=azure-cli-latest#az-backup-protection-resume) cmdlet для возобновления защиты совместного использования файла *azurefiles* с помощью политики резервного копирования *schedule1.*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount” --item-name “AzureFileShare;azurefiles” --policy-name schedule2 --out table
```

Вы также можете запустить предыдущую команду, используя дружественное имя для контейнера и элемента, предоставив следующие два дополнительных параметра:

* **--резервное копирование-управление типа**: *лазурный хранения*
* **--рабочий тип**нагрузки : *лазурила*

```azurecli-interactive
az backup protection resume --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --item-name azurefiles --workload-type azurefileshare --backup-management-type Azurestorage --policy-name schedule2 --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
75115ab0-43b0-4065-8698-55022a234b7f  azurefiles
```

Атрибут **имени** в выводе соответствует названию задания, созданного службой резервного копирования для операции защиты резюме. Чтобы отследить состояние задания, используйте [задание резервного копирования az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="unregister-a-storage-account"></a>Отменить регистрацию учетной записи хранилища

Если вы хотите защитить свои файлы в определенном хранилище с помощью другого хранилища служб восстановления, сначала [защиту остановки для всех файлов в](#stop-protection-on-a-file-share) этом хранилище. Затем отменить регистрацию учетной записи из хранилища служб восстановления, используемого в настоящее время для защиты.

Для регистрации учетной записи необходимо узнать имя контейнера. Чтобы получить **имя** или **дружественное имя** контейнера, используйте команду [списка резервного копирования АЗ.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)

Следующий пример отменяет учет хранения *afsaccount* из *azurefilesvault* с помощью [контейнера резервного копирования аз нерегистрируется](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-unregister) cmdlet.

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --out table
```

Вы также можете запустить предыдущий cmdlet, используя дружественное название для контейнера, предоставив следующий дополнительный параметр:

* **--резервное копирование-управление типа**: *лазурный хранения*

```azurecli-interactive
az backup container unregister --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --out table
```

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации смотрите [резервное копирование файлов Troubleshoot Azure.](troubleshoot-azure-files.md)
