---
title: Планы обслуживания и квоты для весеннего облака Azure
description: Узнайте о квотах обслуживания и планах обслуживания для облака Azure Spring
author: bmitchell287
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 8a7ba3c3b9c19b2084b6892b55ac417da38ab047
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278895"
---
# <a name="quotas-and-service-plans-for-azure-spring-cloud"></a>Квоты и планы обслуживания для весеннего облака Azure

Все службы Azure устанавливают ограничения по умолчанию и квоты для ресурсов и функций.  В период предварительного просмотра Azure Spring Cloud предлагает только один план обслуживания.

В этой статье подробно описаны квоты службы, предлагаемые в течение текущего периода предварительного просмотра.

## <a name="azure-spring-cloud-service-tiers-and-quotas"></a>Уровни и квоты облачных служб Azure Spring

В период предварительного просмотра Azure Spring Cloud предлагает только один уровень обслуживания.

Ресурс | Сумма
------- | -------
vCPU | 4 на экземпляр обслуживания
Память | 8 GBytes на экземпляр обслуживания
Экземпляры службы Azure Spring Cloud по регионам на одну подписку | 10
Всего экземпляров приложений на экземпляр службы Azure Spring Cloud | 500
Всего экземпляров приложений для приложения Spring | 20
Постоянные тома | 10 x 50 ГБ

Когда вы достигнете квоты, вы получите 400 ошибок, которая гласит: "Квота превышает предел подписки на *подписку* в *регионе, где создается служба Облака Azure Spring.*

## <a name="next-steps"></a>Дальнейшие действия

Вы можете повысить лимит нескольких ограничений и квот. Если ресурс требует увеличения, [создайте запрос на поддержку.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)
