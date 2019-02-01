---
title: Расписания обслуживания Azure (предварительная версия) | Документация Майкрософт
description: Составление расписания обслуживания позволяет клиентам планировать необходимые мероприятия по обслуживанию, которые служба "Хранилище данных SQL Azure" использует для развертывания новых функций, обновлений и исправлений.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: design
ms.date: 11/27/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 61537a4cb8ca1e6e98f2fa04959861eb03480fe9
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247261"
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
- [Дополнительные сведения](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) о создании групп действий и управлении ими.
- Дополнительные сведения о службе "Работоспособность служб Azure" см. в [этой статье](https://docs.microsoft.com/azure/service-health/service-health-overview).


