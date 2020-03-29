---
title: Загрузить VHD с помощью Azure CLI
description: Узнайте, как загрузить vhd на управляемый диск Azure и скопировать управляемый диск в разных регионах с помощью Azure CLI с помощью прямой загрузки.
services: virtual-machines,storage
author: roygara
ms.author: rogarana
ms.date: 03/13/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: d89a4279d425e4b12e92aae81edfd6c1514c3eef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062671"
---
# <a name="upload-a-vhd-to-azure-using-azure-cli"></a>Загрузите vhd в Azure с помощью Azure CLI

В этой статье объясняется, как загрузить vhd с локальной машины на управляемый диск Azure. Ранее вы должны были следовать более вовлеченный процесс, который включал постановку данных в учетную запись хранения и управление этой учетной записью хранения. Теперь вам больше не нужно управлять учетной записью хранения или этапом данных в нем, чтобы загрузить vhd. Вместо этого вы создаете пустой управляемый диск и загружаете vhd непосредственно на него. Это упрощает загрузку наемных ВМ в Azure и позволяет загружать vhd до 32 TiB непосредственно в большой управляемый диск.

Если вы предоставляете решение для резервного копирования IaaS VMs в Azure, мы рекомендуем вам использовать прямую загрузку для восстановления резервных копирования клиентов на управляемых дисках. Если вы загружаете VHD из внешней машины в Azure, скорость будет зависеть от локальной пропускной способности. Если вы используете Azure VM, то пропускная способность будет такой же, как стандартные HDD.

В настоящее время прямая загрузка поддерживается для стандартных ДИСКов, стандартных SSD и премиальных дисков SSD. Он еще не поддерживается для ультра SSD.

## <a name="prerequisites"></a>Предварительные требования

