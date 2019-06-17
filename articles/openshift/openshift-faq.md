---
title: Часто задаваемые вопросы об Azure Red Hat OpenShift | Документация Майкрософт
description: Ниже приведены ответы на часто задаваемые вопросы о Microsoft Azure Red Hat OpenShift
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/08/2019
ms.openlocfilehash: 32eb2c47ed46aed8e2e3755a83437a21391295c5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122961"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift часто задаваемые вопросы

В этой статье рассматриваются часто задаваемые вопросы (FAQ) о Microsoft Azure Red Hat OpenShift.

## <a name="how-do-i-get-started"></a>Как начать работу?

Перед тем как использовать Azure Red Hat OpenShift, вам потребуется приобрести не менее 4 узлов приложения защищены Azure Red Hat OpenShift.

Если вы являетесь клиентом Azure,[приобрести Azure Red Hat OpenShift зарезервированные экземпляры](https://aka.ms/openshift/buy) через портал Azure. После приобретения, подписки будут активированы в течение 24 часов, после чего вы сможете Подготовка кластеров.

Если вы не являетесь клиентом Azure [связаться с отделом продаж](https://aka.ms/openshift/contact-sales) и заполните форму продаж в нижней части страницы, чтобы начать процесс.

Ссылаться на [странице с ценами Azure Red Hat OpenShift](https://aka.ms/openshift/pricing) Дополнительные сведения.

## <a name="which-azure-regions-are-supported"></a>В каких регионах Azure поддерживаются?

См. в разделе [поддерживаемые ресурсы](supported-resources.md#azure-regions) список глобальных регионах, где поддерживается Azure Red Hat OpenShift.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Можно ли развернуть кластер в существующей виртуальной сети?

№ Но кластер Azure Red Hat OpenShift можно подключиться к существующей виртуальной сети с помощью пиринга. См. в разделе [подключить виртуальную сеть кластера к существующей виртуальной сети ](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) подробные сведения.

## <a name="what-cluster-operations-are-available"></a>Какие операции кластера доступны?

Можно масштабировать только увеличивать или уменьшать количество вычислительных узлов. Другие изменения не разрешены для `Microsoft.ContainerService/openShiftManagedClusters` ресурсов после ее создания. Максимальное количество вычислительных узлов ограничено 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Какие размеры виртуальных машин можно использовать?

См. в разделе [размеры виртуальных машин Azure Red Hat OpenShift](supported-resources.md#virtual-machine-sizes) список размеров виртуальных машин, можно использовать с кластером Azure Red Hat OpenShift.

## <a name="is-data-on-my-cluster-encrypted"></a>Включен кластер зашифрованные данные?

По умолчанию есть шифрование в неактивном состоянии. Платформа службы хранилища Azure автоматически шифрует данные перед их сохранением и расшифровывает данные перед извлечением. См. в разделе [шифрование службы хранилища Azure для неактивных данных](https://docs.microsoft.com/azure/storage/common/storage-service-encryption) подробные сведения.

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>Можно ли использовать Prometheus или Grafana для мониторинга моих приложений?

Да, можно развернуть Prometheus в приложения пространство имен и монитор в пространстве имен.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Можно ли использовать Prometheus или Grafana для мониторинга метрики, связанные с работоспособности кластера и емкость?

Нет, не в настоящий момент.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Доступен в реестр Docker извне, поэтому я могу использовать средства, такие как Jenkins?

Реестр Docker доступен из `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` тем не менее, не предоставляется гарантией строгого хранилища. Можно также использовать [реестр контейнеров Azure](https://azure.microsoft.com/services/container-registry/).

## <a name="is-cross-namespace-networking-supported"></a>Поддерживается ли кросс namespace сети?

Клиентов и администраторов отдельного проекта можно настроить сети кросс-пространство имен (включая запрещается) для каждого проекта с помощью `NetworkPolicy` объектов.

## <a name="can-an-admin-manage-users-and-quotas"></a>Администратор Управление пользователями и квоты?

Да. Может управлять администратор Azure Red Hat OpenShift, пользователей и квоты в дополнение к доступу всех пользователей, которые создали проекты.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Можно ограничить кластер только определенным пользователям Azure AD?

Да. Вы можете ограничить какие Azure AD пользователи могут входить в кластер путем настройки приложения Azure AD. Дополнительные сведения см. в разделе [как: Ограничить набор пользователей приложения](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Кластер может иметь вычислительных узлов в нескольких регионах Azure?

№ Все узлы в кластере Azure Red Hat OpenShift должны создаваться в том же регионе Azure.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Узлы master и инфраструктуры позволяют абстрагироваться неизменными со службой Azure Kubernetes (AKS)?

№ Все ресурсы, включая главного кластера, запустите в подписке клиента. Эти типы ресурсов, помещаются в группу ресурсов только для чтения.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>— Открытый компонент Service Broker для Azure (OSBA) поддерживается?

Да. OSBA можно использовать с Azure Red Hat OpenShift. См. в разделе [открытый компонент Service Broker для Azure](https://github.com/Azure/open-service-broker-azure#openshift-project-template) Дополнительные сведения.

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Пытается установить пиринг в виртуальной сети в другую подписку, но получение `Failed to get vnet CIDR` ошибки.

В подписке, которая содержит виртуальную сеть, убедитесь, что регистрация `Microsoft.ContainerService` поставщика с `az provider register -n Microsoft.ContainerService --wait` 
