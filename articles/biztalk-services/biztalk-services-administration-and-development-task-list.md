---
title: Список задач администрирования и разработки в службах BizTalk | Документация Майкрософт
description: Планирование и инструкции по развертыванию служб BizTalk Azure
services: biztalk-services
documentationcenter: ''
author: msftman
manager: erikre
editor: ''
ms.assetid: 0ab70b5b-1a88-4ba5-b329-ec51b785010e
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2016
ms.author: deonhe
ms.openlocfilehash: 6b8e0fea73dc24f7e680482be1f06fba6d702804
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58916246"
---
# <a name="administration-and-development-task-list-in-biztalk-services"></a>Список задач администрирования и разработки в службах BizTalk

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]
> 
> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

## <a name="getting-started"></a>Приступая к работе
При работе со службами BizTalk Microsoft Azure следует принимать во внимание несколько локальных и облачных компонентов. Для начала рассмотрим следующую схему.  

| Шаг | Ответственное лицо | Задача | Связанные ссылки |
| --- | --- | --- | --- |
| 1. |Администратор |Создание подписки Microsoft Azure с помощью учетной записи Майкрософт или учетной записи организации |[Портал Azure](https://portal.azure.com) |
| 2. |Администратор |Создание или подготовка службы BizTalk. |[Создание службы BizTalk](/previous-versions/azure/reference/dn232347(v=azure.100)) |
| 3. |Администратор |Регистрация развертывания службы BizTalk, принадлежащего вам или вашей компании. |[Регистрация и обновление развертывания службы BizTalk на портале служб BizTalk](/previous-versions/azure/hh689837(v=azure.100)) |
| 4. |Администратор |Применяется, если приложение использует службу адаптера BizTalk для подключения к локальной бизнес-системе или использует в качестве места назначения очередь или раздел.  Создание пространства имен служебной шины Azure. Предоставление этого пространства имен, значений имени поставщика служебной шины и ключа поставщика служебной шины разработчику. |[How to: Создание или изменение пространства имен службы служебной шины](../service-bus-messaging/service-bus-dotnet-get-started-with-queues.md) и [значения получить имя издателя и ключ издателя](biztalk-issuer-name-issuer-key.md) |
| 5. |Developer |Установка пакета SDK и создание проекта службы BizTalk в Visual Studio. |[Установка пакета SDK для служб Azure BizTalk](/previous-versions/azure/hh689760(v=azure.100)) и [Создание конечных точек расширенного обмена сообщениями в Azure](/previous-versions/azure/hh689766(v=azure.100)) |
| 6. |Developer |Развертывание проекта службы BizTalk в службе BizTalk, размещенной в Azure. |[Развертывание и обновление проекта служб BizTalk](/previous-versions/azure/hh689881(v=azure.100)) |
| 7. |Администратор |Применяется, если вы используете EDI.  Можно добавлять партнеров и создавать соглашения на портале служб BizTalk Microsoft Azure. При создании соглашения в список его параметров можно добавить мост и (или) преобразования, созданные разработчиком. |[Настройка EDI, AS2 и EDIFACT на портале служб BizTalk](/previous-versions/azure/hh689853(v=azure.100)) |
| 8. |Администратор |Использование [REST](/previous-versions/azure/reference/dn232347(v=azure.100)), наблюдение за работоспособностью службы BizTalk, включая мониторинг показателей производительности. |[Службы BizTalk. Вкладки панели мониторинга, мониторинг и масштабирование](https://go.microsoft.com/fwlink/p/?LinkID=302281) |
| 9. |Администратор |Использование портала служб BizTalk Microsoft Azure, управление артефактами, используемыми службами BizTalk, и отслеживание сообщений по мере их обработки файлами моста. |[Использование портала служб BizTalk](/previous-versions/azure/dn874043(v=azure.100)) |
| 10. |Администратор |Создание плана архивации для резервного копирования службы BizTalk. |[Непрерывность бизнес-процессов и аварийное восстановление в службах BizTalk](/previous-versions/azure/dn509557(v=azure.100)) |

## <a name="next-steps"></a>Следующие шаги
[Учебники и примеры](/previous-versions/azure/hh689895(v=azure.100))

[Создание проекта в Visual Studio](/previous-versions/azure/hh689811(v=azure.100))

[Установка пакета SDK служб Azure BizTalk](/previous-versions/azure/hh689760(v=azure.100))

## <a name="concepts"></a>Основные понятия
[Создание проекта в Visual Studio](/previous-versions/azure/hh689811(v=azure.100))  
[EDI, AS2 и EDIFACT обмена сообщениями (бизнес-бизнес)](/previous-versions/azure/hh689898(v=azure.100))  

## <a name="other-resources"></a>Другие ресурсы
[Добавление источника, назначения и моста, конечные точки обмена сообщениями](/previous-versions/azure/hh689877(v=azure.100))  
[Обучение и Создание сопоставлений и преобразований сообщений](/previous-versions/azure/hh689905(v=azure.100))  
[Использование службы адаптера BizTalk (BAS)](/previous-versions/azure/hh689889(v=azure.100))  
[Службы Azure BizTalk](https://go.microsoft.com/fwlink/p/?LinkID=303664)