- Скачать последнюю [версию AzCopy v10](../../storage/common/storage-use-azcopy-v10.md#download-and-install-azcopy).
- [Установите Azure CLI](/cli/azure/install-azure-cli).
- Файл vhd, хранящийся локально
- Если вы собираетесь загрузить vhd из локальных: Vhd фиксированного размера, который [был подготовлен для Azure,](../windows/prepare-for-upload-vhd-image.md)хранится локально.
- Или управляемый диск в Azure, если вы собираетесь выполнить действие копирования.

## <a name="create-an-empty-managed-disk"></a>Создание пустого управляемого диска

Чтобы загрузить vhd в Azure, необходимо создать пустой управляемый диск, настроенный для этого процесса загрузки. Перед созданием, есть некоторые дополнительные сведения, которые вы должны знать об этих дисках.

Этот вид управляемого диска имеет два уникальных состояния:

- ReadToUpload, что означает, что диск готов к загрузке, но, не [защищенная подпись доступа](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) (SAS) не была создана.
- ActiveUpload, что означает, что диск готов к загрузке и SAS был создан.

В то время как в любом из этих состояний, управляемый диск будет выставлен счет по [стандартной цене HDD,](https://azure.microsoft.com/pricing/details/managed-disks/)независимо от фактического типа диска. Например, P10 будет выставлен счет как S10. Это будет верно `revoke-access` до тех пор, пока не будет вызван управляемый диск, который необходим для того, чтобы прикрепить диск к VM.

Прежде чем вы сможете создать пустой стандартный HDD для загрузки, вам нужно иметь размер файла vhd вы хотите загрузить, в байтах. Чтобы получить это, вы `wc -c <yourFileName>.vhd` `ls -al <yourFileName>.vhd`можете использовать либо или . Это значение используется при указании параметра **--загрузить размер-байт.**

Создайте пустой стандартный HDD для загрузки, указав как **параметр --для-загрузки,** так и **параметр --upload-size-байты** на [диске, создающий](/cli/azure/disk#az-disk-create) cmdlet:

```azurecli
az disk create -n mydiskname -g resourcegroupname -l westus2 --for-upload --upload-size-bytes 34359738880 --sku standard_lrs
```

Если вы хотите загрузить либо премиум SSD или стандартный SSD, замените **standard_lrs** **premium_LRS** или **standardssd_lrs.** Ultra SSD пока не поддерживается.

Теперь вы создали пустой управляемый диск, настроенный для процесса загрузки. Чтобы загрузить vhd на диск, вам понадобится пишущее SAS, так что вы можете ссылаться на него в качестве места назначения для загрузки.

Для создания вывешивого SAS вашего пустого управляемого диска используйте следующую команду:

```azurecli
az disk grant-access -n mydiskname -g resourcegroupname --access-level Write --duration-in-seconds 86400
```

Образец возвращенного значения:

```output
{
  "accessSas": "https://md-impexp-t0rdsfgsdfg4.blob.core.windows.net/w2c3mj0ksfgl/abcd?sv=2017-04-17&sr=b&si=600a9281-d39e-4cc3-91d2-923c4a696537&sig=xXaT6mFgf139ycT87CADyFxb%2BnPXBElYirYRlbnJZbs%3D"
}
```

## <a name="upload-vhd"></a>Загрузить vhd

Теперь, когда у вас есть SAS для пустого управляемого диска, вы можете использовать его для установки управляемого диска в качестве пункта назначения для вашей команды загрузки.

Используйте AzCopy v10 для загрузки локального файла VHD на управляемый диск, указав созданный SAS URI.

Эта загрузка имеет ту же пропускную выливку, что и [эквивалентный стандартный HDD.](disks-types.md#standard-hdd) Например, если у вас есть размер, который приравнивается к S4, вы будете иметь пропускную стоимость до 60 MiB/s. Но, если у вас есть размер, который приравнивается к S70, вы будете иметь пропускную стоимость до 500 MiB/s.

```bash
AzCopy.exe copy "c:\somewhere\mydisk.vhd" "sas-URI" --blob-type PageBlob
```

После того, как загрузка завершена, и вам больше не нужно писать больше данных на диск, отозвать SAS. Отмена SAS изменит состояние управляемого диска и позволит прикрепить диск к VM.

```azurecli
az disk revoke-access -n mydiskname -g resourcegroupname
```

## <a name="copy-a-managed-disk"></a>Копирование управляемого диска

Прямая загрузка также упрощает процесс копирования управляемого диска. Вы можете скопировать в пределах одного региона или кросс-региона (в другой регион).

Последующий сценарий сделает это за вас, процесс аналогичен описанным ранее шагам, с некоторыми отличиями, так как вы работаете с существующим диском.

> [!IMPORTANT]
> При предоставлении размера диска в байтах управляемого диска из Azure необходимо добавить смещение 512. Это происходит потому, что Azure опускает колонтитул при возврате размера диска. Копия не удастся, если вы этого не сделаете. Следующий сценарий уже делает это за вас.

Замените `<sourceResourceGroupHere>` `<sourceDiskNameHere>`, `<targetDiskNameHere>` `<targetResourceGroupHere>`, `<yourTargetLocationHere>` , , и (пример значения местоположения будет uswest2) с значениями, а затем запустить следующий сценарий для того, чтобы скопировать управляемый диск.

```azurecli
sourceDiskName = <sourceDiskNameHere>
sourceRG = <sourceResourceGroupHere>
targetDiskName = <targetDiskNameHere>
targetRG = <targetResourceGroupHere>
targetLocale = <yourTargetLocationHere>

sourceDiskSizeBytes= $(az disk show -g $sourceRG -n $sourceDiskName --query '[uniqueId]' -o tsv)

az disk create -g $targetRG -n $targetDiskName -l $targetLocale --for-upload --upload-size-bytes $(($sourceDiskSizeBytes+512)) --sku standard_lrs

targetSASURI = $(az disk grant-access -n $targetDiskName -g $targetRG  --access-level Write --duration-in-seconds 86400 -o tsv)

sourceSASURI=$(az disk grant-access -n $sourceDiskName -g $sourceRG --duration-in-seconds 86400 --query [accessSas] -o tsv)

.\azcopy copy $sourceSASURI $targetSASURI --blob-type PageBlob

az disk revoke-access -n $sourceDiskName -g $sourceRG

az disk revoke-access -n $targetDiskName -g $targetRG
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы успешно загрузили vhd на управляемый диск, вы можете прикрепить диск в качестве [диска данных к существующему VM](add-disk.md) или [прикрепить диск к VM в качестве диска ОС,](upload-vhd.md#create-the-vm)чтобы создать новый VM. 

