---
title: Изменение производительности управляемых дисков Azure
description: Сведения о уровнях производительности для управляемых дисков, а также об изменении уровней производительности для существующих управляемых дисков.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 7da500c3f18b7bf7057b0c5875bc9b39136a6483
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/27/2020
ms.locfileid: "91396592"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Уровни производительности для управляемых дисков (Предварительная версия)

В настоящее время Хранилище дисков Azure предлагает встроенные возможности для достижения более высокой производительности при обработке краткосрочного непредвиденного трафика. Расширенные твердотельные накопители обеспечивают гибкость при увеличении производительности диска без увеличения фактического размера диска, что позволяет сопоставить потребности рабочей нагрузки и снизить затраты. Сейчас эта функция доступна в предварительной версии. Это идеальный вариант для событий, временно требующих более высокого уровня производительности, например праздничных покупок, тестирования производительности или запуска среды обучения. Для решения этих событий можно выбрать более высокий уровень производительности, если это необходимо, и вернуться к исходному уровню, когда больше не требуется дополнительная производительность.

## <a name="how-it-works"></a>Принцип работы

При первом развертывании или подготовке диска базовый уровень производительности для этого диска задается в зависимости от размера подготовленного диска. Можно выбрать более высокий уровень производительности, чтобы удовлетворить более высокие требования, и если эта производительность больше не нужна, можно вернуться к исходному базовому уровню производительности.

Изменения в оплате по мере изменения уровня. Например, если вы подготавливаете диск P10 (128 гиб), базовый уровень производительности задается как P10 (500 операций ввода-вывода в секунду и 100 МБ/с), и счет будет взиматься по тарифу P10. Уровень можно обновить в соответствии с производительностью P50 (7500 операций ввода-вывода в секунду и 250 МБ/с) без увеличения размера диска, в течение которого будет взиматься плата по тарифу P50. Если более высокая производительность больше не нужна, вы можете вернуться на уровень P10, и на диск будет взиматься плата по тарифу P10.

| Размер диска | Базовый уровень производительности | Можно обновить до |
|----------------|-----|-------------------------------------|
| 4 гиб | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 гиб | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 гиб | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32 ГиБ | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64 ГиБ | P6 | P10, P15, P20, P30, P40, P50 |
| 128 ГиБ | P10 | P15, P20, P30, P40, P50 |
| 256 Гиб | P15 | P20, P30, P40, P50 |
| 512 ГиБ | P20 | P30, P40, P50 |
| 1 ТиБ | P30 | P40, P50 |
| 2 ТиБ | P40 | P50 |
| 4 ТиБ | P50 | None |
| 8 ТиБ | P60 |  P70, P80 |
| 16 ТиБ | P70 | P80 |
| 32 ТиБ | P80 | None |

Сведения о выставлении счетов см. в разделе [цены на управляемые диски](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Ограничения

- Сейчас поддерживается только для твердотельных накопителей уровня "Премиум".
- Диски должны быть отсоединены от работающей виртуальной машины перед изменением уровней.
- Использование уровней производительности P60, P70 и P80 ограничено дисками 4096 гиб и выше.
- Уровень производительности дисков можно изменить каждые 24 часа.

## <a name="regional-availability"></a>Доступность по регионам

Настройка уровня производительности управляемого диска сейчас доступна только для твердотельных накопителей класса Premium в следующих регионах:

- центрально-западная часть США 

## <a name="create-an-empty-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Создание пустого диска данных с уровнем выше базового уровня

```azurecli
subscriptionId=<yourSubscriptionIDHere>
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
diskSize=<yourDiskSizeHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus

az login

az account set --subscription $subscriptionId

az disk create -n $diskName -g $resourceGroupName -l $region --sku Premium_LRS --size-gb $diskSize --tier $performanceTier
```
## <a name="create-an-os-disk-with-a-tier-higher-than-the-baseline-tier-from-an-azure-marketplace-image"></a>Создание диска ОС с уровнем выше базового уровня из образа Azure Marketplace

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>
region=westcentralus
image=Canonical:UbuntuServer:18.04-LTS:18.04.202002180

az disk create -n $diskName -g $resourceGroupName -l $region --image-reference $image --sku Premium_LRS --tier $performanceTier
```
     
## <a name="update-the-tier-of-a-disk"></a>Обновление уровня диска

```azurecli
resourceGroupName=<yourResourceGroupNameHere>
diskName=<yourDiskNameHere>
performanceTier=<yourDesiredPerformanceTier>

az disk update -n $diskName -g $resourceGroupName --set tier=$performanceTier
```
## <a name="show-the-tier-of-a-disk"></a>Отображение уровня диска

```azurecli
az disk show -n $diskName -g $resourceGroupName --query [tier] -o tsv
```

## <a name="next-steps"></a>Дальнейшие действия

Если необходимо изменить размер диска, чтобы воспользоваться преимуществами более крупных уровней производительности, ознакомьтесь с нашими статьями по этой теме:

- [Расширение виртуальных жестких дисков на виртуальной машине Linux с помощью Azure CLI](linux/expand-disks.md)
- [Расширение управляемого диска, подключенного к виртуальной машине Windows](windows/expand-os-disk.md)
