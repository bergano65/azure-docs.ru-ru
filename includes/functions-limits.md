---
author: ggailey777
ms.service: cost-management-billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 4643bb7f95e4fd1249d3ab6699c1f835c77f18fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77198349"
---
| Ресурс | [План потребления](../articles/azure-functions/functions-scale.md#consumption-plan) | [Премиум-план](../articles/azure-functions/functions-scale.md#premium-plan) | [План обслуживания приложений](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Масштабирование | Управление на основе событий | Управление на основе событий | [Руководство/автомасштабирование](../articles/app-service/manage-scale-up.md) | 
| Макс экземпляры | 200 | 100 | 10-20 |
|[Продолжительность тайм-аута](../articles/azure-functions/functions-scale.md#timeout) по умолчанию (мин) |5 | 30 |30<sup>2</sup> |
|[Максимальная продолжительность тайм-аута](../articles/azure-functions/functions-scale.md#timeout) (мин) |10 | неограниченный<sup>8</sup> | неограниченный<sup>3</sup> |
| Макс исходящих соединений (в экземпляре) | 600 активных (1200 всего) | unbounded | unbounded |
| Максимальный размер запроса (MB)<sup>4</sup> | 100 | 100 | 100 |
| Максимальная длина строки запроса<sup>4</sup> | 4096 | 4096 | 4096 |
| Максимальная<sup>длина</sup> URL-адреса Max | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) в каждом экземпляре | 100 | 210-840 | 100-840 |
| Максимальная память (GB на экземпляр) | 1.5 | 3.5-14 | 1.75-14 |
| Функциональные приложения в плане |100 |100 |неограниченный<sup>5</sup> |
| [Планы службы приложений](../articles/app-service/overview-hosting-plans.md) | 100 в [регионе](https://azure.microsoft.com/global-infrastructure/regions/) |100 шт. на одну группу ресурсов |100 шт. на одну группу ресурсов |
| Хранение<sup>6</sup> |1 ГБ |250 ГБ |50-1000 ГБ |
| Количество личных доменов на одно приложение</a> |500<sup>7</sup> |500 |500 |
| личных доменов [Поддержка SSL](../articles/app-service/configure-ssl-bindings.md) |неограниченное соединение SNI SSL включено | неограниченные соединения SNI SSL и 1 IP SSL включены |неограниченные соединения SNI SSL и 1 IP SSL включены | 

<sup>1</sup> Для конкретных ограничений для различных [App Service plan limits](../articles/azure-resource-manager/management/azure-subscription-service-limits.md#app-service-limits)вариантов плана Службы Приложений см.  
<sup>2</sup> По умолчанию тайм-аут для функций 1.x времени выполнения в плане службы app не ограничен.  
<sup>3</sup> Требует, чтобы план службы приложений был установлен [на Always On](../articles/azure-functions/functions-scale.md#always-on). Оплата по стандартным [тарифам](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> Эти ограничения [устанавливаются в хосте.](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config)  
<sup>5</sup> Фактическое количество функциональных приложений, которые можно разместить, зависит от активности приложений, размера экземпляров машины и соответствующего использования ресурсов.  
<sup>6</sup> Лимит хранилища — это общий размер содержимого во временном хранилище во всех приложениях в одном и том же плане Службы Приложений. План потребления использует файлы Azure для временного хранения.  
<sup>7</sup> Когда приложение функции размещается в [плане потребления,](../articles/azure-functions/functions-scale.md#consumption-plan)поддерживается только парапара CNAME. Для функциональных приложений в [плане Premium](../articles/azure-functions/functions-scale.md#premium-plan) или в [плане Службы приложений](../articles/azure-functions/functions-scale.md#app-service-plan)можно сопоставить пользовательский домен, используя cNAME или запись A.  
<sup>8</sup> Гарантировано на срок до 60 минут.