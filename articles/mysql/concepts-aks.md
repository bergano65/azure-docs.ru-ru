---
title: Подключение Службы Azure Kubernetes (AKS) к Базе данных Azure для MySQL
description: Сведения о подключении Службы Azure Kubernetes к Базе данных Azure для MySQL
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 11/28/2018
ms.openlocfilehash: baba85aeb800dd8effe3be295b2149179604b41d
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164846"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-mysql"></a>Подключение Службы Azure Kubernetes к Базе данных Azure для MySQL

Служба Azure Kubernetes (AKS) предоставляет управляемый кластер Kubernetes для использования в Azure. Ниже приведены некоторые настройки, которые можно использовать при создании приложения с помощью AKS и Базы данных Azure для MySQL.


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
[Open Service Broker для Azure](https://github.com/Azure/open-service-broker-azure/blob/master/README.md) (OSBA) позволяет подготавливать службы Azure непосредственно из Kubernetes или Cloud Foundry. Он представляет собой реализацию [API Open Service Broker](https://www.openservicebrokerapi.org/) для Azure.

С помощью OSBA вы можете создать Базу данных Azure для MySQL и связать ее с кластером AKS, используя машинный язык Kubernetes. Сведения об использовании OSBA с Базой данных Azure для MySQL см. на [сайте GitHub](https://github.com/Azure/open-service-broker-azure/blob/master/docs/modules/mysql.md). 



## <a name="next-steps"></a>Дополнительная информация
- [Краткое руководство по развертыванию кластера Службы Azure Kubernetes (AKS)](../aks/kubernetes-walkthrough.md)
- Узнайте, как [установить WordPress из чарта Helm с помощью OSBA и Базы данных Azure для MySQL](../aks/integrate-azure.md).
