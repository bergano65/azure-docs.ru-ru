---
title: Решения для конфиденциальных вычислений в Azure на виртуальных машинах
description: Узнайте о решениях для конфиденциальных вычислений в Azure на виртуальных машинах.
author: JBCook
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: JenCook
ms.openlocfilehash: 8db477f87f344b28e8941e185c70c81d9b860a72
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82149480"
---
# <a name="solutions-on-azure-virtual-machines"></a>Решения на виртуальных машинах Azure

В этой статье рассматриваются сведения о развертывании виртуальных машин Azure для конфиденциальных вычислений (ВМ), на которых работают процессоры Intel, поддерживающие Intel [Software Extension Guard](https://software.intel.com/sgx) (Intel SGX). 

## <a name="azure-confidential-computing-vm-sizes"></a>Размеры виртуальных машин для конфиденциальных вычислений в Azure

Виртуальные машины с конфиденциальными данными Azure предназначены для защиты и целостности данных и кода во время их обработки в облаке. 

[Серия DCsv2](../virtual-machines/dcv2-series.md) Виртуальные машины — это последнее и Последнее семейство конфиденциальных размеров. Эти виртуальные машины поддерживают широкий спектр возможностей развертывания, имеют вдвое больший размер кэша страниц анклава (EPC) и больший выбор размеров по сравнению с виртуальными машинами серии DC. Виртуальные машины [серии DC](../virtual-machines/sizes-previous-gen.md#preview-dc-series) в настоящее время находятся в режиме предварительной версии и будут устаревшими и не включены в общую доступность.

Начните развертывать виртуальную машину серии DCsv2 с помощью коммерческого рынка Майкрософт, следуя [краткому](quick-create-marketplace.md)руководству.

### <a name="current-available-sizes-and-regions"></a>Текущие доступные размеры и регионы

Чтобы получить список общедоступных объемов конфиденциальных виртуальных машин в доступных регионах и зонах доступности, выполните следующую команду в [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli-windows?view=azure-cli-latest):

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family'].{name:name,locations:locationInfo[0].location,AZ_a:locationInfo[0].zones[0],AZ_b:locationInfo[0].zones[1],AZ_c:locationInfo[0].zones[2]}" 
    --all 
    --output table
```

По состоянию на апрель 2020 эти номера SKU доступны в следующих регионах и зонах доступности:

```output
Name              Locations      AZ_a
----------------  -------------  ------
Standard_DC8_v2   eastus         2
Standard_DC1s_v2  eastus         2
Standard_DC2s_v2  eastus         2
Standard_DC4s_v2  eastus         2
Standard_DC8_v2   CanadaCentral
Standard_DC1s_v2  CanadaCentral
Standard_DC2s_v2  CanadaCentral
Standard_DC4s_v2  CanadaCentral
Standard_DC8_v2   uksouth        3
Standard_DC1s_v2  uksouth        3
Standard_DC2s_v2  uksouth        3
Standard_DC4s_v2  uksouth        3
Standard_DC8_v2   CentralUSEUAP
Standard_DC1s_v2  CentralUSEUAP
Standard_DC2s_v2  CentralUSEUAP
Standard_DC4s_v2  CentralUSEUAP
```

Чтобы просмотреть более подробные сведения об указанных размерах, выполните следующую команду:

```azurecli-interactive
az vm list-skus 
    --size dc 
    --query "[?family=='standardDCSv2Family']"
```

## <a name="deployment-considerations"></a>Рекомендации по развертыванию

Ознакомьтесь с кратким руководством по развертыванию виртуальной машины серии DCsv2 менее чем за 10 минут. 

- **Подписка Azure** — чтобы развернуть конфиденциальный экземпляр виртуальной машины, рассмотрим подписку с оплатой по мере использования или другой вариант покупки. Если вы используете [бесплатную учетную запись Azure](https://azure.microsoft.com/free/), у вас не будет квоты на подходящее количество ядер вычислений Azure.

- **Цены и региональные** сведения о доступности — найдите цены на виртуальные машины серии DCsv2 на [странице цен на виртуальную машину](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). Проверьте [доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) , чтобы узнать, в каких регионах Azure их можно использовать.


- **Квота ядер**. Вам может потребоваться увеличить стандартную квоту на число ядер в подписке Azure. Ваша подписка также может ограничивать количество ядер, которые можно развернуть в определенных семействах размеров виртуальных машин, включая серии DCsv2. Чтобы запросить увеличение квоты, отправьте [запрос в службу поддержки клиентов](https://docs.microsoft.com/azure/azure-portal/supportability/per-vm-quota-requests) бесплатно. Обратите внимание, что ограничения по умолчанию могут различаться в зависимости от категории подписки.

  > [!NOTE]
  > Если вам нужны ресурсы в очень большом объеме, обратитесь в службу поддержки Azure. Квоты Azure — это ограничения по кредитам, а не гарантированная емкость. Вне зависимости от квоты с вас будет взиматься плата только за используемые ядра.
  
- **Изменение размера** — из-за специального аппаратного обеспечения можно изменять размер только тех экземпляров конфиденциальных вычислений в пределах одного семейства размеров. Например, можно изменить размер виртуальной машины серии DCsv2 только с одного размера DCsv2 рядов на другой. Изменение размера неконфиденциальных вычислений с учетом конфиденциального размера не поддерживается.  

- **Образ** . чтобы предоставить поддержку Intel Software Guard Extension (Intel SGX) для конфиденциальных вычислений, все развертывания должны выполняться в образах поколения 2. Конфиденциальные вычисления в Azure поддерживают рабочие нагрузки под управлением Ubuntu 18,04 Gen 2, Ubuntu 16,04 Gen 2 и Windows Server 2016 Gen 2. Дополнительные сведения о поддерживаемых и неподдерживаемых сценариях см. в статье [Поддержка виртуальных машин поколения 2 в Azure](../virtual-machines/linux/generation-2.md) . 

- **Хранилище** — диски данных виртуальной машины с конфиденциальной системой Azure и временные диски ОС находятся на дисках NVMe. Экземпляры поддерживают только SSD (цен. категория "Премиум") и SSD (цен. категория "Стандартный") диски, а не SSD (цен. категория "Ультра") или HDD (цен. категория "Стандартный"). Размер виртуальной машины **DC8_v2** не поддерживает хранилище класса Premium. 

- **Шифрование диска** — конфиденциальные экземпляры не поддерживают шифрование дисков в настоящее время. 

## <a name="high-availability-and-disaster-recovery-considerations"></a>Рекомендации по высокой доступности и аварийному восстановлению

При использовании виртуальных машин в Azure вы несете ответственность за реализацию решения высокого уровня доступности и аварийного восстановления, чтобы избежать простоев. 

В настоящее время конфиденциальные вычисления в Azure не поддерживают избыточность зоны с помощью Зоны доступности. Для обеспечения высокой доступности и избыточности конфиденциальных вычислений используйте [группы доступности](../virtual-machines/windows/manage-availability.md#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy). В связи с аппаратными ограничениями группы доступности для экземпляров конфиденциальных вычислений могут иметь не более 10 доменов обновления. 

## <a name="deploying-via-an-azure-resource-manager-template"></a>Развертывание с помощью шаблона Azure Resource Manager 

Azure Resource Manager — это служба развертывания и управления для Azure. Она обеспечивает уровень управления, позволяющий создавать, обновлять и удалять ресурсы в подписке Azure. Вы можете использовать ее функции управления, такие как управление доступом, блокировка и добавление тегов, чтобы защитить и упорядочить ресурсы после развертывания.

Дополнительные сведения о шаблонах Azure Resource Manager см. в статье [Шаблоны Azure Resource Manager](../azure-resource-manager/templates/overview.md).

Чтобы развернуть виртуальную машину серии DCsv2 в шаблоне ARM, вы будете использовать [ресурс виртуальной машины](../virtual-machines/windows/template-description.md). Необходимо убедиться, что указаны правильные свойства для **vmSize** и для **imageReference**.

### <a name="vm-size"></a>Размер виртуальной машины

Укажите один из следующих размеров в шаблоне ARM в ресурсе виртуальной машины. Эта строка помещается в **свойствах**как **vmSize** .

```json
  [
        "Standard_DC1s_v2",
        "Standard_DC2s_v2",
        "Standard_DC4s_v2",
        "Standard_DC8_v2"
      ],
```

### <a name="gen2-os-image"></a>Образ ОС Gen2

В разделе **Свойства**также необходимо сослаться на образ в разделе **storageProfile**. Используйте *только одно* из следующих образов для **imageReference**.

```json
      "2016-datacenter-gensecond": {
        "offer": "WindowsServer",
        "publisher": "MicrosoftWindowsServer",
        "sku": "2016-datacenter-gensecond",
        "version": "latest"
      },
        "18_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "18_04-lts-gen2",
        "version": "latest"
      },
      "16_04-lts-gen2": {
        "offer": "UbuntuServer",
        "publisher": "Canonical",
        "sku": "16_04-lts-gen2",
        "version": "latest"
      }
```

## <a name="next-steps"></a>Дальнейшие действия 

В этой статье вы узнали о квалификации и конфигурациях, необходимых при создании виртуальной машины для конфиденциальных вычислений. Теперь вы можете заголовкировать в Azure Marketplace, чтобы развернуть виртуальную машину серии DCsv2.

[!div class=”nextstepaction”]
[Развертывание виртуальной машины серии DCsv2 в Azure Marketplace](quick-create-marketplace.md)