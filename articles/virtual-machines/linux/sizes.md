---
title: Размеры виртуальных машин Linux в Azure
description: Список различных размеров виртуальных машин Linux в Azure.
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 2620795d8c4fc8adf11fc0795048ff9999d6ef83
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84783361"
---
# <a name="sizes-for-linux-virtual-machines-in-azure"></a>Размеры виртуальных машин Linux в Azure

В этой статье описаны доступные размеры и разновидности виртуальных машин Azure, которые можно использовать для запуска приложений и рабочих нагрузок Linux. Здесь также предоставлены рекомендации по развертыванию, которые нужно учитывать при планировании использования этих ресурсов. Также доступна версия этой статьи для [виртуальных машин Windows](../windows/sizes.md?toc=/azure/virtual-machines/windows/toc.json&bc=/azure/virtual-machines/windows/breadcrumb/toc.json).

| Тип | Размеры | Описание |
|------|-------|-------------|
| [Универсальные](../sizes-general.md)   | B, Dsv3, Dv3, Dasv4, Dav4, DSv2, Dv2, Av2, DC, DCv2, dv4, Dsv4, Ddv4, Ddsv4  | Сбалансированное соотношение ресурсов ЦП и памяти. Идеальное решение для тестирования и разработки, небольших и средних баз данных, а также веб-серверов с небольшим или средним объемом трафика. |
| [Оптимизированные для вычислений](../sizes-compute.md) | Fsv2 | Высокое соотношение ресурсов ЦП и памяти. Подходят для веб-серверов со средним объемом трафика, сетевых устройств, пакетных процессов и серверов приложений. |
| [Оптимизированные для памяти](../sizes-memory.md) | Esv3, Ev3, Easv4, Eav4, Ev4, Esv4, Edv4, Edsv4, Mv2, M, DSv2, Dv2 | Высокое соотношение ресурсов памяти и ядра. Отлично подходят для серверов реляционной базы данных, кэша среднего и большого объема, а также выполняющейся в памяти аналитики.                 |
| [Оптимизированные для хранилища](../sizes-storage.md) | Lsv2 | Высокая пропускная способность и количество операций ввода-вывода. Идеальный вариант для работы с большими данными, с базами данных SQL и NoSQL, системами хранения данных и большими транзакционными базами данных.  |
| [GPU](../sizes-gpu.md) | NC, NCv2, NCv3, ND, NDv2 (предварительная версия), NV, NVv3, NVv4 | Специализированные виртуальные машины, предназначенные для ресурсоемкой отрисовки изображений и редактирования видео, а также для обучения моделей и формирования выводов с помощью глубокого обучения. Доступны виртуальные машины одним или несколькими GPU. |
| [Для высокопроизводительных вычислений](../sizes-hpc.md) | HB, HBv2, HC, H | Виртуальные машины с самыми быстрыми и мощными ЦП, для которых можно настроить сетевые интерфейсы с высокой пропускной способностью (RDMA). |

- Подробнее о ценах на различные размеры см. в разделе [Цены на виртуальные машины](https://azure.microsoft.com/pricing/details/virtual-machines/#Linux). 
- Доступность размеров виртуальных машин см. в статье [Доступность продуктов по регионам](https://azure.microsoft.com/regions/services/).
- Сведения об общих ограничениях виртуальных машин Azure см. в статье [Подписка Azure, границы, квоты и ограничения службы](../../azure-subscription-service-limits.md).
- Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](../acu.md) сравнить производительность вычислений для различных номеров SKU Azure.

## <a name="rest-api"></a>REST API

Сведения об использовании Rest API, чтобы получить сведения о размерах виртуальных машин, см. в следующих статьях:

- [List available virtual machine sizes for resizing](https://docs.microsoft.com/rest/api/compute/virtualmachines/listavailablesizes) (Вывод доступных размеров виртуальных машин для изменения размеров)
- [List available virtual machine sizes for a subscription](https://docs.microsoft.com/rest/api/compute/resourceskus/list) (Вывод доступных размеров виртуальных машин для подписки)
- [List available virtual machine sizes in an availability set](https://docs.microsoft.com/rest/api/compute/availabilitysets/listavailablesizes) (Вывод доступных размеров виртуальных машин в группе доступности)

## <a name="acu"></a>ACU

Узнайте больше о том, как с помощью [единиц вычислений Azure (ACU)](../acu.md) сравнить производительность вычислений для различных номеров SKU Azure.

## <a name="benchmark-scores"></a>Результаты теста производительности

Узнайте больше о вычислительной мощности виртуальных машин Linux с помощью [результатов теста производительности CoreMark](compute-benchmark-scores.md).

## <a name="next-steps"></a>Дальнейшие действия

Узнайте больше о различных доступных размерах виртуальных машин.

- [Универсальные](../sizes-general.md)
- [Оптимизированные для вычислений](../sizes-compute.md)
- [Оптимизированные для памяти](../sizes-memory.md)
- [Оптимизированные для хранилища](../sizes-storage.md)
- [GPU](../sizes-gpu.md)
- [Для высокопроизводительных вычислений](../sizes-hpc.md)
- Описание серий A Standard, Dv1 (D1–4 и D11–14 v1) и A8–A11 приведено на странице [Предыдущее поколение](../sizes-previous-gen.md).
