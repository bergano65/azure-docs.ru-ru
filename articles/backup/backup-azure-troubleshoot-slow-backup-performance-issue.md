---
title: Устранение неполадок, связанных с низкой производительностью архивации файлов и папок в службе архивации Azure
description: В этой статье представлены инструкции по устранению неполадок, которые помогут вам диагностировать причины проблем с производительностью службы архивации Azure.
services: backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
ms.openlocfilehash: 592a46077bb9e3469f3a42a95173af1b6db93510
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67704940"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Устранение неполадок, связанных с низкой производительностью архивации файлов и папок в службе архивации Azure
В этой статье представлены инструкции по устранению неполадок, которые помогут вам диагностировать причины низкой производительности при архивации файлов и папок при использовании службы архивации Azure. Если архивация файлов выполняется с помощью агента службы архивации Azure, этот процесс может занять больше времени, чем ожидалось. Ниже приведены причины возникновения задержек.

* [Наличие узких мест производительности на компьютере, для которого выполняется архивация](#cause1)
* [На работу службы архивации Azure влияет другой процесс или антивирусная программа](#cause2)
* [Агент службы архивации запущен на виртуальной машине Azure](#cause3)  
* [Архивация большого количества файлов (нескольких миллионов)](#cause4)

Прежде чем приступить к устранению неполадок, рекомендуется скачать и установить [последнюю версию агента службы архивации Azure](https://aka.ms/azurebackup_agent). Мы периодически обновляем агент службы архивации, чтобы устранить различные проблемы, добавить новые возможности и улучшить производительность.

Кроме того, настоятельно рекомендуется изучить статью [Служба архивации Azure: часто задаваемые вопросы](backup-azure-backup-faq.md) , чтобы убедиться, что проблемы c производительностью не вызваны общими ошибками конфигурации.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Причина. Узкие места производительности на компьютере
Наличие узких мест производительности на компьютере, для которого выполняется архивация, может приводить к задержкам. Например, причиной узких мест на компьютере может быть возможность чтения с диска или записи на него либо пропускная способность, доступная для отправки данных по сети.

Windows предоставляет встроенный инструмент, [системный монитор](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) , для обнаружения этих узких мест.

Ниже приведены некоторые счетчики производительности и диапазоны, которые могут оказаться полезными при обнаружении узких мест производительности для оптимизации архивации.

| Счетчик | Status |
| --- | --- |
| Логический диск (физический диск) — % времени простоя |• 100–50 % — работоспособное состояние.</br>• 49–20 % — состояние предупреждения или отслеживания.</br>• 19–0 % — критическое или недопустимое состояние. |
| Логический диск (физический диск) — среднее время выполнения операции чтения или записи на диске |• 0,001–0,015 мс — работоспособное состояние.</br>• 0,015–0,025 мс — состояние предупреждения или отслеживания.</br>• 0,026 мс или больше — критическое или недопустимое состояние. |
| Логический диск (физический диск) — текущая длина очереди диска (для всех экземпляров) |80 запросов в течение более 6 минут |
| Память — байт в невыгружаемом пуле |• Потребление менее 60 % ресурсов пула — работоспособное состояние.<br>• 61–80 % — состояние предупреждения или отслеживания.</br>• Более 80 % — критическое или недопустимое состояние. |
| Память — байт в выгружаемом пуле |• Потребление менее 60 % ресурсов пула — работоспособное состояние.</br>• 61–80 % — состояние предупреждения или отслеживания.</br>• Более 80 % — критическое или недопустимое состояние. |
| Доступная память (МБ) |• 50 % свободной памяти или больше — работоспособное состояние.</br>• 25 % свободной памяти — состояние отслеживания.</br>• 10 % свободной памяти — состояние предупреждения.</br>• Менее 100 МБ или 5 % свободной памяти — критическое или недопустимое состояние. |
| Processor--\%Processor Time (all instances) |• Less than 60% consumed = Healthy</br>• 61% to 90% consumed = Monitor or Caution</br>• 91% to 100% consumed = Critical |

> [!NOTE]
> If you determine that the infrastructure is the culprit, we recommend that you defragment the disks regularly for better performance.
>
>

<a id="cause2"></a>

## Cause: Another process or antivirus software interfering with Azure Backup
We've seen several instances where other processes in the Windows system have negatively affected performance of the Azure Backup agent process. For example, if you use both the Azure Backup agent and another program to back up data, or if antivirus software is running and has a lock on files to be backed up, the multiple locks on files might cause contention. In this situation, the backup might fail, or the job might take longer than expected.

The best recommendation in this scenario is to turn off the other backup program to see whether the backup time for the Azure Backup agent changes. Usually, making sure that multiple backup jobs are not running at the same time is sufficient to prevent them from affecting each other.

For antivirus programs, we recommend that you exclude the following files and locations:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe as a process
* C:\Program Files\Microsoft Azure Recovery Services Agent\ folders
* Scratch location (if you're not using the standard location)

<a id="cause3"></a>

## Cause: Backup agent running on an Azure virtual machine
If you're running the Backup agent on a VM, performance will be slower than when you run it on a physical machine. This is expected due to IOPS limitations.  However, you can optimize the performance by switching the data drives that are being backed up to Azure Premium Storage. We're working on fixing this issue, and the fix will be available in a future release.

<a id="cause4"></a>

## Cause: Backing up a large number (millions) of files
Moving a large volume of data will take longer than moving a smaller volume of data. In some cases, backup time is related to not only the size of the data, but also the number of files or folders. This is especially true when millions of small files (a few bytes to a few kilobytes) are being backed up.

This behavior occurs because while you're backing up the data and moving it to Azure, Azure is simultaneously cataloging your files. In some rare scenarios, the catalog operation might take longer than expected.

The following indicators can help you understand the bottleneck and accordingly work on the next steps:

* **UI is showing progress for the data transfer**. The data is still being transferred. The network bandwidth or the size of data might be causing delays.
* **UI is not showing progress for the data transfer**. Open the logs located at C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, and then check for the FileProvider::EndData entry in the logs. This entry signifies that the data transfer finished and the catalog operation is happening. Don't cancel the backup jobs. Instead, wait a little longer for the catalog operation to finish. If the problem persists, contact [Azure support](https://portal.azure.com/#create/Microsoft.Support).Процессор — \` загруженности процессора (все экземпляры)es and folders in Azure Backup
description: Provides troubleshooting guidance to help you diagnose the cause of Azure Backup performance issues
services: backup
author: saurabhsensharma
manager: saurabhsensharma
ms.service: backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.author: saurse
---
# Troubleshoot slow backup of files and folders in Azure Backup
This article provides troubleshooting guidance to help you diagnose the cause of slow backup performance for files and folders when you're using Azure Backup. When you use the Azure Backup agent to back up files, the backup process might take longer than expected. This delay might be caused by one or more of the following:

* [There are performance bottlenecks on the computer that’s being backed up.](#cause1)
* [Another process or antivirus software is interfering with the Azure Backup process.](#cause2)
* [The Backup agent is running on an Azure virtual machine (VM).](#cause3)  
* [You're backing up a large number (millions) of files.](#cause4)

Before you start troubleshooting issues, we recommend that you download and install the [latest Azure Backup agent](https://aka.ms/azurebackup_agent). We make frequent
updates to the Backup agent to fix various issues, add features, and improve performance.

We also strongly recommend that you review the [Azure Backup service FAQ](backup-azure-backup-faq.md) to make sure you're not experiencing any of the common configuration issues.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

<a id="cause1"></a>

## Cause: Performance bottlenecks on the computer
Bottlenecks on the computer that's being backed up can cause delays. For example, the computer's ability to read or write to disk, or available bandwidth to send data over the network, can cause bottlenecks.

Windows provides a built-in tool that's called [Performance Monitor](https://technet.microsoft.com/magazine/2008.08.pulse.aspx) (Perfmon) to detect these bottlenecks.

Here are some performance counters and ranges that can be helpful in diagnosing bottlenecks for optimal backups.

| Counter | Status |
| --- | --- |
| Logical Disk(Physical Disk)--%idle |• 100% idle to 50% idle = Healthy</br>• 49% idle to 20% idle = Warning or Monitor</br>• 19% idle to 0% idle = Critical or Out of Spec |
| Logical Disk(Physical Disk)--%Avg. Disk Sec Read or Write |• 0.001 ms to 0.015 ms  = Healthy</br>• 0.015 ms to 0.025 ms = Warning or Monitor</br>• 0.026 ms or longer = Critical or Out of Spec |
| Logical Disk(Physical Disk)--Current Disk Queue Length (for all instances) |80 requests for more than 6 minutes |
| Memory--Pool Non Paged Bytes |• Less than 60% of pool consumed = Healthy<br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Pool Paged Bytes |• Less than 60% of pool consumed = Healthy</br>• 61% to 80% of pool consumed = Warning or Monitor</br>• Greater than 80% pool consumed = Critical or Out of Spec |
| Memory--Available Megabytes |• 50% of free memory available or more = Healthy</br>• 25% of free memory available = Monitor</br>• 10% of free memory available = Warning</br>• Less than 100 MB or 5% of free memory available = Critical or Out of Spec |
| Processor--\%Processor Time (all instances) |• Менее 60 % — работоспособное состояние.</br>• 61–90 % — состояние предупреждения или отслеживания.</br>• 91–100 % — критическое состояние. |

> [!NOTE]
> Если выяснилось, что причина в инфраструктуре, то рекомендуется регулярно выполнять дефрагментацию дисков, чтобы повысить производительность.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Причина. Другой процесс или антивирусная программа будет мешать Azure Backup
Мы уже сталкивались с несколькими случаями, когда на производительность агента службы архивации Azure отрицательно влияли другие процессы, запущенные в системе Windows. Например, если для архивации данных используется как агент службы архивации Azure, так и другая программа, или если выполняется архивация файлов, заблокированных антивирусной программой. В таких ситуациях архивация может завершиться сбоем или занять больше времени, чем ожидалось.

Для решения этой проблемы рекомендуется отключить другие программы архивации и отследить время резервного копирования, выполняемого с помощью агента службы архивации Azure. Как правило, чтобы предотвратить эти конфликтные ситуации, достаточно не выполнять несколько заданий архивации одновременно.

Для антивирусных программ необходимо исключить следующие файлы и расположения:

* процесс C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe;
* папки C:\Program Files\Microsoft Azure Recovery Services Agent\;
* вспомогательное расположение (если не используется стандартное расположение).

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Причина. Агент резервного копирования, запущенный на виртуальной машине Azure
Если агент службы архивации запущен на виртуальной машине Azure, а не на физическом компьютере, это может привести к снижению производительности. Это ожидаемое поведение, так как максимальное количество операций ввода-вывода ограничено.  Чтобы оптимизировать производительность, рекомендуется переместить данные, для которых выполняется архивация, в службу хранилища Azure уровня "Премиум". Мы работаем над этой проблемой, и она будет устранена в следующей версии.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Причина. Резервное копирование большого количества (нескольких миллионов) файлы
Перемещение большого объема данных занимает больше времени, чем перемещение меньшего объема данных. В некоторых случаях длительность архивации обуславливается не только объемом данных, но количеством файлов или папок. Это особенно актуально при архивации миллионов небольших файлов (размером от нескольких байтов до нескольких килобайтов).

Причина этого состоит в том, что при архивации и переносе данных в Azure одновременно выполняется каталогизация файлов. Иногда операция каталогизации может занять больше времени, чем ожидалось.

Ниже приведены признаки, которые помогут выявить узкие места и принять соответствующие меры.

* **В пользовательском интерфейсе отображается ход выполнения передачи данных**. Идет перенос данных. Возможны задержки из-за пропускной способности сети или объема данных.
* **В пользовательском интерфейс не отображается ход выполнения передачи данных**. В этом случае необходимо открыть файлы журналов в папке C:\Program Files\Microsoft Azure Recovery Services Agent\Temp и проверить в них наличие записи FileProvider::EndData. Наличие этой записи означает, что передача данных завершена, и выполняется операция каталогизации. Не отменяйте задание архивации. Вместо этого подождите еще немного, пока не завершится операция каталогизации. Если проблема не исчезла, обратитесь в [службу поддержки Azure](https://portal.azure.com/#create/Microsoft.Support).
