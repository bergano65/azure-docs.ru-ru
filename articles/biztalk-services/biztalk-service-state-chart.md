---
title: Допустимые в различных состояниях задания в службах BizTalk | Документация Майкрософт
description: 'Действия и операции, разрешенные в другом состоянии MABS: остановка, запуск, перезапуск, приостановка, возобновление, удаление, масштабирование, обновление конфигурации и архивация'
services: biztalk-services
documentationcenter: ''
author: MandiOhlinger
manager: anneta
editor: ''
ms.assetid: aea738f3-ec76-4099-a41b-e17fea9e252f
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/08/2016
ms.author: mandia
ms.openlocfilehash: 71463b523f0afdf63a7c89ff2b3137ae8d091b3b
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086706"
---
# <a name="what-you-can-and-cant-do-using-the-biztalk-service-state"></a>Что можно и что нельзя сделать с помощью состояния службы BizTalk

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

В зависимости от текущего состояния службы BizTalk существуют операции, которые можно или нельзя выполнять со службой BizTalk.

Например, можно подготовить новую службу BizTalk. После успешного завершения этого процесса служба BizTalk переводится в состояние `active`. В активном состоянии службу BizTalk остановить, приостановить и удалить. Если при попытке остановить службу BizTalk произошел сбой, то она переходит в состояние `StopFailed`. Службу BizTalk в состоянии `StopFailed` можно перезапустить. При попытке выполнения запрещенной операции, например возобновления, произойдет следующая ошибка.

`Operation not allowed`

## <a name="view-the-possible-states"></a>Просмотр возможных состояний

В следующих таблицах перечислены операции или действия, которые могут быть выполнены, когда служба BizTalk находится в том или ином состоянии. Установленный флажок ✔ означает, что в этом состоянии операция разрешена. Пустая запись означает, что операция не может быть выполнена в этом состоянии.

| Состояние службы | Начало | Остановить | Перезагрузить | Приостановить | Продолжить | Delete (Удалить) | Масштаб | Блокировка изменений <br/> Параметр Configuration | Azure Backup |
| --- | --- | --- | --- | --- | --- | --- |--- | --- | --- |
| Активна |  | ✔ | ✔ | ✔ |  | ✔ |✔ |✔ |✔ |
| Отключено |  |  |  |  |  | ✔ | |  |  | 
| Приостановлено |  |  |  |  | ✔ | ✔ | |  | ✔ |
| Остановлено | ✔ |  | ✔ |  |  | ✔ | |  | ✔ |
| Обновление службы завершено с ошибкой |  |  |  |  |  | ✔ | |  |  | 
| Сбой отключения |  |  |  |  |  | ✔ | |  |  | 
| Сбой включения |  |  |  |  |  | ✔ | |  |  | 
| Сбой запуска <br/> Сбой остановки <br/> Сбой перезапуска | ✔ | ✔ | ✔ |  |  | ✔ | | ✔ | |
| Сбой приостановки <br/> Сбой продолжения|  |  |  | ✔ | ✔ | ✔ | |  |  | 
| Сбой создания <br/> Сбой восстановления |  |  |  |  |  | ✔ | |  |  | 
| Сбой обновления конфигурации  |  |  | ✔ |  |  | ✔ | |✔ | |
| Сбой масштабирования |  |  |  |  |  | ✔ |✔ | |  |



## <a name="see-also"></a>См. также
* [Просмотр вкладок "Панель мониторинга", "Монитор", "Масштаб", "Настройка" и "Гибридное подключение"](https://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
* [Службы BizTalk: диаграмма выпусков](https://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
* [Службы BizTalk: резервное копирование и восстановление](https://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
* [Службы BizTalk: регулирование](https://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
* [Службы BizTalk: имя и ключ издателя](https://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
* [Как приступить к работе с пакетом SDK для служб BizTalk Azure](https://go.microsoft.com/fwlink/p/?LinkID=302335)

