---
title: Развертывание Kubernetes в Azure Stack | Документация Майкрософт
description: Узнайте, как развернуть Kubernetes в Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 01/16/2019
ms.openlocfilehash: 9d542f7c2cdeb20806a2db98005ffc03cf72144c
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55238022"
---
# <a name="deploy-kubernetes-to-azure-stack"></a>Развертывание Kubernetes в Azure Stack

*Область применения: интегрированные системы Azure Stack и Пакет средств разработки Azure Stack*

> [!Note]  
> Система Kubernetes доступна в Azure Stack в предварительной версии.

Вы можете следовать инструкциям, описанным в этой статье, чтобы развернуть и настроить ресурсы для Kubernetes за одну согласованную операцию. В этих инструкциях используется шаблон решения Azure Resource Manager. Вам потребуется собрать все необходимые сведения об установке Azure Stack, создать шаблон и выполнить развертывание в облаке. Шаблон Azure Stack не использует управляемую службу AKS, представляемую в глобальной среде Azure.

## <a name="kubernetes-and-containers"></a>Kubernetes и контейнеры

Вы можете установить Kubernetes с помощью шаблонов Azure Resource Manager, созданных модулем ACS в Azure Stack. [Kubernetes](https://kubernetes.io) — это система с открытым кодом для автоматизации развертывания, масштабирования и управления приложениями в контейнерах. [Контейнер](https://www.docker.com/what-container) — это образ. Образ контейнера схож с виртуальной машиной, но в отличие от нее в контейнер помещены ресурсы, необходимые только для запуска приложения, например код, среда выполнения для этого кода, определенные библиотеки и параметры.

Kubernetes можно использовать для следующих целей:

- Разработка обновляемых приложений с высокой масштабируемостью, которые можно развернуть в считаные секунды. 
- Упрощение структуры приложения и повышение его надежности с помощью различных приложений Helm. [Helm](https://github.com/kubernetes/helm) — это средство упаковки с открытым исходным кодом, которое помогает установить приложения Kubernetes и управлять их жизненным циклом.
- Простой мониторинг и диагностика работоспособности приложений с помощью функций масштабирования и обновления.

Вам будут выставляться счета только за использование вычислительных ресурсов, необходимых для узлов, поддерживающих кластер. Дополнительные сведения см. в статье [Потребление ресурсов и выставление счетов в Azure Stack](https://docs.microsoft.com/azure/azure-stack/azure-stack-billing-and-chargeback).

## <a name="deploy-kubernetes"></a>Развертывание Kubernetes

Инструкции по развертыванию кластера Kubernetes в Azure Stack будут зависеть от вашей службы управления удостоверениями. Проверьте решение по управлению удостоверениями, используемое при установке Azure Stack. Обратитесь к администратору Azure Stack, чтобы проверить службу управления удостоверениями.

- **Azure Active Directory (Azure AD).**  
Инструкции по установке кластера с использованием Azure AD см. в статье [Deploy Kubernetes to Azure Stack using Azure Active Directory](azure-stack-solution-template-kubernetes-azuread.md) (Развертывание Kubernetes в Azure Stack с помощью Azure Active Directory).

- **Службы федерации Active Directory (AD FS)**  
Инструкции по установке кластера с использованием AD FS см. в статье [Развертывание Kubernetes в Azure Stack с помощью служб федерации Active Directory](azure-stack-solution-template-kubernetes-adfs.md).

## <a name="connect-to-your-cluster"></a>Подключение к кластеру

Теперь все готово для подключения к кластеру. Главный кластер можно найти в группе кластерных ресурсов. Он называется `k8s-master-<sequence-of-numbers>`. Подключитесь к главному кластеру, используя клиент SSH. Управлять кластером можно с помощью **kubectl**, клиента командной строки Kubernetes. Инструкции см. на сайте [Kubernetes.io](https://kubernetes.io/docs/reference/kubectl/overview).

Можно также использовать диспетчер пакетов **Helm** для установки и развертывания приложений в кластер. Инструкции по установке и использованию Helm с кластером см. на сайте [helm.sh](https://helm.sh/).

## <a name="next-steps"></a>Дополнительная информация

[Добавление Kubernetes в Marketplace (для оператора Azure Stack)](../azure-stack-solution-template-kubernetes-cluster-add.md)

[Deploy Kubernetes to Azure Stack using Azure Active Directory](azure-stack-solution-template-kubernetes-azuread.md) (Развертывание Kubernetes в Azure Stack с помощью Azure Active Directory)

[Развертывание Kubernetes в Azure Stack с помощью служб федерации Active Directory](azure-stack-solution-template-kubernetes-adfs.md)

[Kubernetes в Azure](https://docs.microsoft.com/azure/container-service/kubernetes/container-service-kubernetes-walkthrough)
