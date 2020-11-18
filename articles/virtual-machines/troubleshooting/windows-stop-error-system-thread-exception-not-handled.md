---
title: Windows STOP error — исключение "системный поток 0x0000007E не обработано"
description: В этой статье приводятся инструкции по устранению проблем, когда в гостевой ОС возникают проблемы и требуется перезапустить виртуальную машину Azure. Сообщение будет иметь состояние "исключение системного потока не было обработано".
services: virtual-machines-windows
documentationcenter: ''
author: mibufo
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/04/2020
ms.author: v-mibufo
ms.openlocfilehash: 1ce594d9e3ffddf781c61717ae4534f0c7bd40f8
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681896"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Windows STOP error — исключение "системный поток 0x0000007E не обработано"

В этой статье приводятся инструкции по устранению проблем, когда в гостевой операционной системе (гостевой ОС) возникла проблема, которая попытается перезапустить виртуальную машину Azure. Отображаемое сообщение об ошибке: "исключение системного потока не было обработано".

## <a name="symptoms"></a>Симптомы

Если для просмотра снимка экрана выходных данных виртуальной машины используется [Диагностика загрузки](./boot-diagnostics.md) , вы увидите, что Windows должна перезапуститься с кодом ошибки "Системное исключение не обработано" или кодом "Stop".

![Снимок экрана, показывающий, что Windows зависла во время загрузки, "ваш компьютер столкнулся с проблемой и должен перезапуститься. Мы перезапускаем.» сообщение об ошибке и «СИСТЕМное исключение не обработано» код завершения.](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>Причина

Причина не может быть определена до тех пор, пока не будет проанализирован файл дампа памяти. Продолжайте выполнять накопление файла дампа памяти.

## <a name="solution"></a>Решение

Чтобы устранить эту проблему, сначала необходимо собрать файл дампа памяти для сбоя, а затем отправить файл в службу поддержки Майкрософт. Чтобы получить файл дампа, следуйте инструкциям в следующих двух разделах.

### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Подключение диска ОС к новой виртуальной машине восстановления

1. Чтобы подготовить виртуальную машину восстановления, выполните шаги 1-3 [команды восстановления виртуальной машины](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md).
1. Подключитесь к виртуальной машине восстановления с помощью подключение к удаленному рабочему столу.

### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Размещение файла дампа и отправка запроса в службу поддержки

1. На виртуальной машине восстановления перейдите в папку Windows на подключенном диске ОС. Например, если буква диска, назначенная подключенному диску ОС, обозначена как *F*, перейдите по адресу `F:\Windows` .
1. Найдите файл *Memory. dmp* и отправьте запрос в [службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) с прикрепленным файлом дампа памяти.
1. Если у вас возникли проблемы с нахождением файла *Memory. dmp* , следуйте указаниям по [созданию файла аварийной копии памяти с помощью вызовов прерывания (NMI), не поддерживающих маску](/windows/client-management/generate-kernel-or-complete-crash-dump).

Дополнительные сведения о вызовах NMI см. в разделе [вызов прерываний в пользовательском интерфейсе последовательной консоли Azure](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) .

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Устранение ошибок загрузки виртуальных машин Azure](./boot-error-troubleshoot.md)
