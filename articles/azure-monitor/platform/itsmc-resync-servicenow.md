---
title: Как вручную исправить проблемы синхронизации ServiceNow
description: Сбросьте подключение к ServiceNow, чтобы оповещения в Microsoft Azure могли снова вызвать ServiceNow
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 04/12/2020
ms.openlocfilehash: a3382f93990612b0ab34eb0848cbf3d6577c44ff
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87087940"
---
# <a name="how-to-manually-fix-servicenow-sync-problems"></a>Как вручную исправить проблемы синхронизации ServiceNow

Azure Monitor может подключаться к поставщикам управления ИТ-службами (ITSM) сторонних производителей. ServiceNow — один из этих поставщиков.

По соображениям безопасности может потребоваться обновить маркер проверки подлинности, используемый для подключения к ServiceNow.
Используйте следующий процесс синхронизации для повторной активации подключения и обновления маркера:


1. Найдите решение в верхнем баннере поиска, а затем выберите соответствующие решения.

    ![Новое подключение](media/itsmc-resync-servicenow/solution-search-8bit.png)

1. На экране решения выберите "выделить все" в фильтре подписки, а затем отфильтруйте по "ServiceDesk".

    ![Новое подключение](media/itsmc-resync-servicenow/solutions-list-8bit.png)

1. Выберите решение для подключения ITSM.
1. Выберите подключение ITSM в левом баннере.

    ![Новое подключение](media/itsmc-resync-servicenow/itsm-connector-8bit.png)

1. Выберите каждый соединитель из списка. 
    1. Щелкните имя соединителя, чтобы настроить его.
    1. Удаление всех соединителей, которые больше не используются

    1. Обновление полей в соответствии с [этими определениями](./itsmc-connections.md) в зависимости от типа партнера

    1. Щелкните "синхронизировать"

       ![Новое подключение](media/itsmc-resync-servicenow/resync-8bit2.png)

    1. Щелкните "Сохранить"

        ![Новое подключение](media/itsmc-resync-servicenow/save-8bit.png)

е)    Просмотрите уведомления, чтобы узнать, завершился ли процесс успешно 

## <a name="next-steps"></a>Дальнейшие шаги

Дополнительные сведения о [подключениях управления ИТ Service Management](itsmc-connections.md)
