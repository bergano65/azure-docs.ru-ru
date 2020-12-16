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
ms.openlocfilehash: 17c0e63b02d61b3aecf70624bc97a468d18f66fe
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/16/2020
ms.locfileid: "97607513"
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
- **Создание**
   > **Хотите попробовать? У вас нет доступного кластера Kubernetes? Мы предоставляем Вам песочницу:**  
   > Быстро приступите к работе с [Azure Arc](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) в службе Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) или на виртуальной машине Azure.

   - **Создания**
      - [Установка клиентских средств](install-client-tools.md)
      - [Создание контроллера данных Azure Arc](create-data-controller.md) (сначала необходимо установить клиентские средства)
      - [Создайте группу серверов "база данных Azure для PostgreSQL" в службе "Дуга" Azure](create-postgresql-hyperscale-server-group.md) (сначала необходимо создать контроллер данных Arc Azure).
- [**Подробнее о службах данных с поддержкой Arc Azure**](https://azure.microsoft.com/services/azure-arc/hybrid-data-services)
- [**Сведения о службе "Дуга Azure"**](https://aka.ms/azurearc)
