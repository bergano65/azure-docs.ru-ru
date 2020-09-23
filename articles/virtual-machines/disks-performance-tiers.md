---
title: Повышение производительности управляемых дисков Azure
description: Сведения о уровнях производительности для управляемых дисков, а также об обновлении уровней производительности для управляемых дисков.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 09/22/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: aa188babf56d4a825059fe6103e2e07745eb134f
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90974132"
---
# <a name="performance-tiers-for-managed-disks-preview"></a>Уровни производительности для управляемых дисков (Предварительная версия)

В настоящее время Хранилище дисков Azure предлагает встроенные возможности для достижения более высокой производительности при обработке краткосрочного непредвиденного трафика. Расширенные твердотельные накопители обеспечивают гибкость при увеличении производительности диска без увеличения фактического размера диска, что позволяет сопоставить потребности рабочей нагрузки и снизить затраты. Сейчас эта функция доступна в предварительной версии. Это идеальный вариант для событий, временно требующих более высокого уровня производительности, например праздничных покупок, тестирования производительности или запуска среды обучения. Для решения этих событий можно выбрать более высокий уровень производительности, если это необходимо, и вернуться к исходному уровню, когда больше не требуется дополнительная производительность.

## <a name="how-it-works"></a>Принцип работы

При первом развертывании или подготовке диска базовый уровень производительности для этого диска задается в зависимости от размера подготовленного диска. Можно выбрать более высокий уровень производительности, чтобы удовлетворить более высокие требования, и если эта производительность больше не нужна, можно вернуться к исходному базовому уровню производительности. Например, если вы подготавливаете диск P10 (128 гиб), базовый уровень производительности задается как P10 (500 операций ввода-вывода в секунду и 100 МБ/с). Уровень можно обновить в соответствии с производительностью P50 (7500 операций ввода-вывода в секунду и 250 МБ/с) без увеличения размера диска и возврата в P10, если более высокая производительность больше не нужна.

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
| 4 ТиБ | P50 | Отсутствуют |
| 8 ТиБ | P60 |  P70, P80 |
| 16 ТиБ | P70 | P80 |
| 32 ТиБ | P80 | Отсутствуют |

## <a name="restrictions"></a>Ограничения

- Сейчас поддерживается только для твердотельных накопителей уровня "Премиум".
- Диски должны быть отсоединены от работающей виртуальной машины перед изменением уровней.
- Использование уровней производительности P60, P70 и P80 ограничено дисками 4096 гиб и выше.

## <a name="regional-availability"></a>Доступность по регионам

Настройка уровня производительности управляемого диска сейчас доступна только для твердотельных накопителей класса Premium в следующих регионах:

- центрально-западная часть США 
- Восток 2 США 
- Западная Европа
- Восточная Австралия 
- Юго-Восточная Австралия 
- Южная Индия

## <a name="createupdate-a-data-disk-with-a-tier-higher-than-the-baseline-tier"></a>Создание или обновление диска данных с уровнем выше базового уровня

1. Создайте пустой диск данных с уровнем выше базового уровня или обновите уровень диска выше базового уровня, используя пример шаблона [CreateUpdateDataDiskWithTier.jsна](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateDataDiskWithTier.json)

     ```cli
     subscriptionId=<yourSubscriptionIDHere>
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     diskSize=<yourDiskSizeHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az login
    
     az account set --subscription $subscriptionId
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateDataDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier" "dataDiskSizeInGb=$diskSize"
     ```

1. Подтвердите уровень диска

    ```cli
    az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="createupdate-an-os-disk-with-a-tier-higher-than-the-baseline-tier"></a>Создание или обновление диска ОС с уровнем выше базового уровня

1. Создайте диск ОС из образа Marketplace или обновите уровень диска ОС, превышающий базовый, используя пример шаблона [CreateUpdateOSDiskWithTier.jsна](https://github.com/Azure/azure-managed-disks-performance-tiers/blob/main/CreateUpdateOSDiskWithTier.json)

     ```cli
     resourceGroupName=<yourResourceGroupNameHere>
     diskName=<yourDiskNameHere>
     performanceTier=<yourDesiredPerformanceTier>
     region=<yourRegionHere>
    
     az group deployment create -g $resourceGroupName \
     --template-uri "https://raw.githubusercontent.com/Azure/azure-managed-disks-performance-tiers/main/CreateUpdateOSDiskWithTier.json" \
     --parameters "region=$region" "diskName=$diskName" "performanceTier=$performanceTier"
     ```
 
 1. Подтвердите уровень диска
 
     ```cli
     az resource show -n $diskName -g $resourceGroupName --namespace Microsoft.Compute --resource-type disks --api-version 2020-06-30 --query [properties.tier] -o tsv
     ```

## <a name="next-steps"></a>Дальнейшие действия

Если необходимо изменить размер диска, чтобы воспользоваться преимуществами более крупных уровней производительности, ознакомьтесь с нашими статьями по этой теме:

- [Расширение виртуальных жестких дисков на виртуальной машине Linux с помощью Azure CLI](linux/expand-disks.md)
- [Расширение управляемого диска, подключенного к виртуальной машине Windows](windows/expand-os-disk.md)