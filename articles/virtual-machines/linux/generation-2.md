---
title: Виртуальные машины поколения 2 (Предварительная версия) в Azure | Документация Майкрософт
description: Обзор виртуальных машин Azure версии 2
services: virtual-machines-linux
documentationcenter: ''
author: laurenhughes
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: lahugh
ms.openlocfilehash: e6bb947503371e379e4d4972ddfc3614e129174b
ms.sourcegitcommit: 3ced637c8f1f24256dd6ac8e180fff62a444b03c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/17/2019
ms.locfileid: "65835213"
---
# <a name="generation-2-vms-preview-on-azure"></a>Виртуальные машины поколения 2 (Предварительная версия) в Azure

> [!IMPORTANT]
> Виртуальные машины второго поколения сейчас доступны в общедоступной предварительной версии.
> Эта предварительная версия предоставляется без соглашения об уровне обслуживания и не рекомендована для использования рабочей среде. Некоторые функции могут не поддерживаться или их возможности могут быть ограничены.
> Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Поддержка для поколения 2 виртуальных машин (ВМ) теперь доступна в общедоступной предварительной версии в Azure. Создание виртуальной машины после ее создания изменить нельзя. Таким образом, мы рекомендуем, ознакомьтесь с вопросами [здесь](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v) а также укажите сведения на этой странице, прежде чем выбрать поколение.

