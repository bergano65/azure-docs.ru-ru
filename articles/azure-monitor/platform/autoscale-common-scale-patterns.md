---
title: Обзор общих шаблонов автомасштабирования
description: Ознакомьтесь с некоторыми общими шаблонами для автомасштабирования ресурсов в Azure.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/07/2017
ms.author: ancav
ms.subservice: autoscale
ms.openlocfilehash: 8356a8c8c31a043197485b4913b4a67d7d719778
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57997257"
---
# <a name="overview-of-common-autoscale-patterns"></a>Обзор общих шаблонов автомасштабирования
В этой статье описаны некоторые общие шаблоны для масштабирования ресурсов в Azure.

Автомасштабирование Azure Monitor используется только с [масштабируемыми наборами виртуальных машин](https://azure.microsoft.com/services/virtual-machine-scale-sets/), [облачными службами](https://azure.microsoft.com/services/cloud-services/), [веб-приложениями службы приложений](https://azure.microsoft.com/services/app-service/web/) и [службами управления API](https://docs.microsoft.com/azure/api-management/api-management-key-concepts).

## <a name="lets-get-started"></a>Начало работы

В данной статье предполагается, что вы знакомы с автомасштабированием. Сведения по началу работы с автомасштабированием ресурсов см. [здесь][1]. Далее приведены некоторые общие шаблоны масштабирования.

## <a name="scale-based-on-cpu"></a>Масштабирование на основе использования ЦП

У вас есть веб-приложение (VMSS или роль облачной службы). Кроме того:

- вы хотите выполнять развертывание и свертывание на основе использования ЦП;
- вы хотите иметь минимальное количество экземпляров;
- при этом нужно задать максимальное ограничение на количество экземпляров, которые можно развернуть.

![Масштабирование на основе использования ЦП][2]

## <a name="scale-differently-on-weekdays-vs-weekends"></a>Масштабирование в рабочие и выходные дни

У вас есть веб-приложение (VMSS или роль облачной службы). Кроме того:

- требуется 3 экземпляра по умолчанию (в рабочие дни);
- трафик в выходные дни не ожидается, поэтому необходимо уменьшить масштаб до 1 экземпляра.

![Масштабирование в рабочие и выходные дни][3]

## <a name="scale-differently-during-holidays"></a>Масштабирование в праздничные дни

У вас есть веб-приложение (VMSS или роль облачной службы). Кроме того:

- необходимо увеличивать и уменьшать масштаб на основе использования ЦП по умолчанию;
- однако во время праздников (или в определенные дни, которые важны для бизнеса) вы хотите переопределить значения по умолчанию и увеличить емкость.

![Масштабирование в праздничные дни][4]

## <a name="scale-based-on-custom-metric"></a>Масштабирование на основе пользовательской метрики

У вас есть веб-интерфейса и уровень API, который обменивается данными с серверной частью.

- Вы хотите масштабировать уровень API на основе пользовательских событий во внешнем интерфейсе (например, масштабировать процесс оформления заказов и оплаты на основе количества элементов в корзине).

![Масштабирование на основе пользовательской метрики][5]

<!--Reference-->
[1]: ./autoscale-get-started.md
[2]: ./media/autoscale-common-scale-patterns/scale-based-on-cpu.png
[3]: ./media/autoscale-common-scale-patterns/weekday-weekend-scale.png
[4]: ./media/autoscale-common-scale-patterns/holidays-scale.png
[5]: ./media/autoscale-common-scale-patterns/custom-metric-scale.png

