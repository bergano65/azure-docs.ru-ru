---
title: Выбор между службой "Управление затратами Azure" и Cloudyn
description: Эта статья поможет определить, что лучше подойдет вам для управления затратами — служба "Управление затратами Azure" или Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/20/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: a46e02bfa9590b5ef34affa45f3c468fba6999c2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684972"
---
# <a name="choose-between-azure-cost-management-and-cloudyn"></a>Выбор между службой "Управление затратами Azure" и Cloudyn

Служба Cloudyn будет объявлена нерекомендуемой в конце 2020 года. Существующие функции Cloudyn переносятся непосредственно на портал Azure по мере возможности. Сейчас новые клиенты, за исключением клиентов CSP, не могут подключаться к этой службе. Поддержка существующего продукта сохранится до тех пор, пока он не будет объявлен полностью устаревшим.

Корпорация Майкрософт приобрела службу Cloudyn и переносит ее функции управления затратами с портала Cloudyn в Azure. Чтобы использовать новые функции, войдите на портал Azure и перейдите к разделу [Управление затратами и выставление счетов](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) Azure со списка. По сравнению с Cloudyn привычный интерфейс обеспечивает улучшенную производительность и меньшую задержку данных (примерно восемь часов).

Перенос основных функций, предлагаемых в рамках Соглашения Enterprise, подписки MSDN или подписки с оплатой по мере использования, в службу "Управление затратами Azure" завершено. Перенос подписок CSP в эту службу выполняется.

Если вы имеете категорию предложения, перенос которого еще не завершено, продолжайте использовать портал Cloudyn. Все остальные могут использовать службу "Управления затратами Azure".

## <a name="recommended-services-by-offer"></a>Рекомендуемые службы по предложениям

| Предложения Microsoft Azure | Рекомендуемая служба управления затратами |
| --- | --- |
| Соглашение Azure Enterprise | [Управление затратами Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure Web Direct (PAYG/MSDN) | [Управление затратами Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Azure для государственных организаций | [Управление затратами Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview) |
| Клиентское соглашение Майкрософт | [Управление затратами Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Клиентское соглашение Майкрософт, поддерживаемое партнерами | [Управление затратами Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/overview)|
| Azure CSP | [Cloudyn](https://azure.cloudyn.com); |


## <a name="available-cost-management-features"></a>Доступные функции службы "Управление затратами"

Некоторые из приведенных ниже функций доступны в Cloudyn, но теперь все они доступны в службе "Управление затратами Azure".

- Программные интерфейсы
- Рекомендации Azure по оптимизации затрат, включая приведенные ниже.
    - Рекомендации по выбору размера и завершению работы экземпляров Azure
    - Рекомендации по резервированию Azure
- сведения о бюджете;
- Анализ затрат
- Экспорт данных в учетную запись хранения Azure
- Низкая задержка
- Шаблон приложения для Power BI
- Поддержка тегов ресурсов
- Поддержка анализа затрат по нескольким облакам для AWS

## <a name="next-steps"></a>Дальнейшие действия
- Дополнительные сведения см. в разделе [Что такое Azure Cost Management?](../cost-management-billing-overview.md).