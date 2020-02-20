---
title: Включение общих дисков для управляемых дисков Azure
description: Настройка управляемого диска Azure с общими дисками (Предварительная версия) для совместного использования на нескольких виртуальных машинах
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e7ada64a50d6ace6ea4d34db87e0501d0311071d
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77471715"
---
# <a name="enable-shared-disk"></a>Включить общий диск

В этой статье описывается, как включить функцию общих дисков (Предварительная версия) для управляемых дисков Azure. Общие диски Azure (Предварительная версия) — это новая функция для управляемых дисков Azure, которая позволяет подключать управляемые диски одновременно к нескольким виртуальным машинам. Присоединение управляемого диска к нескольким виртуальным машинам позволяет либо развернуть новые или перенести существующие кластерные приложения в Azure. 

Если вы ищете основные сведения об управляемых дисках с включенными общими дисками, см. статью [Общие диски Azure](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]