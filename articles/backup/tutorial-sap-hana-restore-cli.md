---
title: Руководство по восстановлению базы данных SAP HANA в Azure с помощью интерфейса командной строки (CLI)
description: Из этого руководства вы узнаете, как восстанавливать базы данных SAP HANA, запущенные на виртуальной машине Azure, из хранилища Служб восстановления для Azure Backup с помощью Azure CLI.
ms.topic: tutorial
ms.date: 12/4/2019
ms.openlocfilehash: 6dbe0c4382b648506d853feb281c70a8e8401595
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75470409"
---
# <a name="tutorial-restore-sap-hana-databases-in-an-azure-vm-using-azure-cli"></a>Руководство. Восстановление баз данных SAP HANA на виртуальных машинах Azure с помощью Azure CLI

Azure CLI используется для создания ресурсов Azure и управления ими из командной строки или с помощью скриптов. В этой документации содержатся сведения о восстановлении базы данных SAP HANA из резервной копии на виртуальной машине Azure с помощью Azure CLI. Эти действия можно также выполнить с помощью [портала Azure](https://docs.microsoft.com/azure/backup/sap-hana-db-restore).

Вы можете выполнять команды интерфейса командной строки с помощью [Azure Cloud Shell](tutorial-sap-hana-backup-cli.md).

Изучив это руководство, вы сможете выполнить следующие действия:

> [!div class="checklist"]
>
> * Просмотр точек восстановления для базы данных с резервным копированием.
> * Восстановление базы данных

В этом руководстве предполагается, что у вас есть база данных SAP HANA на виртуальной машине Azure, для которой выполняется резервное копирование с помощью Azure Backup. Если для настройки резервного копирования базы данных SAP HANA вы следовали инструкциям из [руководства по резервному копированию баз данных SAP HANA в Azure c помощью CLI](tutorial-sap-hana-backup-cli.md), вы используете следующие ресурсы:

* группа ресурсов с именем *saphanaResourceGroup*;
* хранилище с именем *saphanaVault*;
* защищенный контейнер с именем *VMAppContainer;Compute;saphanaResourceGroup;saphanaVM*;
* резервная копия базы данных или элемента с именем *saphanadatabase;hxe;hxe*;
* ресурсы в регионе *westus2*.

## <a name="view-restore-points-for-a-backed-up-database"></a>Просмотр точек восстановления для базы данных с резервным копированием

Чтобы просмотреть полный список существующих точек восстановления для базы данных, используйте командлет [az backup recoverypoint list](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain), как показано ниже.

```azurecli-interactive
az backup recoverypoint list --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
   --output table
```

Список точек восстановления будет выглядеть следующим образом.

```output
Name                      Time                               BackupManagementType   Item Name               RecoveryPointType
-------------------       ---------------------------------  ---------------------  ----------------------  ------------------
7660777527047692711       2019-12-10T04:00:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Full
7896624824685666836       2019-12-15T10:33:32.346000+00:00   AzureWorkload          SAPHanaDtabase;hxe;hxe  Differential
DefaultRangeRecoveryPoint                                    AzureWorkload          SAPHanaDtabase;hxe;hxe  Log
```

В приведенном выше списке вы видите три точки восстановления, по одной для полной копии, разностной копии и резервной копии журналов.

>[!NOTE]
>Также с помощью командлета [az backup recoverypoint show-log-chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain) можно просмотреть начальную и конечную точки для каждой полной цепочки резервных копий журналов.

## <a name="prerequisites-to-restore-a-database"></a>Предварительные условия для восстановления базы данных

Перед восстановлением базы данных убедитесь, что выполняются следующие условия.

* Можно восстановить базу данных только на экземпляре SAP HANA, расположенном в том же регионе.
* Целевой экземпляр должен быть зарегистрирован в том же хранилище, что и исходный сервер.
* Azure Backup не может обнаруживать два разных экземпляра SAP HANA на одной виртуальной машине. Это означает, что восстановление данных из одного экземпляра в другой экземпляр на той же виртуальной машине невозможно.

## <a name="restore-a-database"></a>Восстановление базы данных

В Azure Backup можно выполнить восстановление баз данных SAP HANA, запущенных на виртуальных машинах Azure, следующим образом.

* Восстановление до состояния на определенную дату или время (с точностью до секунд) с помощью резервных копий журналов. На основе выбранного времени Azure Backup автоматически определяет соответствующие полные или разностные резервные копии и цепочку резервных копий журналов, необходимых для восстановления.
* Восстановление конкретной полной или разностной резервной копии по определенной точке восстановления.

Чтобы восстановить базу данных, используйте командлет [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl), который принимает в качестве входных данных объект конфигурации восстановления. Этот объект можно создать с помощью командлета [az backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show). Объект конфигурации восстановления содержит все сведения для выполнения восстановления. Среди прочего он определяет режим восстановления: **OriginalWorkloadRestore** или **AlternateWorkloadRestore**.

>[!NOTE]
> **OriginalWorkloadRestore** обозначает восстановление данных в том же экземпляре SAP HANA, где находилась исходная база данных-источник. В этом варианте перезаписывается исходная база данных. <br>
> **AlternateWorkloadRestore** обозначает восстановление базы данных в альтернативное расположение с сохранением исходной базы данных-источника.

## <a name="restore-to-alternate-location"></a>Восстановление в альтернативном расположении

Чтобы восстановить базу данных в альтернативном расположении, используйте режим восстановления **AlternateWorkloadRestore**. Для него нужно выбрать точку восстановления, например последнюю из существующих или любую другую.

В рамках этого руководства восстановление выполняется до последней точки восстановления. [Просмотрите список точек восстановления](#view-restore-points-for-a-backed-up-database) для базы данных и выберите наиболее подходящую. В рамках этого руководства используется точка восстановления с именем *7660777527047692711*.

Используя указанные выше имя точки восстановления и режим восстановления, создайте объект конфигурации восстановления, используя командлет [az backup recoveryconfig show](https://docs.microsoft.com/cli/azure/backup/recoveryconfig?view=azure-cli-latest#az-backup-recoveryconfig-show). Давайте узнаем, что означают остальные параметры в этом командлете.

* **--target-item-name** содержит имя, которое будет использоваться для восстанавливаемой базы данных. В нашем примере используется имя *restored_database*.
* **--target-server-name** обозначает имя сервера SAP HANA, который зарегистрирован в хранилище служб восстановления и размещается в том же регионе, что и восстанавливаемая база данных. В нашем примере мы восстановим базу данных на том же сервере SAP HANA, который был защищен резервным копированием, с именем *hxehost*.
* **--target-server-type** должен иметь значение **SapHanaDatabase** для восстановления баз данных SAP HANA.

```azurecli-interactive

az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode AlternateWorkloadRestore \
    --rp-name 7660777527047692711 \
    --target-item-name restored_database \
    --target-server-name hxehost \
    --target-server-type HANAInstance \
    --workload-type SAPHANA \
    --output json
```

В ответ на приведенный выше запрос вы получите примерно такой объект конфигурации восстановления.

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Теперь выполните командлет [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) для восстановления базы данных. Чтобы использовать эту команду, мы предоставим приведенный выше код в формате JSON, сохраненный в файл с именем *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

Выходные данные будут выглядеть следующим образом:

```output
Name                                  Resource
------------------------------------  -------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

В ответе вы получите имя задания. Это имя задания позволяет отслеживать состояние задания с помощью [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show).

## <a name="restore-and-overwrite"></a>Восстановление и перезапись

Чтобы восстановить базу данных в исходном расположении, мы укажем режим восстановления **OrignialWorkloadRestore**. Для него нужно выбрать точку восстановления, например последнюю из существующих или любую другую.

В этом руководстве мы выберем последнюю точку восстановления на момент времени "28-11-2019-09:53:00". Эту точку восстановления можно указать в следующих форматах: дд-мм-гггг, дд-мм-гггг-чч:мм:сс. Чтобы выбрать допустимый момент времени для восстановления, используйте командлет [az backup recoverypoint show-log-chain](https://docs.microsoft.com/cli/azure/backup/recoverypoint?view=azure-cli-latest#az-backup-recoverypoint-show-log-chain), который позволяет получить интервалы полных цепочек резервных копий журналов.

```azurecli-interactive
az backup recoveryconfig show --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --container-name VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \
    --item-name saphanadatabase;hxe;hxe \
    --restore-mode OriginalWorkloadRestore \
    --log-point-in-time 28-11-2019-09:53:00 \
    --output json
```

В ответ на приведенный выше запрос вы получите примерно такой объект конфигурации восстановления.

```output
"{\"restore_mode\": \"OriginalLocation\", \"container_uri\": \" VMAppContainer;Compute;saphanaResourceGroup;saphanaVM \", \"item_uri\": \"SAPHanaDatabase;hxe;hxe\", \"recovery_point_id\": \"DefaultRangeRecoveryPoint\", \"log_point_in_time\": \"28-11-2019-09:53:00\", \"item_type\": \"SAPHana\", \"source_resource_id\": \"/subscriptions/ef4ab5a7-c2c0-4304-af80-af49f48af3d1/resourceGroups/saphanaResourceGroup/providers/Microsoft.Compute/virtualMachines/saphanavm\", \"database_name\": null, \"container_id\": null, \"alternate_directory_paths\": null}"
```

Теперь выполните командлет [az restore restore-azurewl](https://docs.microsoft.com/cli/azure/backup/restore?view=azure-cli-latest#az-backup-restore-restore-azurewl) для восстановления базы данных. Чтобы использовать эту команду, мы предоставим приведенный выше код в формате JSON, сохраненный в файл с именем *recoveryconfig.json*.

```azurecli-interactive
az backup restore restore-azurewl --resource-group saphanaResourceGroup \
    --vault-name saphanaVault \
    --restore-config recoveryconfig.json \
    --output table
```

Выходные данные будут выглядеть следующим образом:

```output
Name                                  Resource
------------------------------------  --------
5b198508-9712-43df-844b-977e5dfc30ea  SAPHANA
```

В ответе вы получите имя задания. Это имя можно использовать для отслеживания состояния задания с помощью командлета [az backup job show](https://docs.microsoft.com/cli/azure/backup/job?view=azure-cli-latest#az-backup-job-show).

## <a name="next-steps"></a>Дальнейшие действия

* Чтобы узнать, как управлять базами данных SAP HANA, для которых выполняется резервное копирование с помощью Azure CLI, перейдите к [этому руководству](tutorial-sap-hana-backup-cli.md).

* Сведения о восстановлении базы данных SAP HANA, работающей на виртуальной машине Azure, с помощью портал Azure см. в [этой статье](https://docs.microsoft.com/azure/backup/sap-hana-db-restore).
