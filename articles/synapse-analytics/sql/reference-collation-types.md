---
title: Параметры сортировки
description: Типы коллажа, поддерживаемые в Azure Synapse S'L
author: filippopovic
ms.service: synapse-analytics
ms.topic: reference
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 4270677f8f5f77e1ada0b1d9385163dad762bbda
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81431544"
---
# <a name="database-collation-support-for-synapse-sql"></a>Поддержка сопоставления баз данных для Synapse S'L

Коллации обеспечивают локальную страницу кода, порядок сортировки и правила чувствительности символов для типов данных, основанных на символах. После выбора все столбцы и выражения, требующие сопоставления информации, наследуют выбранный сопоставление из настройки базы данных. Наследование по умолчанию может быть отменено путем явного указания другого сопоставления для типа данных, основанных на символах.

При создании новой базы данных пула S'L можно изменить сопоставление базы данных по умолчанию с портала Azure. Эта возможность упрощает создание новой базы данных с помощью одного из 3800 поддерживаемых сопоставлений баз данных.

Можно указать коллаж базы данных по умолчанию Synapse s-L по требованию во время создания с помощью оператора CREATE DATABASE.

## <a name="changing-collation"></a>Изменение коллажа
Чтобы изменить сопоставление по умолчанию для базы данных пула S'L, вы просто обновите поле коллажа в опыте подготовки. Например, если вы хотите изменить коллаж по умолчанию в случай чувствительного, вы просто переименуете коллаж из SQL_Latin1_General_CP1_CI_AS в SQL_Latin1_General_CP1_CS_AS. 

Чтобы изменить сопоставление по умолчанию для базы данных по запросу S'L, можно использовать заявление ALTER DATABASE.

## <a name="list-of-unsupported-collation-types"></a>Список неподдерживаемых типов коллажа
*    Japanese_Bushu_Kakusu_140_BIN
*    Japanese_Bushu_Kakusu_140_BIN2
*    Japanese_Bushu_Kakusu_140_CI_AI_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_VSS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS_VSS
*    Japanese_Bushu_Kakusu_140_CI_AI
*    Japanese_Bushu_Kakusu_140_CI_AI_WS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS
*    Japanese_Bushu_Kakusu_140_CI_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS
*    Japanese_Bushu_Kakusu_140_CI_AS_WS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS
*    Japanese_Bushu_Kakusu_140_CI_AS_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AI
*    Japanese_Bushu_Kakusu_140_CS_AI_WS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS
*    Japanese_Bushu_Kakusu_140_CS_AI_KS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS
*    Japanese_Bushu_Kakusu_140_CS_AS_WS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS
*    Japanese_Bushu_Kakusu_140_CS_AS_KS_WS
*    Japanese_XJIS_140_BIN
*    Japanese_XJIS_140_BIN2
*    Japanese_XJIS_140_CI_AI_VSS
*    Japanese_XJIS_140_CI_AI_WS_VSS
*    Japanese_XJIS_140_CI_AI_KS_VSS
*    Japanese_XJIS_140_CI_AI_KS_WS_VSS
*    Japanese_XJIS_140_CI_AS_VSS
*    Japanese_XJIS_140_CI_AS_WS_VSS
*    Japanese_XJIS_140_CI_AS_KS_VSS
*    Japanese_XJIS_140_CI_AS_KS_WS_VSS
*    Japanese_XJIS_140_CS_AI_VSS
*    Japanese_XJIS_140_CS_AI_WS_VSS
*    Japanese_XJIS_140_CS_AI_KS_VSS
*    Japanese_XJIS_140_CS_AI_KS_WS_VSS
*    Japanese_XJIS_140_CS_AS_VSS
*    Japanese_XJIS_140_CS_AS_WS_VSS
*    Japanese_XJIS_140_CS_AS_KS_VSS
*    Japanese_XJIS_140_CS_AS_KS_WS_VSS
*    Japanese_XJIS_140_CI_AI
*    Japanese_XJIS_140_CI_AI_WS
*    Japanese_XJIS_140_CI_AI_KS
*    Japanese_XJIS_140_CI_AI_KS_WS
*    Japanese_XJIS_140_CI_AS
*    Japanese_XJIS_140_CI_AS_WS
*    Japanese_XJIS_140_CI_AS_KS
*    Japanese_XJIS_140_CI_AS_KS_WS
*    Japanese_XJIS_140_CS_AI
*    Japanese_XJIS_140_CS_AI_WS
*    Japanese_XJIS_140_CS_AI_KS
*    Japanese_XJIS_140_CS_AI_KS_WS
*    Japanese_XJIS_140_CS_AS
*    Japanese_XJIS_140_CS_AS_WS
*    Japanese_XJIS_140_CS_AS_KS
*    Japanese_XJIS_140_CS_AS_KS_WS

Кроме того, пул S'L не поддерживает следующие типы коллажа:

*    SQL_EBCDIC1141_CP1_CS_AS
*    SQL_EBCDIC277_2_CP1_CS_AS

## <a name="checking-the-current-collation"></a>Проверка текущего коллажа
Чтобы проверить текущий коллаж для базы данных, можно запустить следующий фрагмент T-S'L:
```sql
SELECT DATABASEPROPERTYEX(DB_NAME(), 'Collation') AS Collation;
```
При пройде "Коллации" в качестве параметра свойства функция DatabasePropertyEx возвращает текущее сопоставление для указанной базы данных. Вы можете узнать больше о функции DatabasePropertyEx на MSDN.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительную информацию о наилучшей практике для пула S'L и S'L по требованию можно найти в следующих статьях:

- [Рекомендации по пулу S'L](best-practices-sql-pool.md)
- [Рекомендации по рекомендациям по запросу](best-practices-sql-on-demand.md)


