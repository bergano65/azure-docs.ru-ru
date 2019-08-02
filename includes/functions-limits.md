---
author: ggailey777
ms.service: billing
ms.topic: include
ms.date: 05/09/2019
ms.author: glenga
ms.openlocfilehash: aa43fe6c7999b8fa73419916870f9dfca0c01e3d
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68601370"
---
| Resource | [План потребления](../articles/azure-functions/functions-scale.md#consumption-plan) | [План Premium](../articles/azure-functions/functions-scale.md#premium-plan) | [План службы приложений](../articles/azure-functions/functions-scale.md#app-service-plan) <sup>1</sup> |
| --- | --- | --- | --- |
| Горизонтальное масштабирование | Управляемые событиями | Управляемые событиями | [Ручная или автомасштабирование](../articles/app-service/web-sites-scale.md) | 
| Максимальное число экземпляров | 200 | 20 | 10-20 |
|[Длительность ожидания](../articles/azure-functions/functions-scale.md#timeout) по умолчанию (мин.) |5 | 30 |30<sup>2</sup> |
|Максимальная [Длительность истечения времени ожидания](../articles/azure-functions/functions-scale.md#timeout) (мин.) |10 | unbounded | без привязки<sup>3</sup> |
| Максимальное число исходящих подключений (на экземпляр) | 600 активна (всего 1200) | unbounded | unbounded |
| Максимальный размер запроса (МБ)<sup>4</sup> | 100 | 100 | 100 |
| Максимальная длина строки запроса<sup>4</sup> | 4096 | 4096 | 4096 |
| Максимальная длина URL-адреса запроса<sup>4</sup> | 8192 | 8192 | 8192 |
| [ACU](../articles/virtual-machines/windows/acu.md) на экземпляр | 100 | 210-840 | 100-840 |
| Максимальный объем памяти (ГБ на экземпляр) | 1.5 | 3,5-14 | 1,75-14 |
| Приложения функций на план |100 |100 |без привязки<sup>5</sup> |
| [Планы службы приложений](../articles/app-service/overview-hosting-plans.md) | 100. США за [регион](https://azure.microsoft.com/global-infrastructure/regions/) |100 шт. на одну группу ресурсов |100 шт. на одну группу ресурсов |
| Хранилище<sup>6</sup> |1 ГБ |250 ГБ |50-1000 ГБ |
| Количество личных доменов на одно приложение</a> |500<sup>7</sup> |500 |500 |
| личных доменов [Поддержка SSL](../articles/app-service/app-service-web-tutorial-custom-ssl.md) |включена неограниченное подключение SNI SSL | неограниченные протоколы SNI SSL и 1 SSL на основе IP подключений |неограниченные протоколы SNI SSL и 1 SSL на основе IP подключений | 

<sup>1</sup> для определенных ограничений для различных параметров плана службы приложений см. раздел [ограничения плана службы приложений](../articles/azure-subscription-service-limits.md#app-service-limits).  
<sup>2</sup> по умолчанию время ожидания для выполнения функций 1. x в плане службы приложений не ограничено.  
<sup>3</sup> требует, чтобы план службы приложений был установлен в [Always on](../articles/azure-functions/functions-scale.md#always-on). Оплата по стандартным [тарифам](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>4</sup> эти ограничения задаются [в узле](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>5</sup> фактическое число приложений функций, которые можно разместить, зависит от активности приложений, размера экземпляров компьютера и соответствующего использования ресурсов.  
<sup>6</sup> предел хранилища — это общий размер содержимого во временном хранилище во всех приложениях в том же плане службы приложений. План потребления использует службы файлов Azure для временного хранения.  
<sup>7</sup> если приложение-функция размещено в [плане потребления](../articles/azure-functions/functions-scale.md#consumption-plan), поддерживается только параметр CNAME. Для приложений-функций в [плане Premium](../articles/azure-functions/functions-scale.md#premium-plan) или в [плане службы приложений](../articles/azure-functions/functions-scale.md#app-service-plan)можно сопоставлять личный домен с помощью записи CNAME или a.
