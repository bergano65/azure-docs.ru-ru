---
title: Подключение Службы Azure Kubernetes (AKS) к Базе данных Azure для PostgreSQL
description: Сведения о подключении Службы Azure Kubernetes к Базе данных Azure для PostgreSQL
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.date: 11/27/2018
ms.topic: article
ms.openlocfilehash: 770a583ad09ec1aaa8191f5dafb3db61b2386e68
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53166240"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql"></a>Подключение Службы Azure Kubernetes к Базе данных Azure для PostgreSQL

Служба Azure Kubernetes (AKS) предоставляет управляемый кластер Kubernetes для использования в Azure. Ниже приведены некоторые настройки, которые можно использовать при создании приложения с помощью AKS и Базы данных Azure для PostgreSQL.


## <a name="accelerated-networking"></a>Ускорение работы в сети
Используйте в кластере AKS базовые виртуальные машины с поддержкой ускоренной работы в сети. Если на виртуальной машине включена ускоренная работа в сети, на машине наблюдается снижение задержки, дрожания и загрузки ЦП. Дополнительные сведения об ускоренной работе в сети, поддерживаемых версиях ОС и экземплярах виртуальных машин Linux см. в статье [Создание виртуальной машины Linux с ускоренной сетью](../virtual-network/create-vm-accelerated-networking-cli.md).

С ноября 2018 года AKS поддерживает ускоренные сети на поддерживаемых экземплярах виртуальных машин. Для новых кластеров AKS, которые используют эти виртуальные машины, ускоренная работа в сети включена по умолчанию.

Чтобы проверить, использует ли ваш кластер AKS ускоренную сеть, сделайте следующее:
1. Перейдите на портал Azure и выберите свой кластер AKS.
2. Выберите вкладку "Свойства".
3. Скопируйте имя **группы ресурсов инфраструктуры**.
4. С помощью панели поиска на портале найдите и откройте группу ресурсов инфраструктуры.
5. Выберите в этой группе ресурсов виртуальную машину.
6. Перейдите на вкладку **Сети** для виртуальной машины.
7. Проверьте, включена ли **ускоренная сеть**.


## <a name="open-service-broker-for-azure"></a>Открытый компонент Service Broker для Azure 
[Open Service Broker для Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) позволяет подготавливать службы Azure непосредственно из Kubernetes или Cloud Foundry. Он предоставляется в реализации [API Open Service Broker](https://www.openservicebrokerapi.org/) для Azure.

С помощью OSBA вы можете создать Базу данных Azure для PostgreSQL и связать ее с кластером AKS, используя машинный язык Kubernetes. Сведения об использовании OSBA с Базой данных Azure для PostgreSQL см. на [сайте GitHub](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/postgresql.md). 


## <a name="connection-pooling"></a>Пул подключений
Пул подключений позволяет свести к минимуму затраты и время, связанные с созданием и закрытием новых подключений к базе данных. Пул — это коллекция подключений, которые можно использовать повторно. 

С PostgreSQL можно использовать несколько пулов подключений. Один из них — [PgBouncer](https://pgbouncer.github.io/). В Реестре контейнеров Майкрософт доступен упрощенный контейнерный пул PgBouncer, который можно использовать в расширении для объединения подключений AKS к Базе данных Azure для PostgreSQL. Перейдите на [страницу центра Docker](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/), чтобы просмотреть сведения о получении доступа к этому образу и его использовании. 


## <a name="next-steps"></a>Дополнительная информация
-  [Краткое руководство по развертыванию кластера Службы Azure Kubernetes (AKS)](../aks/kubernetes-walkthrough.md)
