---
title: Включение общих дисков для управляемых дисков Azure
description: Настройка управляемого диска Azure с общими дисками для совместного использования на нескольких виртуальных машинах
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/16/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: e36c539cc1143490aeb4862c928589db5c502656
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86500615"
---
# <a name="enable-shared-disk"></a>Включить общий диск

В этой статье описывается, как включить функцию "Общие диски" для управляемых дисков Azure. Общие диски Azure — это новая функция для управляемых дисков Azure, которая позволяет подключать управляемые диски одновременно к нескольким виртуальным машинам. Подключение управляемого диска к нескольким виртуальным машинам позволяет разворачивать новые или переносить существующие кластеризованные приложения в Azure. 

Если вы ищете основные сведения об управляемых дисках с включенными общими дисками, см. статью [Общие диски Azure](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]