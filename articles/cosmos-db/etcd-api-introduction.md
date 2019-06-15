---
title: Введение в Azure Cosmos DB etcd API
description: Эта статья содержит обзор и ключ преимущества etcd API в Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 6c7fcb1429438ee024cb226b63cfcdcab05ed9f8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65205807"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Введение в Azure Cosmos DB etcd API (Предварительная версия)

Azure Cosmos DB — глобально распределенная многомодельная база данных служба Майкрософт для критически важных приложений. Он предлагает готовое Глобальное распределение, гибкое Масштабирование пропускной способности и хранилища, миллисекунд задержки на 99-го процентиля и гарантированную высокую доступность — все это прописано в отрасли соглашение об уровне ОБСЛУЖИВАНИЯ по.

[Etcd](https://github.com/etcd-io/etcd) — это хранилище распределенных ключ/значение. В [Kubernetes](https://kubernetes.io/), etcd используется для хранения состояния и конфигурации кластеров Kubernetes. Обеспечение доступности, надежности и производительности etcd крайне важны для общей исправности кластера, масштабируемости, эластичности доступности и производительности кластера Kubernetes. 

Etcd API в Azure Cosmos DB позволяет использовать Azure Cosmos DB в качестве серверного хранилища для [Azure Kubernetes](../aks/index.yml). API etcd в Azure Cosmos DB в настоящее время находится в режиме предварительной версии. Azure Cosmos DB реализует протокол коммутации etcd. С помощью etcd API в Azure Cosmos DB, разработчикам будет автоматически очень надежным, [доступных](high-availability.md), [глобально распределенная](distribute-data-globally.md) Kubernetes. Этот API позволяет разработчикам развернуть управление состоянием Kubernetes на собственный это полностью управляемая облачная служба PaaS. 

> [!NOTE]
> В отличие от других интерфейсах API в Azure Cosmos DB вы не сможете подготовить учетную запись API etcd через портал Azure, интерфейса командной строки или пакетов SDK. Вы можете подготовить учетную запись API etcd, развернув шаблон Resource Manager. Подробные инструкции см. в разделе [том, как подготовить Azure Kubernetes с помощью Azure Cosmos DB](bootstrap-kubernetes-cluster.md) статьи. Etcd API Azure Cosmos DB в настоящее время находится в ограниченной предварительной версии. Вы можете [регистрации для предварительного просмотра](https://aka.ms/cosmosetcdapi-signup), заполнив форму регистрации.

## <a name="wire-level-compatibility"></a>Совместимость на уровне сети

Azure Cosmos DB реализует протокол коммутации etcd версии 3 и позволяет [главный узел](https://kubernetes.io/docs/concepts/overview/components/) серверов API для использования Azure Cosmos DB, так же, как произойдет в среде etcd установленным локально. Etcd API поддерживает взаимной проверки подлинности TLS. 

В примере ниже показан компоненты кластера Kubernetes. В шаблоне кластера сервера API использует etcd API Azure Cosmos DB, вместо etcd установленным локально. 

![Azure Cosmos DB, реализация etcd-протокол связи](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Основные преимущества

### <a name="no-etcd-operations-management"></a>Нет управления операциями etcd

Как полностью управляемая собственного облачная служба Azure Cosmos DB избавляет от необходимости Kubernetes разработчикам создавать и настраивать etcd. Etcd API в Azure Cosmos DB является масштабируемых и высокодоступных, отказоустойчивых и обеспечивает высокую производительность. Затраты на настройки репликации между несколькими узлами, выполнение последовательного обновления, исправления безопасности, и наблюдение за работоспособностью etcd обрабатываются с помощью Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Глобальное распределение и высокий уровень доступности 

С помощью etcd API, Azure Cosmos DB гарантирует 99,99% доступность для операций чтения данных и записывает в одном регионе, а также 99,999% доступности в нескольких регионах. 

### <a name="elastic-scalability"></a>Эластичное масштабирование

Azure Cosmos DB предлагает гибкой масштабируемостью для чтения и записи запросов в разных регионах.
По мере роста кластера Kubernetes в учетную запись etcd API в Azure Cosmos DB гибко масштабировать без простоев. Хранение etcd данных в Azure Cosmos DB вместо главные узлы Kubernetes также позволяет более гибкое масштабирование главного узла. 

### <a name="security--enterprise-readiness"></a>Готовность к безопасности и предприятия

Когда etcd данные хранятся в Azure Cosmos DB, разработчикам Kubernetes автоматически получают [встроенного шифрования при хранении](database-encryption-at-rest.md), [сертификация и соответствие](compliance.md), и [резервное копирование и восстановление возможности](online-backup-and-restore.md) поддерживается Azure Cosmos DB. 

## <a name="next-steps"></a>Дальнейшие действия

* [Как использовать Azure Kubernetes с помощью Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Основные преимущества Azure Cosmos DB](introduction.md)
* [Краткое руководство AKS ядра](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)