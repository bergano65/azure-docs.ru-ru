---
title: Знакомство с API etcd для Azure Cosmos DB
description: В этой статье приводятся общие сведения об API etcd в Azure Cosmos DB и его основные преимущества
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2020
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 16aac5c765c36c49919685ee58e8034786ddf1ae
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2020
ms.locfileid: "83797359"
---
# <a name="introduction-to-the-azure-cosmos-db-etcd-api-preview"></a>Знакомство с API etcd для Azure Cosmos DBI (предварительная версия)

Azure Cosmos DB — это глобально распределенная, многомодельная служба базы данных Майкрософт, необходимая для работы с критически важными приложениями. Она обеспечивает готовое к использованию глобальное распределение, гибкое масштабирование пропускной способности и хранилища, задержки менее 10 миллисекунд на уровне 99-го процентиля, а также гарантированную высокую доступность — все это согласно ведущим в отрасли соглашениям об уровне обслуживания.

[Etcd](https://github.com/etcd-io/etcd) — это распределенное хранилище "ключ — значение". В [Kubernetes](https://kubernetes.io/) etcd используется для хранения состояния и конфигурации кластеров Kubernetes. Обеспечение доступности, надежности и производительности etcd имеет решающее значение для общей работоспособности кластера, масштабируемости, доступности эластичности и производительности кластера Kubernetes.

API etcd в Azure Cosmos DB позволяет использовать Azure Cosmos DB в качестве хранилища баз данных для Azure Kubernetes. API etcd в Azure Cosmos DB в настоящее время находится в режиме предварительной версии. Azure Cosmos DB реализует протокол проводной сети etcd. С помощью API etcd в Azure Cosmos DB разработчики автоматически получают высоконадежные [доступные](high-availability.md), [глобально распределенные](distribute-data-globally.md) услуги Kubernetes. Этот API позволяет разработчикам масштабировать управление состоянием Kubernetes в полностью управляемой облачной службе PaaS. 

> [!NOTE]
> В отличие от других API-интерфейсов в Azure Cosmos DB, вы не можете подготавливать учетные записи API etcd с помощью портала Azure, CLI или пакетов SDK. Учетную запись API etcd можно подготовить только развернув шаблон диспетчера ресурсов. Подробные инструкции см. в статье [Подготовка к работе Azure Kubernetes с Azure Cosmos DB](bootstrap-kubernetes-cluster.md). API etcd для Azure Cosmos DB в настоящее время предоставляется в режиме ограниченной предварительной версии. Вы можете [зарегистрироваться на предварительную версию](https://aka.ms/cosmosetcdapi-signup), заполнив форму регистрации.

## <a name="wire-level-compatibility"></a>Совместимость на уровне сети

Azure Cosmos DB реализует протокол проводной сети etcd версии 3, который позволяет серверам API [главного узла](https://kubernetes.io/docs/concepts/overview/components/) использовать Azure Cosmos DB таким же образом, как это происходило бы в локально установленной среде etcd. API etcd поддерживает взаимную проверку подлинности TLS. 

На следующей схеме показаны компоненты кластера Kubernetes. На основном узле кластера сервер API использует API etcd Azure Cosmos DB, а не локально установленный etcd. 

![Реализация протокола проводной сети etcd в Azure Cosmos DB](./media/etcd-api-introduction/etcd-api-wire-protocol.png)

## <a name="key-benefits"></a>Основные преимущества

### <a name="no-etcd-operations-management"></a>Нет управления операциями etcd

В качестве полностью управляемой собственной облачной службы Azure Cosmos DB избавляет разработчиков Kubernetes от необходимости в установке etcd и управлении им. API etcd в Azure Cosmos DB является масштабируемым, высокодоступным, отказоустойчивым и обеспечивает высокую производительность. Затраты ресурсов на настройку репликации на нескольких узлах, выполнение последовательных обновлений, обновления безопасности и мониторинг работоспособности etcd берет на себя Azure Cosmos DB.

### <a name="global-distribution--high-availability"></a>Глобальное распределение и высокий уровень доступности 

Используя API etcd, Azure Cosmos DB гарантирует доступность на протяжении 99,99 % времени для операций чтения и записи данных в одном регионе и 99,999 % времени при распределении по нескольким регионам. 

### <a name="elastic-scalability"></a>Эластичное масштабирование

Azure Cosmos DB обеспечивает гибкую масштабируемость для запросов чтения и записи в разных регионах.
По мере роста кластера Kubernetes учетная запись API etcd в Azure Cosmos DB гибко масштабируется, безо всяких простоев. Хранение данных etcd в Azure Cosmos DB вместо главных узлов Kubernetes также обеспечивает более гибкое масштабирование главного узла. 

### <a name="security--enterprise-readiness"></a>Готовность к работе в масштабах предприятия и безопасность

Когда данные etcd хранятся в Azure Cosmos DB, разработчики Kubernetes автоматически получают [встроенные средства шифрования неактивных данных](database-encryption-at-rest.md), [сертификацию и соответствие требованиям](compliance.md), а также [возможности резервного копирования и восстановления](online-backup-and-restore.md), поддерживаемые Azure Cosmos DB. 

## <a name="next-steps"></a>Дальнейшие действия

* [Использование Azure Kubernetes с Azure Cosmos DB](bootstrap-kubernetes-cluster.md)
* [Ключевые преимущества Azure Cosmos DB](introduction.md)
* [Краткое руководство по AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/quickstart.md)