---
title: Изменение производительности управляемых дисков Azure
description: Сведения о уровнях производительности для управляемых дисков и о том, как изменить уровни производительности для существующих управляемых дисков.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/24/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: efbe8bc24b430716da46601ed073300e4c79cca7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91743732"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Уровни производительности для управляемых дисков (Предварительная версия)

В настоящее время Хранилище дисков Azure предлагает встроенные возможности для повышения производительности при обработке краткосрочного непредвиденного трафика. Расширенные твердотельные накопители позволяют повысить производительность диска, не увеличивая фактический размер диска. Эта возможность позволяет соответствовать потребностям производительности рабочей нагрузки и снизить затраты. 

> [!NOTE]
> Эта функция в настоящее время находится на стадии предварительной версии. 

Эта функция идеально подходит для событий, которые временно потребовали более высокого уровня производительности, например праздничных покупок, тестирования производительности или запуска среды обучения. Для решения этих событий можно использовать более высокий уровень производительности в течение того времени, пока он необходим. После этого можно вернуться к исходному уровню, если больше не требуется дополнительная производительность.

## <a name="how-it-works"></a>Принцип работы

При первом развертывании или подготовке диска базовый уровень производительности для этого диска задается в зависимости от размера подготовленного диска. Более высокий уровень производительности можно использовать для удовлетворения более высоких потребностей. Если этот уровень производительности больше не нужен, можно вернуться к исходному базовому уровню производительности.

Изменения в оплате по мере изменения уровня. Например, если вы подготавливаете диск P10 (128 гиб), базовый уровень производительности задается как P10 (500 операций ввода-вывода в секунду и 100 Мбит/с). Плата взимается по тарифу P10. Уровень можно обновить в соответствии с производительностью P50 (7 500 операций ввода-вывода в секунду и 250 Мбит/с) без увеличения размера диска. Во время обновления будет взиматься плата по ставке P50. Если больше не требуется высокая производительность, можно вернуться к уровню P10. На диск будет взиматься плата по тарифу P10.

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

- В настоящее время эта функция поддерживается только для твердотельных накопителей уровня "Премиум".
- Для изменения уровня диска необходимо отсоединить диск от работающей виртуальной машины.
- Использование уровней производительности P60, P70 и P80 ограничено дисками 4 096 гиб и выше.
- Уровень производительности диска можно изменить каждые 24 часа.

## <a name="regional-availability"></a>Доступность по регионам

Возможность настройки уровня производительности управляемого диска в настоящее время доступна только для твердотельных накопителей категории "Премиум" в регионе "Западная Центральная часть США". 

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

## <a name="next-steps"></a>Дальнейшие шаги

Если необходимо изменить размер диска, чтобы воспользоваться преимуществами более высоких уровней производительности, см. следующие статьи:

- [Расширение виртуальных жестких дисков на виртуальной машине Linux с помощью Azure CLI](linux/expand-disks.md)
- [Расширение управляемого диска, подключенного к виртуальной машине Windows](windows/expand-os-disk.md)