Виртуальные машины поколения 2: поддержка основные функции, такие как: увеличения памяти, Intel® Software Guard Extensions (SGX) и виртуальных постоянной памяти (vPMEM), которые не поддерживаются на виртуальных машинах поколения 1. Виртуальные машины второго поколения имеют некоторые функции, которые еще не поддерживаются в Azure. Дополнительные сведения см. в разделе [функции и возможности](#features-and-capabilities) раздел. 

Виртуальные машины второго поколения использования новой архитектуры vs загрузки на основе UEFI архитектура на основе BIOS, используемых виртуальными машинами поколения 1. По сравнению с виртуальных машин поколения 1, виртуальные машины поколения 2 могут улучшили время загрузки и установки. Обзор виртуальных машин поколения 2 и некоторые основные различия между поколения 1 и поколения 2 см. в разделе [следует ли создавать виртуальные машины поколения 1 или 2 в Hyper-V?](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>Размеры виртуальных Машин поколения 2

Виртуальные машины поколения 1, поддерживаются все размеры виртуальных Машин в Azure. Azure предлагает поддержку поколения 2 ниже выбранного ряда виртуальных Машин в общедоступной предварительной версии:

* [Dsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv2-series) и [серии Dsv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dsv3-series-1)
* [Серии Esv3](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#esv3-series)
* [Серия Fsv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-compute#fsv2-series-1)
* [Серия GS](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#gs-series)
* [Серия ls —](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-previous-gen#ls-series) и [Lsv2 серии](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-storage#lsv2-series)
* [Серии Mv2](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#mv2-series)

## <a name="generation-2-vm-images-in-azure-marketplace"></a>Образы виртуальных Машин поколения 2 в Azure Marketplace

Виртуальные машины поколения 2 поддерживают следующие образы Azure Marketplace:

* Windows server 2019 центра обработки данных
* Windows server 2016 Datacenter
* Windows server 2012 R2 Datacenter
* Windows server 2012 Datacenter

## <a name="on-premises-vs-azure-generation-2-vms"></a>Vs локальных виртуальных машин Azure версии 2

Azure не поддерживает некоторые функции, которые on-premises Hyper-V поддерживает для виртуальных машин поколения 2.

| Функция поколения 2                | On-premises Hyper-V | Таблицы Azure |
|-------------------------------------|---------------------|-------|
| Безопасная загрузка                         | :heavy_check_mark:  | :x:   |
| Экранированной виртуальной Машины                         | :heavy_check_mark:  | :x:   |
| виртуального доверенного платформенного модуля                                | :heavy_check_mark:  | :x:   |
| Безопасность на основе виртуализации (VBS) | :heavy_check_mark:  | :x:   |
| Формат VHDX                         | :heavy_check_mark:  | :x:   |

## <a name="features-and-capabilities"></a>Функции и возможности

### <a name="generation-1-vs-generation-2-features"></a>Особенности поколения 2 vs поколения 1

| Компонент | Поколение 1 | Поколения 2 |
|---------|--------------|--------------|
| Загрузка             | PCAT                      | UEFI                               |
| Контроллеры дисков | IDE                       | SCSI                               |
| Размеры виртуальных машин         | На все размеры виртуальных Машин | Хранилище класса Premium виртуальные машины, поддерживаемые только |

### <a name="generation-1-vs-generation-2-capabilities"></a>Возможности поколения 2 vs поколения 1

| Функция | Поколение 1 | Поколения 2 |
|------------|--------------|--------------|
| > 2 ТБ на диске ОС                    | :x:                        | :heavy_check_mark: |
| Пользовательские переключения/дисков/образа ОС         | :heavy_check_mark:         | :heavy_check_mark: |
| Поддержка набора масштабирования виртуальных машин | :heavy_check_mark:         | :heavy_check_mark: |
| ASR или резервного копирования                        | :heavy_check_mark:         | :x:                |
| Общедоступная коллекция образов              | :heavy_check_mark:         | :x:                |
| Шифрование диска Azure             | :heavy_check_mark:         | :x:                |

## <a name="creating-a-generation-2-vm"></a>Создание поколения 2 виртуальной Машины

### <a name="marketplace-image"></a>Образ Marketplace

Виртуальные машины поколения 2 могут создаваться из образа marketplace (который поддерживает загрузку UEFI) с помощью портала Azure или Azure CLI.

`windowsserver-gen2preview` Предложение содержит Windows только для образов поколения 2. Это позволяет избежать путаницы в отношении поколения 1 и поколения 2 изображения. Чтобы создать две виртуальные машины поколения, выберите **образы** из этого предложения и выполните стандартный процесс создания виртуальной Машины.

В настоящее время следующие Windows поколения 2 изображения публикуются в Azure Marketplace:

* 2019 центра обработки данных уровня gen2.
* 2016-datacenter-gen2
* 2012-r2-datacenter-2-го поколения
* 2012-datacenter-gen2

См. в разделе возможностей списка поддерживаемых marketplace образов, как мы продолжим добавлять дополнительные образы, которые поддерживают поколения 2.

### <a name="managed-image-or-managed-disk"></a>Управляемый образ или управляемый диск

Две виртуальные машины могут создаваться из управляемого образа или управляемый диск таким же образом, необходимо создать первого поколения виртуальных Машин.

### <a name="virtual-machine-scale-sets"></a>наборы для масштабирования виртуальных машин

Поколения 2 виртуальные машины также могут создаваться с использованием масштабируемых наборов виртуальных машин. Вы можете создать поколения 2 виртуальных машин, с помощью наборов масштабирования виртуальных машин Azure с помощью Azure CLI.

## <a name="frequently-asked-questions"></a>Часто задаваемые вопросы

* **Виртуальные машины второго поколения поддерживают ускоренной сетью?**  
    Да, поддержка виртуальных машин поколения 2 [ускоренной сети](../../virtual-network/create-vm-accelerated-networking-cli.md).

* **Поддерживается ли .vhdx для поколения 2?**  
    Нет, только VHD-файла поддерживается на виртуальных машинах поколения 2.

* **Поддерживают ли виртуальные машины второго поколения Ultra твердотельных накопителей (SSD)?**  
    Да, виртуальные машины второго поколения поддерживают Ultra SSD.

* **Можно ли перенести из поколения 1 в виртуальные машины поколения 2?**  
    Нет, не может изменить поколение виртуальной Машины после ее создания. Если необходимо переключиться между поколениями виртуальной Машины, необходимо создать новую виртуальную Машину различные поколения.

## <a name="next-steps"></a>Дальнейшие действия

* Дополнительные сведения о [виртуальные машины поколения 2 в Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).