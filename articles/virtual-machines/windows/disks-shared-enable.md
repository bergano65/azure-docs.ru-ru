---
title: Включение общих дисков для управляемых дисков Azure
description: Настройка управляемого диска Azure с общими дисками (Предварительная версия) для совместного использования на нескольких виртуальных машинах
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/13/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 7eb2f808269ec0f91725644d5afb82e12cbb885d
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77202363"
---
# <a name="enable-shared-disk"></a>Включить общий диск

В этой статье описывается, как включить функцию общих дисков (Предварительная версия) для управляемых дисков Azure. Общие диски Azure (Предварительная версия) — это новая функция для управляемых дисков Azure, которая позволяет подключать управляемые диски одновременно к нескольким виртуальным машинам. Присоединение управляемого диска к нескольким виртуальным машинам позволяет либо развернуть новые или перенести существующие кластерные приложения в Azure. 

Если вы ищете основные сведения об управляемых дисках с включенными общими дисками, см. статью [Общие диски Azure](disks-shared.md).
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]