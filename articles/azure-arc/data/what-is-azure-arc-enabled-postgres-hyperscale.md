---
title: Что такое служба "PostgreSQL" в Azure ARC с поддержкой масштабирования?
description: Что такое служба "PostgreSQL" в Azure ARC с поддержкой масштабирования?
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 10f21067f48155a394ac20337d77e3e82aae64d8
ms.sourcegitcommit: 04297f0706b200af15d6d97bc6fc47788785950f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98985943"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>Что такое служба "PostgreSQL" в Azure ARC с поддержкой масштабирования?

PostgreSQL "Дуга Azure Enabled" — это одна из служб баз данных, доступных в составе служб данных, поддерживающих службу Arc Azure. Azure Arc позволяет запускать службы данных Azure локально, в пограничной среде и общедоступных облаках, используя Kubernetes и любую инфраструктуру. Ценность служб данных, включенных в службу Arc Azure, обходит:
- Всегда актуальное состояние
- Эластичное масштабирование
- Самостоятельная подготовка
- Унифицированное управление
- Поддержка сценариев без подключения

Дополнительные сведения см. по адресу:
- [Службы данных с поддержкой Azure Arc](overview.md)
- [Режимы подключения и требования](connectivity.md)

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="compare-solutions"></a>Сравнение решений

В этом разделе описывается, как PostgreSQL в Azure Arc отличается от "PostgreSQL" базы данных Azure для масштабирования (Цитус).

## <a name="azure-database-for-postgresql-hyperscale-citus"></a>Гипермасштабируемое (Citus) развертывание базы данных Azure для PostgreSQL

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Масштабирование базы данных SQL Azure для PostgreSQL (Цитус)":::

Это конструктивный коэффициент для ядра СУБД postgres, доступный как база данных как услуга в Azure (PaaS). Он работает с помощью расширения Цитус, которое обеспечивает возможность использования возможностей масштабирования. В этом конструктивном параметре служба работает в центрах обработки данных Майкрософт и работает корпорацией Майкрософт.

## <a name="azure-arc-enabled-postgresql-hyperscale"></a>PostgreSQL в службе "Дуга Azure" с поддержкой масштабирования

:::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="PostgreSQL в службе &quot;Дуга Azure&quot; с поддержкой масштабирования":::

Это конструктивный коэффициент для ядра СУБД postgres, доступный в службах данных, поддерживающих службу Arc Azure. Кроме того, она работает на основе расширения Цитус, которое обеспечивает возможность использования возможностей масштабирования. В этом конструктивном коэффициенте наши клиенты предоставляют инфраструктуру, в которой размещаются системы и работают их.

## <a name="next-steps"></a>Дальнейшие действия
- **Попробуйте.** Быстро Начните работу с помощью [Azure Arc JumpStart](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) в службе Azure Kubernetes Service (AKS), AWS эластичной Kubernetes Service (ЕКС), Google Cloud Kubernetes Engine (гке) или на виртуальной машине Azure. 

- **Создайте свой собственный.** Чтобы создать кластер Kubernetes, выполните следующие действия. 
   1. [Установка клиентских средств](install-client-tools.md)
   2. [Создание контроллера данных ARC в Azure](create-data-controller.md)
   3. [Создание группы серверов масштабирования базы данных Azure для PostgreSQL в службе "Дуга Azure"](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Подробнее о службах данных с поддержкой Arc Azure](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
   - [Сведения о службе "Дуга Azure"](https://aka.ms/azurearc)
