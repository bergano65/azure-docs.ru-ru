---
title: Сбой видеоиндекса и аварийное восстановление
titleSuffix: Azure Media Services
description: Узнайте, как перейти на вторичную учетную запись Video Indexer, если происходит сбой или сбой в работе регионального центра обработки данных.
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: ''
ms.topic: article
ms.custom: ''
ms.date: 07/29/2019
ms.author: juliako
ms.openlocfilehash: 17c21900448fcb6d0a40fe5407f3b8bd62f9e3e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79499622"
---
# <a name="video-indexer-failover-and-disaster-recovery"></a>Сбой видеоиндекса и аварийное восстановление

Video Indexer Media Service не обеспечивает мгновенное сбой службы при сбое или сбое в работе регионального центра обработки данных. В этой статье объясняется, как настроить среду для сбоя, чтобы обеспечить оптимальную доступность для приложений и свести к минимуму время восстановления в случае сбоя.

Мы рекомендуем настроить восстановление непрерывности бизнеса (BCDR) в региональных парах, чтобы воспользоваться политикой изоляции и доступности Azure. Дополнительные сведения см. в статье [Непрерывность бизнес-процессов и аварийное восстановление в службах BizTalk: пары регионов Azure](https://docs.microsoft.com/azure/best-practices-availability-paired-regions).

## <a name="prerequisites"></a>Предварительные требования

Подписка Azure. Если у вас еще нет подписки На Azure, зарегистрируйтесь на [бесплатную пробную версию Azure.](https://azure.microsoft.com/free/)

## <a name="failover-to-a-secondary-account"></a>Отказ от вторичной учетной записи

Для реализации BCDR необходимо иметь две учетные записи Video Indexer для обработки избыточности.

1. Создайте две учетные записи Video Indexer, подключенные к Azure (см. [Создать учетную запись Video Indexer).](connect-to-azure.md) Создайте одну учетную запись для основной области, а другую в парной лазурной области.
1. Если в основном регионе произошел сбой, переключитесь на индексацию с помощью вторичного счета.

> [!TIP]
> Можно автоматизировать BCDR, настроив оповещения о журнале активности для уведомлений о работоспособности службы в зависимости от [оповещений журнала «Создание активности» в уведомлениях службы.](../../service-health/alerts-activity-log-service-notifications.md)

Для получения информации об использовании нескольких арендаторов [см.](manage-multiple-tenants.md) Для реализации BCDR выберите один из этих двух вариантов: [учетная запись Video Indexer на одного арендатора](manage-multiple-tenants.md#video-indexer-account-per-tenant) или [подписка Azure на одного арендатора.](manage-multiple-tenants.md#azure-subscription-per-tenant)

## <a name="next-steps"></a>Дальнейшие действия

[Управление учетной записью Video Indexer, подключенной к Azure.](manage-account-connected-to-azure.md)
