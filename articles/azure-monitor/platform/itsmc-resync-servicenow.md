---
title: Как вручную устранить проблемы синхронизации ServiceNow
description: Сбросить соединение с ServiceNow, чтобы оповещения в Microsoft Azure снова вызвали ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: f09f5010c18f5ea064b02f0fbbae107bf473e1f8
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313672"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>Как вручную устранить проблемы синхронизации ServiceNow

Azure Monitor может подключиться к сторонним поставщикам ИТ-услуг (ITSM). ServiceNow является одним из этих поставщиков.

По соображениям безопасности может потребоваться обновить маркер проверки подлинности, используемый для подключения к ServiceNow.
Используйте следующий процесс синхронизации для повторного активации соединения и обновления маркера:


1. Поиск решения в верхнем баннере поиска, а затем выберите соответствующие решения

    ![Новое подключение](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. На экране решения выберите "Выберите все" в фильтре подписки, а затем отфильтруйте на "ServiceDesk"

    ![Новое подключение](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Выберите решение подключения ITSM.
1. Выберите соединение ITSM в левом баннере.

    ![Новое подключение](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Выберите каждый разъем из списка. 
    1. Нажмите на имя соединителя, чтобы настроить его
    1. Удаление любых разъемов, которые больше не используются

    1. Обновление полей в соответствии с [этими определениями](https://docs.microsoft.com/azure/azure-monitor/platform/itsmc-connections) на основе типа партнера

    1. Нажмите на синхронизацию

       ![Новое подключение](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Нажмите на сохранить

        ![Новое подключение](media/itsmc-resync-servicenow/save-8bit.png)

е)    Просмотрите уведомления, чтобы увидеть, если процесс закончился с успехом 

## <a name="next-steps"></a>Next Steps

Подробнее о [соединениях управления ИТ-службами](itsmc-connections.md)
