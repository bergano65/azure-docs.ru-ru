---
title: Вопросы и ответы о конфиденциальных компьютерных системах Azure
description: Ответы на часто задаваемые вопросы о конфиденциальных вычислениях в Azure.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: workloads
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: 2bd9430fc6f48d72faa2c1850af0bb8432a7c5f5
ms.sourcegitcommit: f7fb9e7867798f46c80fe052b5ee73b9151b0e0b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82149506"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Часто задаваемые вопросы о конфиденциальных вычислениях в Azure

В этой статье содержатся ответы на некоторые наиболее распространенные вопросы о выполнении [рабочих нагрузок для конфиденциальных вычислений в Azure](overview.md).

Если эта статья не помогла устранить вашу проблему с Azure, посетите [форумы по Azure на сайтах MSDN и Stack Overflow](https://azure.microsoft.com/support/forums/). Вы можете разместить свои проблемы на этих форумах или опубликовать [ @AzureSupport в Twitter](https://twitter.com/AzureSupport). Вы также можете отправить запрос в службу поддержки Azure. Чтобы отправить запрос на поддержку, на [странице поддержки Azure](https://azure.microsoft.com/support/options/)выберите получить поддержку.

## <a name="confidential-computing-virtual-machines"></a>Виртуальные машины для конфиденциальных вычислений<a id="vm-faq"></a>

1. **Как можно начать развертывание виртуальных машин серии DCsv2?**

   Ниже приведены некоторые способы развертывания виртуальной машины DCsv2.
   - Использование [шаблона Azure Resource Manager](../virtual-machines/windows/template-description.md)
   - Из [портал Azure](https://portal.azure.com/#create/hub)
   - В шаблоне решения для [конфиденциальных вычислений Azure (виртуальных машин)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) Marketplace. Шаблон решения Marketplace поможет ограничить возможности клиента поддерживаемыми сценариями (регионами, изображениями, доступностью, шифрованием дисков). 

1. **Будут ли все образы ОС работать с конфиденциальными вычислениями в Azure?**

   Нет. Виртуальные машины можно развертывать только на виртуальных машинах версии 2. Мы предлагаем поддержку поколения 2 для Ubuntu Server 18,04, Ubuntu Server 16,04 и Windows Server 2016 Datacenter. Узнайте больше о виртуальных машинах поколения 2 в [Linux](../virtual-machines/linux/generation-2.md) и [Windows](../virtual-machines/windows/generation-2.md) .

1. **Виртуальные машины DCsv2 неактивны на портале, и я не могу выбрать один из них**

   На основе информационного всплывающего окна рядом с виртуальной машиной можно предпринять различные действия:
    -   **Унсуппортедженератион**: измените поколение образа виртуальной машины на "Gen2".
    -   **Нотаваилаблефорсубскриптион** : регион еще недоступен для вашей подписки. Выберите доступный регион.
    -   **ИнсуффиЦиенткуота**: [Создайте запрос в службу поддержки, чтобы увеличить квоту](../azure-portal/supportability/per-vm-quota-requests.md). У бесплатных пробных подписок нет квоты на виртуальные машины для конфиденциальных вычислений. 

1. **Виртуальные машины DCsv2 не отображаются при попытке найти их в селекторе размера портала**

   Убедитесь, что выбран доступный регион. Также убедитесь, что в селекторе размера выбрано "очистить все фильтры". 

1. **В чем разница между виртуальными машинами серии DCsv2 и DC?**

   Виртуальные машины серии DC работают на старых 6-ядерных процессорах Intel с SGX. Они имеют менее общий объем памяти, меньшее количество памяти (страничный корпус анклава) и доступны в меньшем количестве регионов. Эти виртуальные машины доступны только в восточной части США и Западной Европе доступны в двух размерах: Standard_DC2s и Standard_DC4s. Они не будут работать в общедоступной версии и могут быть развернуты только в экземпляре Marketplace [VM (Предварительная версия) виртуальной машины серии DC [Preview]](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview) .

1. **Доступны ли DCsv2 виртуальные машины глобально?**

   Нет, эти виртуальные машины доступны только в выбранных регионах. Последние доступные регионы см. на [странице продукты по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) . 

1. **Разделы справки установить пакет SDK Open анклава?**
   
   Инструкции по установке пакета OE SDK на компьютере в Azure или локальной среде см. в разделе [Open АНКЛАВА SDK GitHub](https://github.com/openenclave/openenclave).
     
   Инструкции по установке для конкретной ОС можно также заголовков Open анклава SDK GitHub:
     - [Установка пакета OE SDK в Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
     - [Установка пакета OE SDK на Ubuntu 18,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
     - [Установка пакета OE SDK на Ubuntu 16,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)
