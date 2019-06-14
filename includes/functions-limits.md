---
author: ggailey777
ms.service: billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: 9b98e1a1f5243584d0ca4b1490e25302ec26b465
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67050537"
---
| Ресурс | [План потребления](../articles/azure-functions/functions-scale.md#consumption-plan) | [План "премиум"](../articles/azure-functions/functions-scale.md#premium-plan) | [План службы приложений](../articles/azure-functions/functions-scale.md#app-service-plan)<sup>1</sup> |
| --- | --- | --- | --- |
| Масштабирование | На основе событий | На основе событий | [Ручное или автоматическое масштабирование](../articles/app-service/web-sites-scale.md) | 
|По умолчанию [длительность времени ожидания](../articles/azure-functions/functions-scale.md#timeout) (мин) |5 | 30 |30<sup>2</sup> |
|Max [длительность времени ожидания](../articles/azure-functions/functions-scale.md#timeout) (мин) |10 | unbounded | unbounded<sup>3</sup> |
| Максимальное число исходящих подключений (для каждого экземпляра) | 600 active (всего 1200) | unbounded | unbounded |
| Запрос на максимальный размер (МБ)<sup>4</sup> | 100 | 100 | 100 |
| Максимальная длина строки запроса<sup>4</sup> | 4096 | 4096 | 4096 |
| Максимальная длина URL-адрес запроса<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) каждого экземпляра | 100 | 210-840 | 100-840 |
| Максимальный объем памяти (ГБ на один экземпляр) | 1.5 | 3.5-14 | 1.75-14 |
| Приложения-функции на план |100 |100 |unbounded<sup>5</sup> |
| [Планы службы приложений](../articles/app-service/overview-hosting-plans.md) | 100 на [регион](https://azure.microsoft.com/global-infrastructure/regions/) |100 шт. на одну группу ресурсов |100 шт. на одну группу ресурсов |
| Хранилище<sup>6</sup> |1 GB |250 ГБ |50-1000 ГБ |
| Количество личных доменов на одно приложение</a> |500<sup>7</sup> |500 |500 |
| личных доменов [Поддержка SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |Не поддерживается, групповой сертификат для *. azurewebsites.net по умолчанию| unbounded SNI SSL и 1 подключения SSL на ОСНОВЕ IP включен |unbounded SNI SSL и 1 подключения SSL на ОСНОВЕ IP включен | 

<sup>1</sup> определенные ограничения для различных параметров плана службы приложений см. в разделе [ограничения плана службы приложений](../articles/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup> по умолчанию время ожидания для выполнения функций 1.x в рамках плана службы приложений не ограничено.  
<sup>3</sup> план службы приложений должны быть установлены [Always On](../articles/azure-functions/functions-scale.md#always-on). Оплата по стандартным [тарифы](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> эти ограничения действуют [в узле](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> фактическое число приложений-функций, которые можно разместить зависит от интенсивностью работы приложений, размером экземпляров машин и степенью использования соответствующих ресурсов.
<sup>6</sup> предельный размер хранилища — это общий размер содержимого во временное хранилище для всех приложений в одном плане службы приложений. План потребления службы файлов Azure использует для временного хранилища.  
<sup>7</sup> при размещении приложения-функции в [план потребления](../articles/azure-functions/functions-scale.md#consumption-plan), поддерживается только параметр CNAME. Для приложений-функций в [план "премиум"](../articles/azure-functions/functions-scale.md#premium-plan) или [план службы приложений](../articles/azure-functions/functions-scale.md#app-service-plan), можно сопоставить личный домен с помощью записи CNAME или A-запись.
