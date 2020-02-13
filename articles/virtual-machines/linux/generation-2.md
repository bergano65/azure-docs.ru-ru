---
title: Поддержка Azure для виртуальных машин поколения 2
description: Обзор поддержки Azure для виртуальных машин поколения 2
services: virtual-machines-linux
documentationcenter: ''
author: ju-shim
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.topic: article
ms.date: 02/11/2020
ms.author: jushiman
ms.openlocfilehash: 70b13b16e6aca6b0bdb0858a32a219defef6cca3
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162044"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Поддержка виртуальных машин поколения 2 в Azure

Поддержка виртуальных машин версии 2 теперь доступна в Azure. Вы не сможете изменить поколение виртуальных машин после ее создания, поэтому перед тем как выбрать поколение, ознакомьтесь с вопросами на этой странице.

Виртуальные машины поколения 2 поддерживают ключевые функции, которые не поддерживаются в виртуальных машинах поколения 1. Эти функции включают в себя увеличенную память, расширения Intel Software Guard (Intel SGX) и виртуализированную постоянную память (Впмем). Виртуальные машины поколения 2, работающие локально, имеют некоторые функции, которые еще не поддерживаются в Azure. Дополнительные сведения см. в разделе [функции и возможности](#features-and-capabilities) .

Виртуальные машины поколения 2 используют новую архитектуру загрузки на основе UEFI, а не архитектуру на основе BIOS, используемую виртуальными машинами поколения 1. По сравнению с виртуальными машинами поколения 1, виртуальные машины поколения 2 могут улучшить загрузку и время установки. Общие сведения о виртуальных машинах поколения 2 и некоторых различиях между поколением 1 и поколением 2 см. [в статье Создание виртуальной машины поколения 1 или 2 в Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Размеры виртуальных машин поколения 2

Виртуальные машины поколения 1 поддерживаются всеми размерами виртуальных машин в Azure (за исключением виртуальных машин серии Mv2). Теперь Azure предлагает поддержку поколения 2 для следующей выбранной серии виртуальных машин:

* [Серия B](https://docs.microsoft.com/azure/virtual-machines/linux/b-series-burstable)
* [Серия DC](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dc-series)
* Серии [Dsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series) и [Dsv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Серия Esv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Серия серия fsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [Серия GS](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#gs-series)
* [Серия ХБ](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#hb-series)
* [Серия HC](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-hpc#hc-series)
* Серии [Ls](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) и [Lsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Серия Mv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)
* Серии [NCv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#ncv2-series) и [NCv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#ncv3-series)
* [Серия ND](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nd-series)
* [Серия NVv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-gpu#nvv3-series--1)

> [!NOTE]
> Использование образов виртуальных машин поколения 2 для виртуальных машин серии Mv2 общедоступно, так как серия Mv2 работает исключительно с образами виртуальных машин поколения 2. Образы виртуальных машин поколения 1 не поддерживаются на виртуальных машинах серии Mv2. 

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Образы виртуальных машин поколения 2 в Azure Marketplace

Виртуальные машины поколения 2 поддерживают следующие образы Marketplace:

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10
* SUSE Linux Enterprise Server 15 с пакетом обновления 1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16,04, 18,04, 19,04, 19,10 
* RHEL 8,0, 7,6, 7,5, 7,4, 7,0
* Цент ОС 8,0

## <a name="on-premises-vs-azure-generation-2-vms"></a>Локальная среда и виртуальные машины поколения 2 для Azure

В настоящее время Azure не поддерживает некоторые функции, поддерживаемые локальными Hyper-V для виртуальных машин поколения 2.

| Поколение 2, функция                | Локальная версия Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Безопасная загрузка                         | :heavy_check_mark:  | :x:   |
| Экранированная виртуальная машина                         | :heavy_check_mark:  | :x:   |
| vTPM                                | :heavy_check_mark:  | :x:   |
| Безопасность на основе виртуализации (VBS) | :heavy_check_mark:  | :x:   |
| Формат VHDX                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Функции и возможности

### <a name="generation-1-vs-generation-2-features"></a>Функции поколения 1 и поколения 2

| Компонент | Поколение 1 | Поколение 2 |
|---------|--------------|--------------|
| Загрузка             | пкат         | UEFI |
| Контроллеры дисков | ИНТЕРФЕЙС          | ИНТЕРФЕЙСА |
| Размеры виртуальной машины         | Все размеры виртуальных машин | Только виртуальные машины, поддерживающие хранилище класса Premium |

### <a name="generation-1-vs-generation-2-capabilities"></a>Возможности поколения 1 и поколения 2

| Функция | Поколение 1 | Поколение 2 |
|------------|--------------|--------------|
| Диск ОС > 2 ТБ                    | :x:                | :heavy_check_mark: |
| Пользовательский диск, образ или ОС замены         | :heavy_check_mark: | :heavy_check_mark: |
| Поддержка масштабируемого набора виртуальных машин | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Резервное копирование и восстановление                    | :heavy_check_mark: | :heavy_check_mark: |
| Коллекция общих образов              | :heavy_check_mark: | :heavy_check_mark: |
| Шифрование дисков Azure             | :heavy_check_mark: | :x:                |

## <a name="creating-a-generation-2-vm"></a>Создание виртуальной машины поколения 2

### <a name="marketplace-image"></a>Образ Marketplace

В портал Azure или Azure CLI можно создать виртуальные машины поколения 2 из образа Marketplace, который поддерживает загрузку UEFI.

#### <a name="azure-portal"></a>Портал Azure

Ниже приведены шаги по созданию виртуальной машины поколения 2 (Gen2) в портал Azure.

1. Войдите на портал Azure по адресу https://portal.azure.com.
1. Выберите **Создать ресурс**.
1. Щелкните **Просмотреть все** в Azure Marketplace слева.
1. Выберите образ, который поддерживает Gen2.
1. Нажмите кнопку **Создать**.
1. На вкладке **Дополнительно** в разделе **Создание виртуальной машины** выберите параметр **Gen 2** .
1. На вкладке " **основные** " в разделе " **сведения об экземпляре**" выберите **Размер** и откройте колонку **Выбор размера виртуальной машины** .
1. Выберите [поддерживаемую виртуальную машину поколения 2](#generation-2-vm-sizes).
1. Просмотрите [последовательность создания портал Azure](quick-create-portal.md) , чтобы завершить создание виртуальной машины.

![Выбор виртуальной машины Gen 1 или Gen 2](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

Вы также можете создать виртуальную машину с помощью PowerShell, обратившись непосредственно к SKU поколения 1 или поколения 2.

Например, используйте следующий командлет PowerShell, чтобы получить список номеров SKU в предложении `WindowsServer`.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Кроме того, можно использовать Azure CLI, чтобы просмотреть все доступные образы поколения 2, указанные **издателем**.

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

Если вы создаете виртуальную машину с Windows Server 2012 в качестве ОС, выберите номер SKU виртуальной машины поколения 1 (BIOS) или поколения 2 (UEFI), который будет выглядеть следующим образом:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Текущий список поддерживаемых образов Marketplace см. в разделе [функции и возможности](#features-and-capabilities) .

### <a name="managed-image-or-managed-disk"></a>Управляемый образ или управляемый диск

Вы можете создать виртуальную машину поколения 2 из управляемого образа или управляемого диска таким же образом, как и виртуальная машина поколения 1.

### <a name="virtual-machine-scale-sets"></a>Масштабируемые наборы виртуальных машин

Вы также можете создать виртуальные машины поколения 2 с помощью масштабируемых наборов виртуальных машин. В Azure CLI используйте масштабируемые наборы Azure для создания виртуальных машин поколения 2.

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

* **Доступны ли виртуальные машины поколения 2 во всех регионах Azure?**  
    Да. Но не все [размеры виртуальных машин поколения 2](#generation-2-vm-sizes) доступны в каждом регионе. Доступность виртуальной машины поколения 2 зависит от доступности размера виртуальной машины.

* **Есть ли разница в ценах между виртуальными машинами поколения 1 и 2?**  
    Нет.

* **У меня есть VHD-файл из локальной виртуальной машины поколения 2. Можно ли использовать этот VHD-файл для создания виртуальной машины поколения 2 в Azure?**
  Да, вы можете перенести VHD-файл версии 2 в Azure и использовать его для создания виртуальной машины поколения 2. Для этого выполните следующие действия.
    1. Отправьте VHD-файл в учетную запись хранения в том же регионе, где вы хотите создать виртуальную машину.
    1. Создайте управляемый диск из VHD-файла. Задайте для свойства создания Hyper-V значение v2. Следующие команды PowerShell задают свойство создания Hyper-V при создании управляемого диска.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. После того как диск будет доступен, создайте виртуальную машину, присоединив этот диск. Созданная виртуальная машина будет виртуальной машиной поколения 2.
    При создании виртуальной машины поколения 2 можно при необходимости обобщить образ этой виртуальной машины. Обобщение образа, вы можете использовать его для создания нескольких виртуальных машин.

* **Разделы справки увеличить размер диска ОС?**  
  Диски ОС размером более 2 ТБ являются новыми для виртуальных машин поколения 2. По умолчанию диски ОС имеют размер менее 2 ТБ для виртуальных машин поколения 2. Размер диска можно увеличить до рекомендуемого максимума в 4 ТБ. Чтобы увеличить размер диска ОС, используйте Azure CLI или портал Azure. Сведения о том, как программно расширять диски, см. [в разделе изменение размера диска](expand-disks.md).

  Чтобы увеличить размер диска ОС с портал Azure:

  1. В портал Azure перейдите на страницу свойств виртуальной машины.
  1. Чтобы завершить работу виртуальной машины и освободить ее, нажмите кнопку **остановить** .
  1. В разделе **диски** выберите диск операционной системы, который требуется увеличить.
  1. В разделе **диски** выберите **Конфигурация**и обновите **Размер** до нужного значения.
  1. Вернитесь на страницу свойств виртуальной машины и **ЗАПУСТИТЕ** виртуальную машину.

  Может появиться предупреждение для дисков ОС размером более 2 ТБ. Предупреждение не применяется к виртуальным машинам поколения 2. Однако *не рекомендуется использовать* размеры дисков ОС размером более 4 ТБ.

* **Поддерживают ли виртуальные машины поколения 2 возможности ускорения работы в сети?**  
    Да. Дополнительные сведения см. [в статье Создание виртуальной машины с ускоренной сетью](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **Поддерживается ли VHDX в поколении 2?**  
    Нет, виртуальные машины поколения 2 поддерживают только виртуальный жесткий диск.

* **Поддерживают ли виртуальные машины поколения 2 Azure Ultra Хранилище дисков?**  
    Да.

* **Можно ли перенести виртуальную машину из поколения 1 в поколение 2?**  
    Нет, вы не сможете изменить создание виртуальной машины после ее создания. Если необходимо переключиться между поколениями виртуальных машин, создайте новую виртуальную машину другого поколения.

* **Почему размер виртуальной машины не включен в селекторе размера при попытке создать виртуальную машину Gen2?**

    Это можно устранить, выполнив следующие действия.

    1. Убедитесь, что для свойства **Создание виртуальной машины** задано значение **Gen 2** на вкладке **Дополнительно** .
    1. Убедитесь, что вы ищете [Размер виртуальной машины, который поддерживает виртуальные машины Gen2](#generation-2-vm-sizes).

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о [виртуальных машинах версии 2 в Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
