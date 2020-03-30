---
title: Введение в API Azure Cosmos DB и т.д.
description: В этой статье представлен обзор и основные преимущества API-изл. в Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: acd87fac5ec2edc40d27d98f073e13c0acae8d8a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79498593"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Введение в API Azure Cosmos DB и т.д. (предварительный просмотр)

Azure Cosmos DB — это глобально распределенная, многомодельная служба базы данных Майкрософт, необходимая для работы с критически важными приложениями. Он предлагает под ключ глобального распределения, упругое масштабирование пропускной способности и хранения, однозначные миллисекунды опоздал на 99-й процентиль, и гарантированно высокой доступности, все при поддержке ведущих в отрасли SLA.

[Etcd](https://github.com/etcd-io/etcd) — это распределенный магазин ключей/значений. В [Kubernetes](https://kubernetes.io/), etcd используется для хранения состояния и конфигурации кластеров Kubernetes. Обеспечение доступности, надежности и производительности etcd имеет решающее значение для общей работоспособности кластера, масштабируемости, доступности эластичности и производительности кластера Kubernetes. 

Ит-тактный API в Azure Cosmos DB позволяет использовать Azure Cosmos DB в качестве запасного хранилища для [Azure Kubernetes.](../aks/index.yml) API etcd в Azure Cosmos DB в настоящее время находится в режиме предварительной версии. Azure Cosmos DB реализует протокол провода и т.д. С помощью etcd API в Azure Cosmos DB разработчики автоматически получат высоконадежные, [доступные,](high-availability.md) [глобально распределенные](distribute-data-globally.md) Kubernetes. Этот API позволяет разработчикам масштабировать управление состоянием Kubernetes на полностью управляемом облачном сервисе PaaS. 

> [!NOTE]
> В отличие от других API в Azure Cosmos DB, вы не можете предоставить учетную запись API и т.д. через портал Azure, CLI или SDKs. Вы можете предоставить учетную запись API и т.д. путем развертывания шаблона менеджера ресурсов; подробные шаги посмотреть, [как предоставить Azure Kubernetes статье Azure Cosmos DB.](bootstrap-kubernetes-cluster.md) Azure Cosmos DB и т.д. API в настоящее время в ограниченном предварительном просмотре. Вы можете [зарегистрироваться для предварительного просмотра,](https://aka.ms/cosmosetcdapi-signup)заполнив форму регистрации.

## <a name="wire-level-compatibility"></a>Совместимость уровня провода

Azure Cosmos DB реализует протокол провода и т.д. версии 3 и позволяет серверам API [мастера использовать API-серверы Главного узла](https://kubernetes.io/docs/concepts/overview/components/) так же, как это было бы в локально установленной и т.д. среде. Ит-тактный API поддерживает взаимную аутентификацию TLS. 

На следующей диаграмме показаны компоненты кластера Kubernetes. В мастере кластера API Server использует Azure Cosmos DB и т.д., вместо локально установленных и т.д. 

![Azure Cosmos DB реализует протокол проср.](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Основные преимущества

### <a name="no-etcd-operations-management"></a>Нет ит-д-управления операциями

Как полностью управляемый родной облачный сервис, Azure Cosmos DB устраняет необходимость создания и управления разработчиками Kubernetes и т.д. Ит-тактный API в Azure Cosmos DB масштабируемый, высокодоступен, терпим опоре толерантен и обеспечивает высокую производительность. Накладные расходы на настройку репликации в нескольких узлах, выполнение обновления подвижного состава, патчи безопасности и мониторинг идки работоспособности и т.д. обрабатываются Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Глобальное распределение & высокой доступности 

Используя API и т.д., Azure Cosmos DB гарантирует 99,99% доступности данных, считывающих и записывающихся в одном регионе, и 99,999% доступности в нескольких регионах. 

### <a name="elastic-scalability"></a>Эластичное масштабирование

Azure Cosmos DB предлагает упругую масштабируемость для чтения и записи запросов в разных регионах.
По мере роста кластера Kubernetes учетная запись API в Azure Cosmos DB упруго масштабируется без каких-либо простоев. Хранение и т.д. данных в Azure Cosmos DB вместо мастер-узлов Kubernetes также обеспечивает более гибкое масштабирование мастер-узлов. 

### <a name="security--enterprise-readiness"></a>Готовность предприятия безопасности &

Когда данные и т.д. хранятся в Azure Cosmos DB, разработчики Kubernetes автоматически получают [встроенное шифрование в состоянии покоя,](database-encryption-at-rest.md) [сертификацию и соответствие](compliance.md)требованиям, а также [резервные и восстановительные возможности,](../synapse-analytics/sql-data-warehouse/backup-and-restore.md) поддерживаемые Azure Cosmos DB. 

## <a name="next-steps"></a>Дальнейшие действия

* [Использование Azure Kubernetes с Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Основные преимущества Azure Cosmos DB](introduction.md)
* [Руководство AKS по быстрому запуску двигателя](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)