---
title: Развертывание моделей в рабочей среде — командный процесс обработки и анализа данных
description: Способы развертывания моделей в рабочей среде для их использовании при принятии бизнес-решений.
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/30/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 94843bfc30b0d0d44284d533c715ff9632e6a6bd
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492366"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Развертывание моделей в рабочей среде должно играть активную роль в принятии бизнес-решений

Развертывание в рабочей среде позволяет использовать модель для выполнения активной роли в организации. Прогнозы, полученные от развернутой модели можно использовать для принятия бизнес-решений.

## <a name="production-platforms"></a>Рабочие платформы

Поместить модели в рабочую среду можно с использованием разных подходов и платформ. Вот некоторые из них:

- [Где развертываются модели с Машинное обучение Azure](../service/how-to-deploy-and-where.md)
- [Развертывание модели на сервере SQL Server](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Сервер машинного обучения Майкрософт](https://docs.microsoft.com/sql/advanced-analytics/r/r-server-standalone)

>[!NOTE]
>Перед развертыванием необходимо убедиться, что показатели задержки модели достаточно малы для использования в рабочей среде.
>

>[!NOTE]
>Разработка с помощью Студии машинного обучения Azure описывается в разделе [Развертывание веб-службы машинного обучения Azure](../studio/publish-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>A/B-тестирование

Если в рабочей среде используется несколько моделей, удобно выполнять [A/B-тестирование](https://en.wikipedia.org/wiki/A/B_testing), чтобы сравнить их эффективность. 
 
## <a name="next-steps"></a>Дальнейшие действия

Также доступны пошаговые руководства, которые демонстрируют все этапы процесса для **конкретных сценариев**. Эти этапы с иллюстрациями и краткими описаниями перечислены в статье [Пошаговые руководства по процессу обработки и анализа данных группы](walkthroughs.md). В них показано, как объединить облачные и локальные средства и службы в единый рабочий процесс или конвейер, чтобы создать интеллектуальное приложение. 
