---
title: Расписания обслуживания Azure (предварительная версия) | Документация Майкрософт
description: Составление расписания обслуживания позволяет клиентам планировать необходимые мероприятия по обслуживанию, которые служба "Хранилище данных SQL Azure" использует для развертывания новых функций, обновлений и исправлений.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 11/27/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 3c683d5819ded4eb3d286969b8c3fc3f0bd42865
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/28/2018
ms.locfileid: "52497513"
---
# <a name="view-a-maintenance-schedule"></a>Просмотр расписания обслуживания 

## <a name="portal"></a>Microsoft Azure

По умолчанию все созданные экземпляры службы "Хранилище данных SQL Azure" во время развертывания получают основной и дополнительный период обслуживания длительностью восемь часов. Этот период можно изменить по завершении развертывания. Обслуживание не выполняется за пределами указанных периодов обслуживания без предварительного уведомления.

Чтобы просмотреть расписание обслуживания, примененное к хранилищу данных, сделайте следующее.

1.  Войдите на [портале Azure](https://portal.azure.com/).
2.  Выберите хранилище данных, которое необходимо просмотреть. 
3.  Выбранное хранилище данных откроется в колонке "Обзор". Под пунктом **Расписание обслуживания (предварительная версия)** будет отображаться расписание, примененное к данному хранилищу данных.

![Колонка "Обзор"](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>Дополнительная информация
- Дополнительные сведения о создании, просмотре оповещений и управлении ими с помощью Azure Monitor см. в [этой статье](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage).
- Дополнительные сведения о действиях веб-перехватчика для правил генерации оповещений журнала см. в [этой статье](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook).
- [Дополнительные сведения](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups) о создании групп действий и управлении ими.
- Дополнительные сведения о службе "Работоспособность служб Azure" см. в [этой статье](https://docs.microsoft.com/azure/service-health/service-health-overview).


