---
title: Защитник Azure для SQL — преимущества и возможности
description: Узнайте о преимуществах и возможностях защитника Azure для SQL.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: conceptual
ms.service: security-center
ms.custom: references_regions
manager: rkarlin
ms.openlocfilehash: dcdbc3efba53f78890816721b6659aa69553d6ea
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91301617"
---
# <a name="introduction-to-azure-defender-for-sql"></a>Введение в защитник Azure для SQL

Защитник Azure для SQL включает два плана защитника Azure, которые расширяют [пакет безопасности данных](../azure-sql/database/advanced-data-security.md) центра безопасности Azure для защиты баз данных и их данных в любом расположении. 

## <a name="availability"></a>Доступность

|Аспект|Подробности|
|----|:----|
|Состояние выпуска:|**Azure Defender для серверов базы данных SQL Azure** — общедоступная версия<br>**Azure Defender для серверов SQL Server на виртуальных машинах** — Предварительная версия|
|Цены|Счета за два плана, которые формируют **защитник Azure для SQL** , выставляются, как показано на [странице с ценами](security-center-pricing.md) .|
|Защищенные версии SQL:|База данных SQL Azure <br>Управляемый экземпляр SQL Azure<br>Azure Synapse Analytics (ранее — Хранилище данных SQL)<br>SQL Server (все поддерживаемые версии)|
|Облако.|![Да](./media/icons/yes-icon.png) Коммерческие облака<br>![Да](./media/icons/yes-icon.png) US Gov<br>![Нет](./media/icons/no-icon.png) Gov Китая, другое gov|
|||

## <a name="what-does-azure-defender-for-sql-protect"></a>Что защищает защитник Azure для SQL?

**Защитник Azure для SQL** состоит из двух отдельных планов защитника Azure:

- Защита **Azure Defender для серверов базы данных SQL Azure** :
  - [База данных SQL Azure](../azure-sql/database/sql-database-paas-overview.md)
  - [Управляемый экземпляр SQL Azure](../azure-sql/managed-instance/sql-managed-instance-paas-overview.md)
  - [Azure Synapse Analytics](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md)

- Служба **"защитник Azure для серверов SQL" на компьютерах (Предварительная версия)** расширяет возможности защиты для собственных серверов SQL Azure, позволяя полностью поддерживать гибридные среды и защищать серверы SQL Server (все поддерживаемые версии), размещенные в Azure, в других облачных средах и даже на локальных компьютерах.


## <a name="what-are-the-benefits-of-azure-defender-for-sql"></a>Каковы преимущества защитника Azure для SQL?

Эти два плана включают функции для определения и устранения потенциальных уязвимостей базы данных и обнаружения аномальных действий, которые могут указывать на угрозы для баз данных.

- [Оценка уязвимостей](../azure-sql/database/sql-vulnerability-assessment.md) . Служба проверки позволяет обнаруживать, записывать и помогать устранять потенциальные уязвимости базы данных. В ходе сканирования оценки содержатся общие сведения о состоянии безопасности компьютеров SQL и сведения о любых выводах безопасности.

- [Advanced Threat protection](../azure-sql/database/threat-detection-overview.md) — служба обнаружения, которая постоянно отслеживает серверы SQL Server для таких угроз, как атака путем внедрения кода SQL, атаки методом подбора и нарушения прав. Эта служба предоставляет оповещения системы безопасности, ориентированные на действия в центре безопасности Azure, с подробными сведениями о подозрительных действиях, рекомендации по устранению угроз и возможности продолжения исследований с помощью Sentinel.


## <a name="what-kind-of-alerts-does-azure-defender-for-sql-provide"></a>Какие оповещения предоставляет Azure Defender для SQL?

Оповещения системы безопасности активируются, если таковые имеются:

- **Потенциальные атаки путем внедрения кода SQL** , включая уязвимости, обнаруженные при формировании приложением ошибочной инструкции SQL в базе данных
- **Аномальный доступ к базам данных и шаблоны запросов** , например, ненормальное большое число неудачных попыток входа с разными учетными данными (попытка принудительного подбора)
- **Подозрительная активность базы данных** — например, изменение в назначении хранилища экспорта для операции импорта и экспорта SQL

Оповещения содержат сведения об инциденте, который их инициировал, а также рекомендации по исследованию и исправлению угроз.



## <a name="next-steps"></a>Дальнейшие действия

В этой статье вы узнали о защитнике Azure для SQL.

Связанные материалы см. в следующих статьях: 

- [Включение защитника Azure для серверов SQL Server на компьютерах](defender-for-sql-usage.md)
- [Включение защитника Azure для серверов базы данных SQL](../azure-sql/database/advanced-data-security.md)
- [Список оповещений защитника Azure для SQL](alerts-reference.md#alerts-sql-db-and-warehouse)