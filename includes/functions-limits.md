---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 05/04/2020
ms.author: glenga
ms.openlocfilehash: 37a61e270f95ffdc420536046e0ce1eda1e489b4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85391500"
---
| Ресурс |[План потребления](../articles/azure-functions/functions-scale.md#consumption-plan)|[План категории "Премиум"](../articles/azure-functions/functions-scale.md#premium-plan)|[Выделенный план](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/aks/quotas-skus-regions.md) |
| --- | --- | --- | --- | --- | --- |
|[Длительность ожидания](../articles/azure-functions/functions-scale.md#timeout) по умолчанию (мин.) |5 | 30 |30<sup>1</sup> | 30 | 30 |
|Макс. [Длительность ожидания](../articles/azure-functions/functions-scale.md#timeout) (мин.) |10 | без привязки к<sup>7</sup> | без привязки<sup>2</sup> | unbounded | unbounded |
| Максимальное число исходящих подключений (на экземпляр) | 600 активна (всего 1200) | unbounded | unbounded | unbounded | unbounded |
| Максимальный размер запроса (МБ)<sup>3</sup> | 100 | 100 | 100 | 100 | Зависит от кластера |
| Максимальная длина строки запроса<sup>3</sup> | 4096 | 4096 | 4096 | 4096 | Зависит от кластера |
| Максимальная длина URL-адреса запроса<sup>3</sup> | 8192 | 8192 | 8192 | 8192 | Зависит от кластера |
|[ACU](../articles/virtual-machines/windows/acu.md) на экземпляр | 100 | 210-840 | 100-840 | 210-250<sup>8</sup> | [Цены на AKS](https://azure.microsoft.com/pricing/details/container-service/) |
| Максимальный объем памяти (ГБ на экземпляр) | 1.5 | 3,5-14 | 1,75-14 | 3,5-14 | Поддерживается любой узел |
| Приложения функций на план |100 |100 |без привязки<sup>4</sup> | unbounded | unbounded |
| [Планы службы приложений](../articles/app-service/overview-hosting-plans.md) | 100. США за [регион](https://azure.microsoft.com/global-infrastructure/regions/) |100 шт. на одну группу ресурсов |100 шт. на одну группу ресурсов | - | - |
| Хранилище<sup>5</sup> |5 ТБ |250 ГБ |50-1000 ГБ | 1 TБ | н/д |
| Количество личных доменов на одно приложение</a> |500<sup>6</sup> |500 |500 | 500 | н/д |
| личных доменов [Поддержка SSL](../articles/app-service/configure-ssl-bindings.md) |включена неограниченное подключение SNI SSL | неограниченные протоколы SNI SSL и 1 SSL на основе IP подключений |неограниченные протоколы SNI SSL и 1 SSL на основе IP подключений | неограниченные протоколы SNI SSL и 1 SSL на основе IP подключений | н/д |

<sup>1</sup> по умолчанию время ожидания для выполнения функций 1. x в плане службы приложений не ограничено.  
<sup>2</sup> требует, чтобы план службы приложений был установлен в [Always on](../articles/azure-functions/functions-scale.md#always-on). Оплата по стандартным [тарифам](https://azure.microsoft.com/pricing/details/app-service/).  
<sup>3</sup> эти ограничения [задаются в узле](https://github.com/Azure/azure-functions-host/blob/dev/src/WebJobs.Script.WebHost/web.config).  
<sup>4</sup> фактическое число приложений функций, которые можно разместить, зависит от активности приложений, размера экземпляров компьютера и соответствующего использования ресурсов.  
<sup>5</sup> предел хранилища — это общий размер содержимого во временном хранилище для всех приложений в том же плане службы приложений. План потребления использует службы файлов Azure для временного хранения.  
<sup>6</sup> если приложение-функция размещено в [плане потребления](../articles/azure-functions/functions-scale.md#consumption-plan), поддерживается только параметр CNAME. Для приложений-функций в [плане Premium](../articles/azure-functions/functions-scale.md#premium-plan) или в [плане службы приложений](../articles/azure-functions/functions-scale.md#app-service-plan)можно сопоставлять личный домен с помощью записи CNAME или a.  
<sup>7</sup> гарантируется до 60 минут.  
<sup>8</sup> рабочих ролей — это роли, в которых размещаются клиентские приложения. Рабочие роли доступны в трех фиксированных размерах: один виртуальных ЦП/3,5 ГБ ОЗУ; 2 виртуальных ЦП/7 ГБ ОЗУ; Четыре виртуальных ЦП/14 ГБ ОЗУ.
