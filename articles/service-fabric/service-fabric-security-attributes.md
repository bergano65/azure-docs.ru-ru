---
title: Атрибуты безопасности для Azure Service Fabric
description: Контрольный список общих атрибутов безопасности для оценки службы Azure Service Fabric
services: service-fabric
documentationcenter: ''
author: msmbaldwin
manager: barbkess
ms.service: service-fabric
ms.topic: conceptual
ms.date: 01/31/2019
ms.author: mbaldwin
ms.openlocfilehash: 0e6e0040577723955a4a9a0d8c3e966d1df5e788
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56110649"
---
# <a name="security-attributes-for-azure-service-fabric"></a>Атрибуты безопасности для Azure Service Fabric

Средства безопасности интегрированы в каждом аспекте службы Azure. В этой статье рассматриваются атрибуты безопасности, встроенные в Azure Service Fabric. Атрибут безопасности — это качество или функция службы Azure, которая позволяет службам предотвращать, обнаруживать уязвимости безопасности или реагировать на них.

Атрибуты безопасности разделены на следующие категории:
* профилактическая;
* сегментация сети;
* Обнаружение
* поддержка управления удостоверениями и доступом;
* журнал аудита;
* управление доступом (если используется);
* управление конфигурацией (если используется).

В каждой категории определяется, используется атрибут или нет (да/нет). Для некоторых служб атрибут может не применяться, это отображается как "Недоступно". Кроме того, может быть также предоставлена заметка или ссылка на дополнительную информацию об атрибуте.


[!INCLUDE [ecurity-attributes-service-fabric](../../includes/security-attributes-service-fabric.md)]