---
title: Рекомендации в Хранилище данных SQL — основные понятия | Документация Майкрософт
description: Узнайте, что такое рекомендации в Хранилище данных SQL и как они создаются.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/27/2018
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 57bce631a570f549d46a9b0beefcb5adce4decfc
ms.sourcegitcommit: 5a9be113868c29ec9e81fd3549c54a71db3cec31
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/11/2018
ms.locfileid: "44380120"
---
# <a name="sql-data-warehouse-recommendations"></a>Рекомендации в Хранилище данных SQL

В этой статье описываются рекомендации, которые отображаются в Хранилище данных SQL с использованием Помощника по Azure.  

Хранилище данных SQL предоставляет рекомендации, которые помогают непрерывно оптимизировать хранилище данных для повышения производительности. Рекомендации по хранилищу данных тесно интегрированы с [Помощником по Azure](https://docs.microsoft.com/azure/advisor/advisor-performance-recommendations), что позволяет получать рекомендации непосредственно на [портале Azure](https://aka.ms/Azureadvisor). Хранилище данных SQL каждый день анализирует текущее состояние хранилища данных, собирает данные телеметрии и выводит рекомендации по активной рабочей нагрузке. Ниже описаны поддерживаемые сценарии рекомендаций по хранилищу данных, а также инструкции по применению рекомендуемых действий.

Если вы хотите оставить отзыв о Помощнике по Хранилищу данных SQL или у вас возникли проблемы, свяжитесь с нами по адресу [sqldwadvisor@service.microsoft.com](mailto:sqldwadvisor@service.microsoft.com).   

Щелкните [здесь](https://aka.ms/Azureadvisor), чтобы проверить рекомендации уже сегодня. Сейчас эта функция применяется только к хранилищам данных 2-го поколения. 

## <a name="data-skew"></a>Неравномерное распределение данных

Неравномерное распределение данных может привести к дополнительному перемещению данных или к возникновению узких мест в ресурсах при выполнении рабочей нагрузки. В следующем документе описывается, как выявить неравномерное распределение данных и избежать его, выбрав оптимальный принцип распределения.

- [Обнаружение и устранение неравномерного распределения](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-distribute#how-to-tell-if-your-distribution-column-is-a-good-choice) 

## <a name="no-or-outdated-statistics"></a>Отсутствующие или устаревшие статистические данные

Неоптимальная статистика может существенно ухудшать производительность запросов из-за того, что оптимизатор запросов Хранилища данных SQL создает неоптимальные планы запросов. В следующем документе приводятся рекомендации по созданию и обновлению статистики:

- [Создание и обновление статистики для таблицы](https://docs.microsoft.com/azure/sql-data-warehouse/sql-data-warehouse-tables-statistics)

Чтобы просмотреть список таблиц, которых касаются эти рекомендации, выполните [скрипт T-SQL](https://github.com/Microsoft/sql-data-warehouse-samples/blob/master/samples/sqlops/MonitoringScripts/ImpactedTables). Для создания этих рекомендаций Помощник непрерывно выполняет один и тот же скрипт T-SQL.
