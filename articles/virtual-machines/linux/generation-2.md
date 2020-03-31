---
title: Поддержка Azure для вдвиотных
description: Обзор поддержки Azure для вдвиотных
author: ju-shim
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.topic: article
ms.date: 02/11/2020
ms.author: jushiman
ms.openlocfilehash: b68179caed4df5efd3696d7d6a3739a1e07a3c80
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267304"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Поддержка вдвизирующих в поколение 2 в Azure

Поддержка виртуальных машин поколения 2 (VM) теперь доступна на Azure. Вы не можете изменить поколение виртуальной машины после того, как вы создали его, так что просмотрите соображения на этой странице, прежде чем выбрать поколение.

ВМ поколения 2 поддерживают ключевые функции, которые не поддерживаются в поколении 1 VMs. Эти функции включают увеличенную память, Intel Software Guard Extensions и виртуализированную постоянную память (vPMEM). Поколение 2 VMs работает на месте, имеют некоторые функции, которые не поддерживаются в Azure еще. Для получения дополнительной информации ознакомьтесь с разделом [«Особенности и возможности».](#features-and-capabilities)

Поколение 2 VMs использует новую архитектуру загрузки на основе UEFI, а не архитектуру на основе BIOS, используемую поколения 1 VMs. По сравнению с вм-предсетки поколения 1, в2 поколениях, возможно, улучшилось время загрузки и установки. Для обзора поколения 2 ВМ и некоторые различия между поколением 1 и поколением 2, [см.](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v)

## <a name="generation-2-vm-sizes"></a>Поколение 2 VM размеров

ВМ поколения 1 поддерживаются всеми размерами VM в Azure (за исключением VM-м вибрёв серии Mv2). Теперь Azure предлагает поддержку поколения 2 для следующих выбранных серий VM:

* [Серия B](https://docs.microsoft.com/azure/virtual-machines/linux/b-series-burstable)
* [Серии DC](../dcv2-series.md)
* [DSv2-серия](../dv2-dsv2-series.md) и [Dsv3-серия](../dv3-dsv3-series.md)
* [Серия Esv3](../ev3-esv3-series.md)
* [Fsv2-серия](../fsv2-series.md)
* [Серия GS](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#gs-series)
* [Серия HB](../hb-series.md)
* [Серия HC](../hc-series.md)
* [Ls-серии](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) и [Lsv2-серии](../lsv2-series.md)
* [Серия Mv2](../mv2-series.md)
* [NCv2-серии](../ncv2-series.md) и [NCv3-серии](../ncv3-series.md)
* [Серия ND](../nd-series.md)
* [Серия NVv3](../nvv3-series.md)

> [!NOTE]
> Использование поколения 2 VM-изображений для VMs-серии Mv2, как правило, доступны, так как серия Mv2 работает исключительно с поколением 2 VM-изображений. Поколение 1 VM изображения не поддерживаются на Mv2-серии VMs. 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Поколение 2 VM изображения в Azure Marketplace

Поколение 2 VMs поддерживает следующие изображения Marketplace:

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16.04, 18.04, 19.04, 19.10 
* РЭЛ 8,1, 8,0, 7,7, 7,6, 7,5, 7,4, 7,0
* Цент ОС 8,0, 7,7, 7,6, 7,5, 7,4
* Oracle Linux 7.7, 7.7-CI

## <a name="on-premises-vs-azure-generation-2-vms"></a>На территории против Azure поколения 2 VMs

В настоящее время Azure не поддерживает некоторые функции, которые поддерживает Hyper-V для вдвистого поколения 2.

| Функция Поколения 2                | Локальные серверы Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Безопасная загрузка                         | :heavy_check_mark:  | :x:   |
| Экранированная виртуальная машина:                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| Безопасность на основе виртуализации (VBS) | :heavy_check_mark:  | :x:   |
| Формат VHDX                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Функции и возможности

### <a name="generation-1-vs-generation-2-features"></a>Поколение 1 против функций поколения 2

| Компонент | Поколение 1 | Поколение 2 |
|---------|--------------|--------------|
| Загрузка             | Pcat         | UEFI |
| Дисковые контроллеры | IDE          | SCSI |
| Размеры виртуальной машины         | Все размеры VM | Только VM, поддерживающие премиум-хранилище |

### <a name="generation-1-vs-generation-2-capabilities"></a>Поколение 1 против возможностей поколения 2

| Функция | Поколение 1 | Поколение 2 |
|------------|--------------|--------------|
| OS диск > 2 ТБ                    | :x:                | :heavy_check_mark: |
| Пользовательский диск/изображение/своп-ОС         | :heavy_check_mark: | :heavy_check_mark: |
| Поддержка набора виртуального масштаба машины | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Резервное копирование и восстановление                    | :heavy_check_mark: | :heavy_check_mark: |
| Общая галерея изображений              | :heavy_check_mark: | :heavy_check_mark: |
| Шифрование дисков Azure             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>Создание поколения 2 VM

### <a name="marketplace-image"></a>Изображение рынка

На портале Azure или Azure CLI можно создать 2 визгы из изображения Marketplace, поддерживающего загрузку UEFI.

#### <a name="azure-portal"></a>Портал Azure

Ниже приведены шаги по созданию поколения 2 (Gen2) VM на портале Azure.

1. Войдите на портал Azure по адресу https://portal.azure.com.
1. Выберите **Создать ресурс**.
1. Нажмите **Посмотреть все** из Azure Marketplace слева.
1. Выберите изображение, которое поддерживает Gen2.
1. Нажмите кнопку **Создать**.
1. В вкладке **Advanced** под разделом **поколения VM** выберите опцию **Gen 2.**
1. Во вкладке **"Основы"** в **рамках подробной информации**Under Instance перейдите в **размер** и откройте **лезвие размера VM.**
1. Выберите [поддерживаемое поколение 2 VM.](#generation-2-vm-sizes)
1. Пройдите через [поток создания портала Azure,](quick-create-portal.md) чтобы закончить создание VM.

![Выберите Gen 1 или Gen 2 VM](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

Вы также можете использовать PowerShell для создания VM, напрямую ссылаясь на поколение 1 или поколение 2 SKU.

Например, используйте следующий Смдлет PowerShell, чтобы `WindowsServer` получить список SКУ в предложении.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Кроме того, вы можете использовать Azure CLI, чтобы увидеть все доступные изображения поколения 2, перечисленные **издателем.**

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

Если вы создаете VM с Windows Server 2012 в качестве ОС, то вы выберете либо поколение 1 (BIOS) или поколение 2 (UEFI) VM SKU, которые выглядят следующим образом:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Ознакомьтесь с разделом [«Функции и возможности»](#features-and-capabilities) для текущего списка поддерживаемых изображений Marketplace.

### <a name="managed-image-or-managed-disk"></a>Управляемое изображение или управляемый диск

Вы можете создать поколение 2 VM из управляемого изображения или управляемого диска таким же образом, как вы создали бы поколение 1 VM.

### <a name="virtual-machine-scale-sets"></a>Масштабируемые наборы виртуальных машин

Вы также можете создавать поколения 2 Виртуальных с помощью виртуальных наборов масштаба машины. В Azure CLI используйте наборы масштабов Azure для создания двух визг.

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

* **Доступны ли в 2 раза мизанты поколения во всех регионах Azure?**  
    Да. Но не все [поколения 2 VM размеры](#generation-2-vm-sizes) доступны в каждом регионе. Наличие поколения 2 VM зависит от наличия размера VM.

* **Существует ли разница в цене между поколением 1 и поколением 2 ВМ?**  
    Нет.

* **У меня есть файл .vhd от моего на месте поколения 2 VM. Могу ли я использовать этот файл .vhd для создания поколения 2 VM в Azure?**
  Да, вы можете принести свой файл поколения 2 .vhd в Azure и использовать его для создания поколения 2 VM. Используйте следующие шаги, чтобы сделать это:
    1. Загрузите .vhd на учетную запись хранения в том же регионе, где вы хотите создать свой VM.
    1. Создайте управляемый диск из файла .vhd. Установите свойство Hyper-V Generation на V2. Следующие команды PowerShell устанавливают свойство Hyper-V Generation при создании управляемого диска.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Как только диск будет доступен, создайте VM, прикрепив этот диск. Созданный VM будет поколение2 VM.
    При создании поколения 2 VM можно дополнительно обобщить образ этого VM. Обобщая изображение, вы можете использовать его для создания нескольких VMs.

* **Как увеличить размер диска ОС?**  
  ОС диски больше, чем 2 ТБ являются новыми для поколения 2 ВМ. По умолчанию диски ОС меньше 2 ТБ для ВМ поколения 2. Вы можете увеличить размер диска до рекомендуемого максимума в 4 ТБ. Для увеличения размера диска ОС используйте azure CLI или портал Azure. Для получения информации о том, как расширить диски программно, [см.](expand-disks.md)

  Чтобы увеличить размер диска ОС с портала Azure:

  1. На портале Azure перейдите на страницу свойств VM.
  1. Чтобы выключить и разложить VM, выберите кнопку **"Стоп".**
  1. В разделе **Диски** выберите диск ОС, который вы хотите увеличить.
  1. В разделе **Диски** выберите **Конфигурацию**и обновите **размер** до нужного значения.
  1. Вернитесь на страницу свойств VM и **запустите** VM.

  Вы можете увидеть предупреждение для дисков ОС больше, чем 2 ТБ. Предупреждение не распространяется на визуа 2 ВМ. Тем не менее, размеры диска ОС больше, чем 4 ТБ *не рекомендуется.*

* **Поддерживают ли поколения 2 ВМ ускоренные сети?**  
    Да. Для получения дополнительной информации [см.](../../virtual-network/create-vm-accelerated-networking-cli.md)

* **Поддерживается ли VHDX на 2-м поколении?**  
    Нет, поколение 2 ВМ поддерживает только VHD.

* **Поддерживает ли поколение 2 ВМ-системы Хранилище Azure Ultra Disk?**  
    Да.

* **Могу ли я перенести VM из поколения 1 в поколение 2?**  
    Нет, вы не можете изменить поколение VM после его создания. Если вам нужно переключаться между поколениями VM, создайте новый VM другого поколения.

* **Почему мой размер VM не включен в селекторе размера, когда я пытаюсь создать Gen2 VM?**

    Это может быть решена, делая следующее:

    1. Убедитесь, что свойство **генерации VM** настроено на **Gen 2** во вкладке **Advanced.**
    1. Убедитесь, что вы ищете [размер VM, который поддерживает Gen2 VMs.](#generation-2-vm-sizes)

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте больше о [виртуальных машинах поколения 2 в Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
