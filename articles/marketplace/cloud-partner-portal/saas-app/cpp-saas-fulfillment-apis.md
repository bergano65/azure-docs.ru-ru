---
title: Интерфейсы API выполнения SaaS — Azure Marketplace | Документация Майкрософт
description: Введение в версиях исполнение, предлагаемых API-интерфейсы, которые позволяют интегрировать ваши SaaS с Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: pbutlerm
ms.openlocfilehash: c7da46984d592abc6ed97d7490fde732bf26b0ba
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798743"
---
# <a name="saas-fulfillment-apis"></a>API исполнения SaaS

API-интерфейсы выполнения SaaS включить независимых поставщиков программного обеспечения (ISV) интегрировать свои приложения SaaS с Azure Marketplace. Эти интерфейсы API позволяют приложениям независимых поставщиков программного обеспечения для участия в все каналы commerce включен: direct, осуществляемое партнерами (торгового посредника) и поле под руководством.  Они являются обязательными для получения списка могут быть частью транзакции SaaS предложения в Azure Marketplace.

> [!WARNING]
> Текущая версия этого API — 2 версии, которые должны использоваться для всех новых SaaS предлагает.  Версии 1 API является устаревшим и поддерживается для поддержки существующих предложений.


## <a name="business-model-support"></a>Поддержка модели бизнеса

Этот API поддерживает следующие возможности модели бизнеса; Вы можете:

* Укажите несколько планов для предложения. Эти планы различаются по функциональности и могут быть разные цены.
* Отправить предложение о каждого сайта или на пользователя, выставление счетов в основе модели.
* Укажите ежемесячно и ежегодно (платный заранее) возможностью оплаты.
* Укажите закрытый цены для клиента на основании согласованный бизнес-соглашение.


## <a name="next-steps"></a>Дальнейшие действия

Если вы еще не сделано, зарегистрируйте свое приложение SaaS в [портала Azure](https://ms.portal.azure.com) как описано в [зарегистрировать приложение Azure AD](./cpp-saas-registration.md).  После этого используйте последнюю версию этого интерфейса для разработки: [Версия API SaaS выполнения 2](./cpp-saas-fulfillment-api-v2.md).
