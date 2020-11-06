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
ms.openlocfilehash: dd18d69009b9c150c4c12a755e9060cd5dfaccae
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424439"
---
# <a name="windows-stop-error---0x0000007e-system-thread-exception-not-handled"></a>Windows STOP error — исключение "системный поток 0x0000007E не обработано"

В этой статье приводятся инструкции по устранению проблем, когда в гостевой ОС возникают проблемы и требуется перезапустить виртуальную машину Azure. Сообщение будет иметь состояние "исключение системного потока не было обработано".

## <a name="symptoms"></a>Симптомы

При использовании [диагностики загрузки](./boot-diagnostics.md) для просмотра снимка экрана виртуальной машины вы увидите, что на снимке экрана отображается окно, требующее перезагрузки, при этом **не обрабатывается исключение потока системы** кода Stop или код ошибки **0x0000007E**.

![На снимке экрана показано, что Windows зависла во время загрузки с сообщением: "ваш компьютер столкнулся с проблемой и должен перезапуститься. Мы перезапускаем.» Код завершения: "исключение системного потока не обработано"](media/windows-stop-error-system-thread-exception-not-handled/windows-stop-error-system-thread-exception-not-handled-1.png)

## <a name="cause"></a>Причина

Причина не может быть определена до тех пор, пока не будет проанализирован файл дампа памяти. Продолжайте выполнять накопление файла дампа памяти.

## <a name="solution"></a>Решение

### <a name="collect-the-memory-dump-file"></a>Получение файла дампа памяти

Чтобы устранить эту проблему, сначала необходимо собрать файл дампа памяти для сбоя, а затем обратиться в службу поддержки с файлом дампа памяти. Чтобы получить файл дампа, выполните следующие действия.

#### <a name="attach-the-os-disk-to-a-new-repair-vm"></a>Подключение диска ОС к новой виртуальной машине восстановления

1. Выполните [шаги 1-3 команды восстановления виртуальной машины](./repair-windows-vm-using-azure-virtual-machine-repair-commands.md) , чтобы подготовить виртуальную машину восстановления.
2. Подключитесь к виртуальной машине восстановления с помощью подключение к удаленному рабочему столу.

#### <a name="locate-the-dump-file-and-submit-a-support-ticket"></a>Размещение файла дампа и отправка запроса в службу поддержки

1. На виртуальной машине восстановления перейдите в папку Windows на подключенном диске ОС. Если буква драйвера, назначенная подключенному диску ОС, обозначена как *F* , необходимо обратиться к `F:\Windows` .
2. Найдите файл **Memory. dmp** и отправьте запрос в [службу поддержки](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) с файлом дампа памяти.
3. Если у вас возникли проблемы с нахождением файла **Memory. dmp** , следуйте указаниям по [созданию файла аварийной копии памяти с помощью вызовов прерывания (NMI), не поддерживающих маску](/windows/client-management/generate-kernel-or-complete-crash-dump).

Дополнительные сведения о вызовах NMI см. [в разделе немаскированные вызовы прерываний (NMI) в пользовательском интерфейсе последовательной консоли](./serial-console-windows.md#use-the-serial-console-for-nmi-calls) .

## <a name="next-steps"></a>Дальнейшие шаги

> [!div class="nextstepaction"]
> [Устранение ошибок загрузки виртуальных машин Azure](./boot-error-troubleshoot.md)