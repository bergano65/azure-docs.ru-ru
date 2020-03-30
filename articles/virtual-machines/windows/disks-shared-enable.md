---
title: Включить общие диски для управляемых дисков Azure
description: Налажить управляемый azure диск с общими дисками (предварительным просмотром), чтобы вы могли делиться им через несколько VMs
author: roygara
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.date: 02/18/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: e7ada64a50d6ace6ea4d34db87e0501d0311071d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471715"
---
# <a name="enable-shared-disk"></a>Включить общий диск

В этой статье рассказывается о том, как включить функцию общих дисков (предварительный просмотр) для управляемых дисков Azure. Общие диски Azure (предварительный просмотр) — это новая функция для управляемых дисков Azure, которая позволяет одновременно прикреплять управляемый диск к нескольким виртуальным машинам (Виртуальным машинам). Присоединение управляемого диска к нескольким ВМ позволяет развертывать новые или мигрировать в Azure. 

Если вы ищете концептуальную информацию о управляемых дисках, которые имеют общий обмен дисками, обратитесь к [общим дискам Azure.](disks-shared.md)
[!INCLUDE [virtual-machines-enable-shared-disk](../../../includes/virtual-machines-enable-shared-disk.md)]