---
title: Поддерживаемые ресурсы для Azure Red Hat OpenShift | Документация Майкрософт
description: Понять, какие регионы Azure и размеры виртуальных машин, поддерживаемых Microsoft Azure Red Hat OpenShift.
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/15/2019
ms.openlocfilehash: c226227797802ab58d1bcbaadb7e97e780b30560
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/29/2019
ms.locfileid: "66306218"
---
# <a name="azure-red-hat-openshift-resources"></a>Ресурсы Azure Red Hat OpenShift

В этом разделе перечислены регионы Azure и размеры виртуальных машин, поддерживаемых службой Microsoft Azure Red Hat OpenShift.

## <a name="azure-regions"></a>Регионы Azure

См. в разделе [доступность продуктов по регионам](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all) текущий список регионов, где вы можете развернуть Azure Red Hat OpenShift кластеров.

## <a name="virtual-machine-sizes"></a>Размер виртуальных машин

Ниже приведены поддерживаемые размеры виртуальных машин, можно указать для вычислительных узлов в кластере Azure Red Hat OpenShift.

> [!Important]
> Каждая виртуальная машина имеет разное количество дисков, которые можно подключить. Это может оказаться как очевидно как память или ЦП размер.
> Не во всех регионах доступны виртуальные машины всех размеров. Даже если API поддерживает размер вами, если размер недоступен в регионе, в указанную вами может появиться ошибка.
> См. в разделе [размеров текущий список поддерживаемых виртуальных Машин на регион](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) Дополнительные сведения.

## <a name="compute-node-sizes"></a>Вычисления размеров узлов

Azure Red Hat OpenShift REST API поддерживает следующие размеры вычислительных узлов.

|Размер|Виртуальных ЦП|ОЗУ|
|-|-|-|
|Стандартный D4s v3|4.|16 ГБ|
|Стандартный D8s v3|8|32 ГБ|
|Стандартный D16s v3|16|64 ГБ|
|Стандартный D32s v3|32|128 ГБ|
|-|-|-|
|Стандартный E4s v3|4.|32 ГБ|
|Стандартный E8s v3|8|64 ГБ|
|Стандартный E16s v3|16|128 ГБ|
|Стандартный E32s v3|32|256 ГБ|
|-|-|-|
|Стандартный F8s v2|8|16 ГБ|
|Стандартный F16s v2|16|32 ГБ|
|Стандартный F32s v2|32|64 ГБ|

## <a name="master-node-sizes"></a>Размеры главный узел

Следующий образец / размеров узлов инфраструктуры поддерживаются Azure Red Hat OpenShift REST API:

|Размер|Виртуальных ЦП|ОЗУ|
|-|-|-|
|Стандартный D4s v3|4.|16 ГБ|
|Стандартный D8s v3|8|32 ГБ|
|Стандартный D16s v3|16|64 ГБ|
|Стандартный D32s v3|32|128 ГБ|

## <a name="next-steps"></a>Дальнейшие действия

Попробуйте [создать кластер Azure Red Hat OpenShift](tutorial-create-cluster.md) руководства.