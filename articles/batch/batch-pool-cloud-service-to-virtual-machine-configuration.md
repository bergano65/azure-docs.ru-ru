---
title: Перенос конфигурации пула пакетной службы из облачных служб на виртуальные машины
description: Узнайте, как обновить конфигурацию пула до последней и рекомендуемой конфигурации.
ms.topic: how-to
ms.date: 1/4/2021
ms.openlocfilehash: 52e1762dc8e81b3eb7e1bce388d91dfd2c76191a
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937710"
---
# <a name="migrate-batch-pool-configuration-from-cloud-services-to-virtual-machines"></a>Перенос конфигурации пула пакетной службы из облачных служб на виртуальные машины

Пулы пакетной службы можно создавать с помощью [cloudServiceConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration) или [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration). "virtualMachineConfiguration" — Рекомендуемая конфигурация, так как она поддерживает все возможности пакета. пулы "cloudServiceConfiguration" не поддерживают все функции, и новые функции не планируются.

Если вы используете пулы "cloudServiceConfiguration", настоятельно рекомендуется перейти к использованию пулов "virtualMachineConfiguration". В этой статье описывается, как перейти к рекомендуемой конфигурации "virtualMachineConfiguration".

## <a name="new-pools-are-required"></a>Требуются новые пулы

Существующие активные пулы не могут быть обновлены с "cloudServiceConfiguration" на "virtualMachineConfiguration", необходимо создать новые пулы. Создание пулов с помощью "virtualMachineConfiguration" поддерживается всеми API пакетной службы, программами командной строки, портал Azure и пользовательским интерфейсом Batch Explorer.

В учебниках по [.NET](tutorial-parallel-dotnet.md) и [Python](tutorial-parallel-python.md) приведены примеры создания пула с помощью "virtualMachineConfiguration".

## <a name="pool-configuration-differences"></a>Различия в конфигурации пула

При обновлении конфигурации пула следует учитывать следующее:

- узлы пула "cloudServiceConfiguration" всегда имеют ОС Windows, а пулы "virtualMachineConfiguration" могут быть ОС Linux или Windows.
- По сравнению с пулами "cloudServiceConfiguration", пулы "virtualMachineConfiguration" имеют более широкий набор возможностей, таких как поддержка контейнеров, диски данных и шифрование дисков.
- узлы пула "virtualMachineConfiguration" используют управляемые диски ОС. [Тип управляемого диска](../virtual-machines/disks-types.md) , используемый для каждого узла, зависит от размера виртуальной машины, выбранной для пула. Если для пула указан размер виртуальной машины, например "Standard_D2s_v3", то используется SSD уровня "Премиум". Если указан размер виртуальной машины "не-s", например "Standard_D2_v3", используется стандартный жесткий диск.

   > [!IMPORTANT]
   > Как и в случае с виртуальными машинами и масштабируемыми наборами виртуальных машин, управляемый диск операционной системы, используемый для каждого узла, требует затрат, что является дополнительной стоимостью виртуальных машин. Нет затрат на диск ОС для узлов "cloudServiceConfiguration", так как диск ОС создается на узлах локального SSD.

- Время запуска и удаления пулов и узлов может немного отличаться между пулами "cloudServiceConfiguration" и пулами "virtualMachineConfiguration".

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о [конфигурациях пулов](nodes-and-pools.md#configurations).
- Дополнительные сведения о рекомендациях по работе с [пулами](best-practices.md#pools).
- REST API Справочник по [добавлению](https://docs.microsoft.com/rest/api/batchservice/pool/add) и [virtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)пула.
