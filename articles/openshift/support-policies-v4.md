---
title: Политика поддержки кластера Azure Red Hat OpenShift 4
description: Общие сведения о требованиях политики поддержки для Red Hat OpenShift 4.
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: container-service
ms.topic: conceptual
ms.date: 04/24/2020
ms.openlocfilehash: 7bdcccee3270f9d2b611682a9a59505158a494d2
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82205213"
---
# <a name="azure-red-hat-openshift-support-policy"></a>Политика поддержки Azure Red Hat OpenShift

Некоторые конфигурации для кластеров Azure Red Hat OpenShift 4 могут влиять на поддержку кластера. Azure Red Hat OpenShift 4 позволяет администраторам кластера вносить изменения во внутренние компоненты кластера, но не все изменения поддерживаются. Политика поддержки, представленная ниже, предоставляет общие сведения о том, какие изменения нарушают политику и поддерживают аннулирование от корпорации Майкрософт и Red Hat.

> [!NOTE]
> Функции, помеченные как предварительная версия технологии на платформе контейнера OpenShift, не поддерживаются в Azure Red Hat OpenShift.

## <a name="cluster-configuration-requirements"></a>Требования к конфигурации кластера

* Все операторы кластера OpenShift должны оставаться в управляемом состоянии. Список операторов кластера можно вернуть, выполнив `oc get clusteroperators`.
* Не удаляйте и не изменяйте службы Prometheus и Алертманажер кластера.
* Не удаляйте правила Алертманажер служб.
* Не изменяйте версию кластера OpenShift.
* Не удаляйте и не изменяйте ведение журнала службы OpenShift Azure Red Hat (мдсд Pod).
* Не удаляйте или не изменяйте секретный код опрашивающего кластера "arosvc.azurecr.io".
* Все виртуальные машины кластера должны иметь исходящий доступ к Интернету по крайней мере для конечных точек Azure Resource Manager (ARM) и Service Logging (Geneva).
* Служба Azure Red Hat OpenShift обращается к кластеру через службу частной связи.  Не удаляйте и не изменяйте доступ к службам.
* Нерхкосные расчетные узлы не поддерживаются. Например, нельзя использовать RHELный узел вычислений.

## <a name="supported-virtual-machine-sizes"></a>Поддерживаемые размеры виртуальных машин

Azure Red Hat OpenShift 4 поддерживает экземпляры рабочих узлов на следующих размерах виртуальных машин:

### <a name="general-purpose"></a>Общего назначения

|Series|Size|vCPU|Память, ГиБ|
|-|-|-|-|
|Dasv4|Standard_D4as_v4|4|16|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dsv3|Standard_D4s_v3|4|16|
|Dsv3|Standard_D8s_v3|8|32|
|Dsv3|Standard_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="memory-optimized"></a>Оптимизированные для операций в памяти

|Series|Size|vCPU|Память, ГиБ|
|-|-|-|-|
|Esv3|Standard_E4s_v3|4|32|
|Esv3|Standard_E8s_v3|8|64|
|Esv3|Standard_E16s_v3|16|128|
|Esv3|Standard_E32s_v3|32|256|

### <a name="compute-optimized"></a>Оптимизированные для вычислений

|Series|Size|vCPU|Память, ГиБ|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|
