---
title: Введение в Azure Cosmos DB API etcd
description: В этой статье приводятся общие сведения и основные преимущества API etcd в Azure Cosmos DB
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 6c7fcb1429438ee024cb226b63cfcdcab05ed9f8
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79498593"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Введение в Azure Cosmos DB API etcd (Предварительная версия)

Azure Cosmos DB — это глобально распределенная, многомодельная служба базы данных Майкрософт, необходимая для работы с критически важными приложениями. Он обеспечивает надежное глобальное распределение, Эластичное масштабирование пропускной способности и хранения, однозначные задержки в миллисекундах на 99-м процентиль и гарантированный высокий уровень доступности, который обеспечивается в соответствии с ведущими в отрасли соглашениями об уровне обслуживания.

[Etcd](https://github.com/etcd-io/etcd) — это распределенное хранилище "ключ — значение". В [Kubernetes](https://kubernetes.io/)etcd используется для хранения состояния и конфигурации кластеров Kubernetes. Обеспечение доступности, надежности и производительности etcd имеет решающее значение для общей работоспособности кластера, масштабируемости, доступности эластичности и производительности кластера Kubernetes. 

API etcd в Azure Cosmos DB позволяет использовать Azure Cosmos DB в качестве серверного хранилища для [Azure Kubernetes](../aks/index.yml). API etcd в Azure Cosmos DB в настоящее время находится в режиме предварительной версии. Azure Cosmos DB реализует протокол проводной сети etcd. С помощью API etcd в Azure Cosmos DB разработчики автоматически получают высокую надежность, [доступную](high-availability.md) [глобально распределенную](distribute-data-globally.md) Kubernetes. Этот API позволяет разработчикам масштабировать управление состоянием Kubernetes в полностью управляемой облачной службе PaaS. 

> [!NOTE]
> В отличие от других API-интерфейсов в Azure Cosmos DB вы не можете подготавливать учетную запись API etcd с помощью портал Azure, CLI или пакетов SDK. Учетную запись API etcd можно подготавливать, развертывая только шаблон диспетчер ресурсов. подробные инструкции см. [в статье о подготовке Azure Kubernetes с Azure Cosmos DB](bootstrap-kubernetes-cluster.md) . Azure Cosmos DB API etcd в настоящее время ограничен предварительной версией. Вы можете [зарегистрироваться для просмотра](https://aka.ms/cosmosetcdapi-signup), заполнив форму регистрации.

## <a name="wire-level-compatibility"></a>Совместимость на уровне сети

Azure Cosmos DB реализует протокол передачи etcd версии 3 и позволяет серверам API [главного узла](https://kubernetes.io/docs/concepts/overview/components/) использовать Azure Cosmos DB так же, как и в локально установленной среде etcd. API etcd поддерживает взаимную проверку подлинности TLS. 

На следующей диаграмме показаны компоненты кластера Kubernetes. В базе данных master кластера сервер API использует Azure Cosmos DB API etcd, а не локально установленные etcd. 

![Azure Cosmos DB реализации кабельного протокола etcd](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Основные преимущества

### <a name="no-etcd-operations-management"></a>Без управления операциями etcd

В качестве полностью управляемой собственной облачной службы Azure Cosmos DB устраняет необходимость в установке и управлении etcd разработчиками Kubernetes. API etcd в Azure Cosmos DB является масштабируемым, высокодоступным, отказоустойчивым и обеспечивает высокую производительность. Накладные расходы на настройку репликации на нескольких узлах, выполнение последовательного обновления, обновлений безопасности и мониторинга работоспособности etcd обрабатываются Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Глобальное распределение & высокий уровень доступности 

Используя API etcd, Azure Cosmos DB гарантирует доступность 99,99% для операций чтения и записи данных в одном регионе и 99,999% доступности в нескольких регионах. 

### <a name="elastic-scalability"></a>Эластичное масштабирование

Azure Cosmos DB обеспечивает масштабируемость для запросов чтения и записи в разных регионах.
По мере роста кластера Kubernetes учетная запись API etcd в Azure Cosmos DB гибко масштабируется без простоев. Хранение данных etcd в Azure Cosmos DB вместо главных узлов Kubernetes также обеспечивает более гибкое масштабирование главного узла. 

### <a name="security--enterprise-readiness"></a>Безопасность & Корпоративная готовность

Когда данные etcd хранятся в Azure Cosmos DB, разработчики Kubernetes автоматически получают [встроенное шифрование при](database-encryption-at-rest.md)хранении, [сертификации и соответствии](compliance.md), а также [возможности резервного копирования и восстановления](online-backup-and-restore.md) , поддерживаемые Azure Cosmos DB. 

## <a name="next-steps"></a>Дальнейшие шаги

* [Использование Azure Kubernetes с Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Основные преимущества Azure Cosmos DB](introduction.md)
* [Краткое руководство по AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)