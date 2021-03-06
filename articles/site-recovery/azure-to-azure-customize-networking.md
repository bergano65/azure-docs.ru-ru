---
title: Настройка сетевых конфигураций для отказоустойчивой виртуальной машины | Документация Майкрософт
description: Содержит общие сведения о настройке сетевых конфигураций для отказоустойчивой виртуальной машины в репликации виртуальных машин Azure с помощью Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: rajanaki
ms.openlocfilehash: 191161c8185f45712052000285013a6e61c9fa6a
ms.sourcegitcommit: b1c94635078a53eb558d0eb276a5faca1020f835
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2019
ms.locfileid: "72968914"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Настройка сетевых конфигураций целевой виртуальной машины Azure

В этой статье приводятся рекомендации по настройке сетевых конфигураций на целевой виртуальной машине Azure при репликации и восстановлении виртуальных машин Azure из одного региона в другой с помощью [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Перед началом работы

Узнайте, как Site Recovery обеспечивает аварийное восстановление для [этого сценария](azure-to-azure-architecture.md).

## <a name="supported-networking-resources"></a>Поддерживаемые сетевые ресурсы

При репликации виртуальных машин Azure можно предоставить следующие основные конфигурации ресурсов для отработки отказа виртуальной машины:

- [Внутренняя подсистема балансировки нагрузки](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#what-is-standard-load-balancer)
- [Общедоступный IP-адрес](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Группа безопасности сети](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) как для подсети, так и для сетевой карты

## <a name="prerequisites"></a>Технические условия

- Убедитесь, что конфигурация на стороне восстановления планируется заранее.
- Создайте сетевые ресурсы заранее. Укажите его в качестве входных данных, чтобы служба Azure Site Recovery могла принять эти параметры и убедиться, что виртуальная машина отработки отказа соответствует этим параметрам.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>Настройка конфигураций сети отработки отказа и тест

1. Перейдите к разделу **реплицированные элементы**. 
2. Выберите нужную виртуальную машину Azure.
3. Выберите среда **вычислений и сеть** и нажмите кнопку **изменить**. Обратите внимание, что параметры конфигурации сетевого адаптера включают соответствующие ресурсы в источнике. 

     ![Настройка конфигураций сети отработки отказа](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Выберите виртуальную сеть тестовой отработки отказа. Вы можете оставить его пустым и выбрать его во время тестовой отработки отказа.
5. Сеть отработки отказа выберите **изменить** рядом с сетевой картой, которую вы хотите настроить. В следующей появившейся колонке выберите соответствующие предварительно созданные ресурсы в области тестовая отработка отказа и расположение отработки отказа.

    ![Изменение конфигурации сетевого адаптера](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. Нажмите кнопку **ОК**.

Site Recovery теперь соберет эти параметры и убедитесь, что виртуальная машина при отработке отказа подключена к выбранному ресурсу через соответствующий сетевой адаптер.

При активации тестовой отработки отказа с помощью плана восстановления всегда будет запрашиваться виртуальная сеть Azure. Эта виртуальная сеть будет использоваться для тестовой отработки отказа на компьютерах, на которых не были предварительно настроены параметры тестовой отработки отказа.

## <a name="troubleshooting"></a>Устранение неисправностей

### <a name="unable-to-view-or-select-a-resource"></a>Не удается просмотреть или выбрать ресурс

Если вы не можете выбрать или просмотреть сетевой ресурс, выполните следующие проверки и условия.

- Целевое поле для сетевого ресурса включается, только если у исходной виртуальной машины есть соответствующие входные данные. Это основано на принципе аварийного восстановления, поэтому требуется либо точная, либо горизонтально уменьшенная версия источника.
- Для каждого сетевого ресурса в раскрывающемся списке применяются некоторые фильтры, чтобы гарантировать, что виртуальная машина для отработки отказа может присоединить себя к выбранному ресурсу и поддерживать надежность отработки отказа. Эти фильтры основаны на тех же сетевых условиях, которые были бы проверены при настройке исходной виртуальной машины.

Проверки внутреннего балансировщика нагрузки:

- Подписка и регион подсистемы балансировки нагрузки и целевой виртуальной машины должны совпадать.
- Виртуальная сеть, связанная с внутренней подсистемой балансировки нагрузки и целевой виртуальной машиной, должна быть одинаковой.
- Номер SKU общедоступного IP-адреса целевой виртуальной машины и номер SKU внутренней подсистемы балансировки нагрузки должны быть одинаковыми.
- Если целевая виртуальная машина настроена для размещения в зоне доступности, проверьте, является ли балансировщик нагрузки избыточным для зоны или частью любой зоны доступности. (Подсистемы балансировки нагрузки уровня SKU "базовый" не поддерживают зоны и не будут отображаться в раскрывающемся списке в этом случае.)
- Убедитесь, что внутренняя подсистема балансировки нагрузки имеет предварительно созданный внутренний пул и конфигурацию внешнего интерфейса.

Общедоступный IP-адрес:

- Подписка и регион общедоступного IP-адреса и целевой виртуальной машины должны совпадать.
- Номер SKU общедоступного IP-адреса целевой виртуальной машины и номер SKU внутренней подсистемы балансировки нагрузки должны быть одинаковыми.

Группа безопасности сети:
- Подписка и регион группы безопасности сети и целевой виртуальной машины должны совпадать.


> [!WARNING]
> Если целевая виртуальная машина связана с набором доступности, необходимо связать общедоступный IP-адрес и внутренний балансировщик нагрузки того же номера SKU с общедоступным IP-адресом и внутренней подсистемой балансировки нагрузки в группе доступности. В противном случае отработка отказа может быть невозможна.
