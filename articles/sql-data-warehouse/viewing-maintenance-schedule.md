---
title: Расписания обслуживания Azure (предварительная версия) | Документация Майкрософт
description: Составление расписания обслуживания позволяет клиентам планировать необходимые мероприятия по обслуживанию, которые служба "Хранилище данных SQL Azure" использует для развертывания новых функций, обновлений и исправлений.
services: sql-data-warehouse
author: antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: design
ms.date: 10/06/2018
ms.author: anvang
ms.reviewer: igorstan
ms.openlocfilehash: 6fbf914c8035344d33e8d2739fb9d92d5757c3d1
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/26/2018
ms.locfileid: "47228165"
---
# <a name="viewing-a-maintenance-schedule"></a>Просмотр расписания обслуживания 

## <a name="portal"></a>Microsoft Azure

По умолчанию все созданные экземпляры службы "Хранилище данных SQL Azure" во время развертывания получают определенные системой основной и дополнительный период обслуживания длительностью 8 часов. Это можно изменить по завершении развертывания. Обслуживание не выполняется за пределами указанных периодов обслуживания без предварительного уведомления.
Чтобы просмотреть на портале расписание обслуживания, примененное к хранилищу данных, сделайте следующее.

Чтобы просмотреть на портале расписание обслуживания, примененное к хранилищу данных, сделайте следующее.
1.  Войдите на [портале Azure](https://portal.azure.com/).
2.  Выберите хранилище данных, которое хотите просмотреть. 
3.  В колонке обзора откроется выбранное Хранилище данных SQL Azure. Под пунктом расписания обслуживания (предварительная версия) будет отображаться расписание, примененное к выбранному хранилищу данных.

![Колонка "Обзор"](media/sql-data-warehouse-maintenance-scheduling/clear-overview-blade.PNG)

## <a name="next-steps"></a>Дополнительная информация
Дополнительные сведения о создании, просмотре оповещений и управлении ими с помощью Azure Monitor см. в статье [Создание и просмотр оповещений метрик, а также управление ими с помощью Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-usage).
Дополнительные сведения о действиях веб-перехватчика для правил генерации оповещений журнала см. в [этой статье](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook).
Дополнительные сведения о службе "Работоспособность служб Azure" см. в [этой статье](https://docs.microsoft.com/azure/service-health/service-health-overview).


