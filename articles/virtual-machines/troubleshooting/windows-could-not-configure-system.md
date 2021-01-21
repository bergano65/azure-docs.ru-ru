---
title: Устранение неполадок Windows, из-за которых не удается завершить настройку системы
titlesuffix: Azure Virtual Machines
description: В этой статье приведены инструкции по устранению проблем, в которых процесс Sysprep предотвращает загрузку виртуальной машины Azure.
services: virtual-machines-windows, azure-resource-manager
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 5fc57a8f-5a0c-4b5f-beef-75eecb4d310d
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: troubleshooting
ms.date: 09/09/2020
ms.author: v-miegge
ms.openlocfilehash: 6cb3467fec99bd12810ed058a61de1be7b39cdd0
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2021
ms.locfileid: "98629595"
---
# <a name="troubleshoot-windows-could-not-finish-configuring-the-system"></a>Устранение неполадок Windows, из-за которых не удается завершить настройку системы

В этой статье приведены инструкции по устранению проблем, из-за которых процесс Sysprep предотвращает загрузку виртуальной машины Azure.

## <a name="symptom"></a>Симптом

При использовании [диагностики загрузки](./boot-diagnostics.md) для просмотра снимка экрана виртуальной машины вы увидите, что на снимке экрана отображается ошибка "установить Windows" при запуске служб программой установки Windows. Отобразится сообщение об ошибке:

`Windows could not finish configuring the system. To attempt to resume configuration, restart the computer. Setup is starting services`

  ![На рис. 1 показана ошибка установки Windows со следующим сообщением: "Windows не удалось завершить настройку системы. Чтобы попытаться возобновить настройку, перезагрузите компьютер. Программа установки запускает службы "](./media/windows-could-not-configure-system/1-windows-error-configure.png)

## <a name="cause"></a>Причина

Эта ошибка возникает, если операционной системе (ОС) не удается завершить [процесс Sysprep](/windows-hardware/manufacture/desktop/sysprep-process-overview). Эта ошибка возникает при первой загрузке обобщенной виртуальной машины. Если вы столкнулись с этой проблемой, воссоздайте обобщенный образ, так как образ находится в неразвертываемом состоянии и не может быть восстановлен.

## <a name="solution"></a>Решение

> [!TIP]
> Если у вас есть недавняя резервная копия виртуальной машины, можно попытаться [восстановить виртуальную машину из резервной копии](../../backup/backup-azure-arm-restore-vms.md) , чтобы устранить проблему загрузки.

Чтобы устранить эту проблему, следуйте [указаниям Azure по подготовке и записи образа](../windows/upload-generalized-managed.md) и подготовке нового обобщенного образа.