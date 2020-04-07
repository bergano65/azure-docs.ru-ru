---
title: Восстановление акций файлов Azure с помощью Azure CLI
description: Узнайте, как использовать ClI Azure для восстановления резервных акций файлов Azure в хранилище служб восстановления
ms.topic: conceptual
ms.date: 01/16/2020
ms.openlocfilehash: 980044011e3417a2aff8447a939e02299923da38
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80757089"
---
# <a name="restore-azure-file-shares-with-the-azure-cli"></a>Восстановление акций файлов Azure с помощью Azure CLI

Azure CLI предоставляет командную строку для управления ресурсами Azure. Это отличный инструмент для создания пользовательской автоматизации для использования ресурсов Azure. В этой статье объясняется, как восстановить всю общую часть файла или определенные файлы из точки восстановления, созданной [резервным копированием Azure,](https://docs.microsoft.com/azure/backup/backup-overview) с помощью Azure CLI. Эти действия также можно выполнить с помощью [Azure PowerShell](https://docs.microsoft.com/azure/backup/backup-azure-afs-automation) или [портала Azure](backup-afs.md).

К концу этой статьи вы узнаете, как выполнять следующие операции с Azure CLI:

* Просмотр точек восстановления для резервного копирования раздела файла Azure.
* Восстановить полный общий объем файлов Azure.
* Восстановление отдельных файлов или папок.

>[!NOTE]
> Резервное копирование Azure теперь поддерживает восстановление нескольких файлов или папок к исходу или альтернативному местоположению с помощью Azure CLI. Чтобы узнать больше, обратитесь к [разделу «Восстановление нескольких файлов» или папок к исходной или альтернативной](#restore-multiple-files-or-folders-to-original-or-alternate-location) секции местоположения этого документа.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Чтобы установить и использовать интерфейс командной строки локально, необходимо использовать Azure CLI версии 2.0.18 или более поздней. Чтобы получить необходимую версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что у вас уже есть доля файлов Azure, подкрепленная резервным копированием Azure. Если у вас его нет, см. [Резервное копирование файлов Azure с ПОМОЩЬю CLI](backup-afs-cli.md) для настройки резервной копирования для вашей доли файла. Для этой статьи используются следующие ресурсы:

| Общая папка  | Учетная запись хранения | Регион | Сведения                                                      |
| ----------- | --------------- | ------ | ------------------------------------------------------------ |
| *лазуриты*  | *afsaccount*      | EastUS | Исходный источник, резервный копий Azure                 |
| *лазурофилы1* | *afaccount1*      | EastUS | Источник назначения, используемый для альтернативного восстановления местоположения |

Можно использовать аналогичную структуру для файлов, чтобы опробовать различные типы восстановлений, объясняемых в этой статье.

## <a name="fetch-recovery-points-for-the-azure-file-share"></a>Получение точек восстановления для общего файла Azure

Используйте [список точка восстановления резервного копирования az,](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-list) чтобы перечислить все точки восстановления для резервной доли файла.

Ниже приводится список точек восстановления для раздела файла *azurefiles* в учетной записи хранения *afsaccount.*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --backup-management-type azurestorage --item-name "AzureFileShare;azurefiles" --workload-type azurefileshare --out table
```

Вы также можете запустить предыдущий cmdlet, используя дружественное название для контейнера и элемента, предоставив следующие два дополнительных параметра:

* **--резервное копирование-управление типа**: *лазурный хранения*
* **--рабочий тип**нагрузки : *лазурила*

```azurecli-interactive
az backup recoverypoint list --vault-name azurefilesvault --resource-group azurefiles --container-name afsaccount --backup-management-type azurestorage --item-name azurefiles --workload-type azurefileshare --out table
```

Набор результатов представляет собой список точек восстановления со временем и детали согласованности для каждой точки восстановления.

```output
Name                Time                        Consistency
------------------  -------------------------   --------------------
932887541532871865  2020-01-05T07:08:23+00:00   FileSystemConsistent
932885927361238054  2020-01-05T07:08:10+00:00   FileSystemConsistent
932879614553967772  2020-01-04T21:33:04+00:00   FileSystemConsistent
```

Атрибут **имени** в выходе соответствует названию точки восстановления, которая может быть использована в качестве значения для параметра **-rp-name** в операциях восстановления.

## <a name="full-share-recovery-by-using-the-azure-cli"></a>Полное восстановление акций с помощью Azure CLI

Эту опцию восстановления можно использовать для восстановления полной доли файла в исходном или альтернативном местоположении.

Определите следующие параметры для выполнения операций восстановления:

* **--контейнер-имя**: Имя учетной записи хранилища, в котором размещается исходная доля резервного копирования файлов. Чтобы получить имя или дружественное имя контейнера, используйте команду [списка резервного копирования АЗ.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--имя элемента**: Имя резервного исходного файла, которое вы хотите использовать для операции восстановления. Чтобы получить имя или дружеское имя резервного элемента, используйте команду [списка резервного копирования az.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

### <a name="restore-a-full-share-to-the-original-location"></a>Восстановить полную долю в исходном местоположении

При восстановлении исходного местоположения не нужно указывать параметры, связанные с целевыми объектами. Необходимо обеспечить только **разрешение конфликта.**

В следующем примере используется резервное [копирование az restore-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) cmdlet с режимом восстановления, установленным на *исходное размещение* для восстановления доли файла *azurefiles* в исходном местоположении. Вы используете точку восстановления 932883129628959823, которую вы получили в [точках восстановления Fetch для общего файла Azure:](#fetch-recovery-points-for-the-azure-file-share)

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932887541532871865   --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
6a27cc23-9283-4310-9c27-dcfb81b7b4bb  azurefiles
```

Атрибут **имени** в выводе соответствует названию задания, созданного службой резервного копирования для операции восстановления. Чтобы отследить состояние задания, используйте [задание резервного копирования az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="restore-a-full-share-to-an-alternate-location"></a>Восстановление полной доли в альтернативном месте

Эту опцию можно использовать для восстановления общего файла в другом месте и сохранения исходной доли файла. Укажите следующие параметры для восстановления альтернативного местоположения:

* **--целевой-хранилище-аккаунт**: Учетная запись хранилища, к которой восстанавливается резервное содержимое. Целевая учетная запись хранения должна быть в том же расположении, что и хранилище.
* **--целевой файл-доля**: Доля файла в целевой учетной записи хранения, к которой восстанавливается резервное содержимое.
* **--целевой папка**: Папка под файлом общего использования, к которому восстанавливаются данные. Если содержимое резервной копии необходимо восстановить в корневую папку, укажите значения целевой папки в виде пустой строки.
* **--Разрешить-конфликт**: Инструкция, если есть конфликт с восстановленными данными. Принимает **перезаписать** или **пропустить**.

В следующем примере используется резервное [копирование аз-azurefileshare](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefileshare) с режимом восстановления в качестве *альтернативного размещения* для восстановления доли файла *azurefiles* в учетной записи хранения *afsaccount* в долю файла *azurefiles1"* в учетной записи хранения *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefileshare --vault-name azurefilesvault --resource-group azurefiles --rp-name 932883129628959823 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
babeb61c-d73d-4b91-9830-b8bfa83c349a  azurefiles
```

Атрибут **имени** в выводе соответствует названию задания, созданного службой резервного копирования для операции восстановления. Чтобы отследить состояние задания, используйте [задание резервного копирования az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="item-level-recovery"></a>Восстановление на уровне элементов

Эту опцию восстановления можно использовать для восстановления отдельных файлов или папок в исходном или альтернативном месте.

Определите следующие параметры для выполнения операций восстановления:

* **--контейнер-имя**: Имя учетной записи хранилища, в котором размещается исходная доля резервного копирования файлов. Чтобы получить имя или дружественное имя контейнера, используйте команду [списка резервного копирования АЗ.](https://docs.microsoft.com/cli/azure/backup/container?view=azure-cli-latest#az-backup-container-list)
* **--имя элемента**: Имя резервного исходного файла, которое вы хотите использовать для операции восстановления. Чтобы получить имя или дружеское имя резервного элемента, используйте команду [списка резервного копирования az.](https://docs.microsoft.com/cli/azure/backup/item?view=azure-cli-latest#az-backup-item-list)

Укажите следующие параметры для элементов, которые вы хотите восстановить:

* **SourceFilePath**: Абсолютный путь файла, который будет восстановлен в рамках общего файла, как строка. Этот путь является тем же путем, используемым в [загрузке файла хранения az](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-download) или [файле хранения az, показываюм](https://docs.microsoft.com/cli/azure/storage/file?view=azure-cli-latest#az-storage-file-show) команды CLI.
* **SourceFileType**: Выберите ли каталог или файл выбран. Принимает **каталог** или **файл**.
* **ResolveConflict**: Инструкция, если есть конфликт с восстановленными данными. Принимает **перезаписать** или **пропустить**.

### <a name="restore-individual-files-or-folders-to-the-original-location"></a>Восстановление отдельных файлов или папок в исходное местоположение

Используйте [резервное копирование аз-azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) cmdlet с режимом восстановления, установленным в *исходное расположение,* чтобы восстановить определенные файлы или папки в исходное местоположение.

Следующий пример восстанавливает файл *RestoreTest.txt* в исходном месте: общий файл *azurefiles.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore/RestoreTest.txt" --resolve-conflict overwrite  --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Атрибут **имени** в выводе соответствует названию задания, созданного службой резервного копирования для операции восстановления. Чтобы отследить состояние задания, используйте [задание резервного копирования az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

### <a name="restore-individual-files-or-folders-to-an-alternate-location"></a>Восстановление отдельных файлов или папок в альтернативное место

Для восстановления определенных файлов или папок в альтернативном месте используйте резервное [копирование az restore-azurefiles](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurefiles) cmdlet с установленным режимом восстановления в *альтернативное местоположение* и укажите следующие параметры, связанные с целевыми объектами:

* **--целевой-хранилище-аккаунт**: Учетная запись хранилища, к которой восстанавливается резервное содержимое. Целевая учетная запись хранения должна быть в том же расположении, что и хранилище.
* **--целевой файл-доля**: Доля файла в целевой учетной записи хранения, к которой восстанавливается резервное содержимое.
* **--целевой папка**: Папка под файлом общего использования, к которому восстанавливаются данные. Если резервное содержимое будет восстановлено в корневой папке, привезжая значение папки-мишени как пустую строку.

Следующий пример восстанавливает файл *RestoreTest.txt,* первоначально присутствующий в файле *azurefiles,* в другое место: папка *восстановления данных* в файле *azurefiles1,* размещенная в учетной записи хранения *afaccount1.*

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932881556234035474 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode alternatelocation --target-storage-account afaccount1 --target-file-share azurefiles1 --target-folder restoredata --resolve-conflict overwrite --source-file-type file --source-file-path "Restore/RestoreTest.txt" --out table
```

```output
Name                                  ResourceGroup
------------------------------------  ---------------
df4d9024-0dcb-4edc-bf8c-0a3d18a25319  azurefiles
```

Атрибут **имени** в выводе соответствует названию задания, созданного службой резервного копирования для операции восстановления. Чтобы отследить состояние задания, используйте [задание резервного копирования az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

## <a name="restore-multiple-files-or-folders-to-original-or-alternate-location"></a>Восстановление нескольких файлов или папок в исходное или альтернативное местоположение

Чтобы выполнить восстановление нескольких элементов, передайте значение для параметра **исходного файла-пути** в виде **пространстверазделенных** путей всех файлов или папок, которые вы хотите восстановить.

Следующий пример восстанавливает файлы *тестирования Restore.txt* и *AFS Report.docx* в их первоначальном местоположении.

```azurecli-interactive
az backup restore restore-azurefiles --vault-name azurefilesvault --resource-group azurefiles --rp-name 932889937058317910 --container-name "StorageContainer;Storage;AzureFiles;afsaccount" --item-name "AzureFileShare;azurefiles" --restore-mode originallocation  --source-file-type file --source-file-path "Restore Test.txt" "AFS Testing Report.docx" --resolve-conflict overwrite  --out table
```

Результат будет выглядеть примерно так:

```output
Name                                          ResourceGroup
------------------------------------          ---------------
649b0c14-4a94-4945-995a-19e2aace0305          azurefiles
```

Атрибут **имени** в выводе соответствует названию задания, созданного службой резервного копирования для операции восстановления. Чтобы отследить состояние задания, используйте [задание резервного копирования az](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show) cmdlet.

Если требуется восстановить несколько элементов в альтернативном месте, используйте приведенную выше команду, указав параметры, связанные с целевыми объектами, как это объясняется в [отдельных файлах или папках Restore,](#restore-individual-files-or-folders-to-an-alternate-location) в альтернативный раздел местоположения.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как [управлять резервными копированиями файлов Azure с помощью Azure CLI.](manage-afs-backup-cli.md)
