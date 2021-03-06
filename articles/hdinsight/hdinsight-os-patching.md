---
title: Настройка расписания обновления ОС для кластеров Azure HDInsight
description: Узнайте, как настроить расписание обновления путем частичной замены ОС для кластеров HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 07/01/2019
ms.openlocfilehash: a97a03f7ef20ae56cec04341fe76b79ee657547b
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73748478"
---
# <a name="configure-the-os-patching-schedule-for-linux-based-hdinsight-clusters"></a>Настройка расписания исправлений ОС для кластеров HDInsight под управлением Linux

> [!IMPORTANT]
> Образы Ubuntu становятся доступными для нового создания кластера Azure HDInsight в течение трех месяцев публикации. По состоянию на Январь 2019 запуск кластеров не производится. Клиенты должны использовать действия сценария или другие механизмы для исправления работающего кластера. Созданные кластеры будут всегда содержать последние обновления, включая самые последние обновления безопасности.

HDInsight предоставляет поддержку для выполнения общих задач в кластере, например для установки исправлений ОС, обновлений безопасности и перезагрузки узлов. Эти задачи выполняются с помощью следующих двух скриптов, которые могут выполняться как [действия сценария](hdinsight-hadoop-customize-cluster-linux.md)и настроены с параметрами.

- `schedule-reboots.sh` — выполните немедленное перезагрузку или запланируйте перезапуск на узлах кластера.
- `install-updates-schedule-reboots.sh` — установите все обновления, только обновления ядра, системы безопасности или только обновления ядра.

> [!NOTE]  
> Действия скрипта не будут автоматически применять обновления для всех будущих циклов обновления. Запускайте скрипты каждый раз, когда необходимо применить новые обновления для установки обновлений, а затем перезапустите виртуальную машину.

## <a name="restart-nodes"></a>Перезапустить узлы
  
[Расписание — перезагрузка](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/schedule-reboots.sh)— задает тип перезагрузки, который будет выполняться на компьютерах в кластере. При отправке действия скрипта настройте его для применения ко всем трем типам узлов: головной узел, Рабочий узел и Zookeeper. Если скрипт не применяется к типу узла, виртуальные машины для этого типа узла не будут обновлены или перезапущены.

`schedule-reboots script` принимает один числовой параметр:

| Параметр | Допустимые значения | Определение |
| --- | --- | --- |
| Тип выполняемой перезагрузки | 1 или 2 | Значение 1 включает перезапуск по расписанию (запланированный через 12-24 часов). Значение 2 включает немедленное перезагрузку (за 5 минут). Если параметр не задан, по умолчанию используется значение 1. |  

## <a name="install-updates-and-restart-nodes"></a>Установка обновлений и перезапуск узлов

Сценарий [install-updates-Schedule-reboots.sh](https://hdiconfigactions.blob.core.windows.net/linuxospatchingrebootconfigv02/install-updates-schedule-reboots.sh) предоставляет параметры для установки различных типов обновлений и ПЕРЕЗАПУСКА виртуальной машины.

Сценарий `install-updates-schedule-reboots` принимает два числовых параметра, как описано в следующей таблице.

| Параметр | Допустимые значения | Определение |
| --- | --- | --- |
| Тип устанавливаемых обновлений | 0, 1 или 2 | Значение 0 устанавливает только обновления ядра. Значение 1 устанавливает все обновления, а 2 устанавливает только обновления ядра и системы безопасности. Если параметр не указан, по умолчанию используется значение 0. |
| Тип выполняемой перезагрузки | 0, 1 или 2 | Значение 0 отключает перезапуск. Значение 1 включает перезапуск по расписанию, а 2 — немедленное перезагрузку. Если параметр не указан, по умолчанию используется значение 0. Пользователь должен изменить входной параметр 1 на входной параметр 2. |

> [!NOTE]
> Сценарий необходимо пометить как сохраненный после применения к существующему кластеру. В противном случае все новые узлы, созданные операциями масштабирования, будут использовать расписание обновления путем частичной замены ОС по умолчанию. Если применить сценарий как часть процесса создания кластера, он будет сохранен автоматически.

## <a name="next-steps"></a>Дальнейшие действия

Конкретные действия по использованию действий скрипта см. в следующих разделах: [Настройка кластеров HDInsight под управлением Linux с помощью действия сценария](hdinsight-hadoop-customize-cluster-linux.md).

* [Использование действия скрипта при создании кластера](hdinsight-hadoop-customize-cluster-linux.md#use-a-script-action-during-cluster-creation)
* [Применение действия скрипта в работающем кластере](hdinsight-hadoop-customize-cluster-linux.md#apply-a-script-action-to-a-running-cluster)
