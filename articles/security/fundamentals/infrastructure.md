---
title: Безопасность инфраструктуры Azure | Документы Майкрософт
description: В этой статье описывается, как корпорация Майкрософт обеспечивает безопасность центров обработки данных Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/06/2018
ms.author: terrylan
ms.openlocfilehash: 3da9fef8c7de248f30100f94724cd5dcdb7db8e3
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727075"
---
# <a name="azure-infrastructure-security"></a>Безопасность инфраструктуры Azure
Microsoft Azure работает в центрах обработки данных, управляемых и обслуживаемых корпорацией Майкрософт. Эти географически распределенные центры обработки данных соответствуют основным отраслевым стандартам обеспечения безопасности и надежности, например ISO/IEC 27001:2013 и NIST SP 800-53. Контроль, управление и администрирование центров обработки данных осуществляется операционным персоналом Майкрософт. Наши специалисты имеют многолетний опыт обслуживания крупнейшей в мире сети веб-служб с круглосуточным доступом.

В этой серии статей приводятся сведения о том, как корпорация Майкрософт обеспечивает безопасность инфраструктуры Azure. Темы статей:

- [Физическая безопасность](physical-security.md)
- [Доступность](infrastructure-availability.md)
- [Компоненты и границы](infrastructure-components.md)
- [Сетевая архитектура](infrastructure-network.md)
- [Рабочая сеть](production-network.md)
- [База данных SQL](infrastructure-sql.md)
- [Операции](infrastructure-operations.md)
- [Мониторинг](infrastructure-monitoring.md)
- [Целостность](infrastructure-integrity.md)
- [Защита данных](protection-customer-data.md)

## <a name="shared-responsibility-model"></a>Модель общей ответственности
Важно понимать разделение ответственности между вашей организацией и корпорацией Майкрософт. В локальной среде весь стек принадлежит вам, но при переходе в облако некоторые обязанности переходят к корпорации Майкрософт. На следующем рисунке показаны области обязанностей в зависимости от типа развертывания стека (программное обеспечение как услуга [SaaS], платформа как услуга [PaaS], инфраструктура как услуга [IaaS] и локальная среда).

![Рисунок с отображением обязанностей](./media/infrastructure/responsibility-zones.png)

Независимо от типа развертывания вы всегда отвечаете за следующее:

- Data
- Конечные точки
- Учетная запись
- управление доступом

Убедитесь, что вы понимаете разделение обязанностей между вами и корпорацией Майкрософт в развертывании SaaS, PaaS и IaaS. Дополнительные сведения см. на странице [общих обязанностей для облачных вычислений](https://gallery.technet.microsoft.com/Shared-Responsibilities-81d0ff91/file/153019/1/Shared%20responsibilities%20for%20cloud%20computing.pdf).

## <a name="next-steps"></a>Следующие шаги
Дополнительные сведения о действиях корпорации Майкрософт для защиты инфраструктуры Azure приведены в следующих статьях:

- [Объекты, локальная среда и физическая безопасность в Azure](physical-security.md)
- [Доступность инфраструктуры Azure](infrastructure-availability.md)
- [Компоненты и границы информационной системы Azure](infrastructure-components.md)
- [Сетевая архитектура Azure](infrastructure-network.md)
- [Рабочая сеть Azure](production-network.md)
- [Возможности безопасности Базы данных SQL Azure](infrastructure-sql.md)
- [Операции и управление в рабочей среде Azure](infrastructure-operations.md)
- [Мониторинг инфраструктуры Azure](infrastructure-monitoring.md)
- [Целостность инфраструктуры Azure](infrastructure-integrity.md)
- [Защита данных клиентов в Azure](protection-customer-data.md)


