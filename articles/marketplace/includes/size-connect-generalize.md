---
title: включить файл
description: файл
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 6c7536e38a0d2cf7d4e906947aff645c74e459c0
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96026370"
---
## <a name="generalize-the-image"></a>Обобщение образа

Все образы в Azure Marketplace должны быть доступны для повторного использования в первоначальном виде. Для этой цели виртуальный жесткий диск операционной системы необходимо подготовить (обобщить). Эта операция удаляет с виртуальной машины все идентификаторы, относящиеся к определенному экземпляру, и программные драйверы.

### <a name="for-windows"></a>Для Windows

Диски ОС Windows обобщены с помощью средства [Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) . Если позже вы обновите или перенастройте ОС, необходимо снова запустить Sysprep.

> [!WARNING]
> После запуска программы Sysprep выключите виртуальную машину, пока она не будет развернута, так как обновления могут запускаться автоматически. Это завершение работы не позволит последующим обновлениям вносить в операционную систему или установленные службы изменения, относящиеся к конкретному экземпляру. Дополнительные сведения о запуске sysprep см. в разделе о [действиях по обобщению VHD](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Для Linux

Следующий процесс обобщает виртуальную машину Linux и повторно развертывает ее как отдельную виртуальную машину. Дополнительные сведения см. на странице [Создание управляемого образа виртуальной машины или виртуального жесткого диска](../../virtual-machines/linux/capture-image.md). При достижении раздела «Создание виртуальной машины из образа для записи» можно прерывать работу.

1. Удалите агент Linux для Azure.
    1. Подключитесь к виртуальной машине Linux c помощью клиента SSH.
    2. В окне SSH введите следующую команду: `sudo waagent –deprovision+user` .
    3. Для продолжения введите Y (можно добавить параметр -force в предыдущую команду, чтобы предотвратить появление запроса на подтверждение).
    4. После выполнения команды введите **Exit** , чтобы закрыть SSH-клиент.
2. Останавливает виртуальную машину.
    1. На портале Azure выберите нужную группу ресурсов (RG) и отмените распределение виртуальной машины.
    2. Теперь ваша виртуальная машина является обобщенной, и вы можете создать новую виртуальную машину с помощью этого диска виртуальной машины.

### <a name="take-a-snapshot-of-the-vm-disk"></a>Создание моментального снимка диска виртуальной машины

1. Войдите на [портал Azure](https://ms.portal.azure.com/).
2. Начиная с верхнего левого угла, выберите **создать ресурс**, а затем найдите и выберите **снимок**.
3. В колонке моментальный снимок выберите  **создать**.
4. Заполните поле **Имя** для моментального снимка.
5. Выберите существующую группу ресурсов или введите имя для новой.
6. В поле **Исходный диск** выберите управляемый диск, моментальный снимок которого необходимо создать.
7. Выберите **тип учетной записи**, которая будет использоваться для хранения моментального снимка. Используйте **диск HDD ценовой категории "Стандартный"**, если вам не нужно хранить моментальный снимок на высокопроизводительном диске SSD.
8. Выберите **Создать**.

#### <a name="extract-the-vhd"></a>Извлечение виртуального жесткого диска

Используйте следующий скрипт для экспорта моментального снимка в виртуальный жесткий диск в вашей учетной записи хранения.

```JSON
#Provide the subscription Id where the snapshot is created
subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
resourceGroupName=myResourceGroupName

#Provide the snapshot name
snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. 
storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

#### <a name="script-explanation"></a>Описание скрипта

Этот скрипт использует следующие команды для создания URI SAS для моментального снимка и копирует базовый виртуальный жесткий диск в учетную запись хранения с помощью URI SAS. Для каждой команды в таблице приведены ссылки на соответствующую документацию.

| Get-Help | Примечания |
| --- | --- |
| az disk grant-access | Создает SAS только для чтения, который используется для копирования базового VHD-файла в учетную запись хранения или загрузки в локальную среду
| az storage blob copy start | Асинхронно копирует большой двоичный объект из одной учетной записи хранения в другую. Используйте `az storage blob show` для проверки состояния нового большого двоичного объекта. |
|
