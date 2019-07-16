---
title: Общие сведения о службе Azure Kubernetes
description: Узнайте о возможностях и преимуществах Службы Azure Kubernetes при развертывании и администрировании контейнерных приложений в Azure.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: overview
ms.date: 05/06/2019
ms.author: mlearned
ms.custom: mvc
ms.openlocfilehash: 5515951a9bde596935f4c603ffd9e088f74dee45
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67615161"
---
# <a name="azure-kubernetes-service-aks"></a>Служба Azure Kubernetes (AKS)

Служба Azure Kubernetes (AKS) упрощает развертывание управляемого кластера Kubernetes в Azure. AKS снижает сложность управления службой Kubernetes и связанные временные затраты, делегируя ответственность за большую их часть облаку Azure. Размещенная в Azure служба Kubernetes отвечает за критические задачи, в частности за мониторинг работоспособности и техническое обслуживание. Azure управляет мастером Kubernetes. Вы только управляете и обслуживаете узлы агентов. Как управляемая среда Kubernetes, AKS предоставляется бесплатно, оплачиваются только узлы агента в кластерах, а не мастера.

С помощью Azure CLI или шаблона на основе параметров развертывания на портале Azure можно создать кластер AKS, например шаблоны Resource Manager и Terraform. При развертывании кластера AKS мастер Kubernetes и все узлы развертываются и настраиваются под ваши потребности. Дополнительные компоненты, например дополнительные сети, интеграция Azure Active Directory и мониторинг, можно также настроить во время развертывания. Сейчас в AKS реализована предварительная версия поддержки контейнеров Windows Server.

Дополнительные сведения о Kubernetes см. в статье о [ключевых концепциях Kubernetes для службы Azure Kubernetes (AKS)][concepts-clusters-workloads].

Для начала работы изучите краткое руководство по развертыванию Azure Kubernetes [с помощью портала][aks-portal] или or [with the Azure CLI][aks-cli] (с помощью CLI).

## <a name="access-security-and-monitoring"></a>Доступ, безопасность и мониторинг

Для повышения безопасности и управления AKS позволяет интегрировать с Azure Active Directory и использовать управление доступом на основе ролей Kubernetes. Также можно отслеживать работоспособность кластера и ресурсов.

### <a name="identity-and-security-management"></a>Управление идентификаторами и безопасностью

Чтобы ограничить доступ к ресурсам кластера, служба AKS поддерживает [Управление доступом на основе ролей (RBAC)][kubernetes-rbac]. RBAC позволяет управлять доступом к ресурсам и пространствам имен Kubernetes, а также разрешениями для доступа к этим ресурсам. Кластер AKS также может быть настроен для интеграции с Azure Active Directory. В интеграции Azure Active Directory на основе идентификатора и группового членства можно настроить доступ к Kubernetes. Доступ к ресурсам AKS, а также интегрированное взаимодействие единого входа можно предоставить существующим пользователям и группам Azure Active Directory.

Дополнительные сведения об идентификаторе см. в статье о [возможностях контроля доступа и идентификации в AKS][concepts-identity].

Сведения о том, как обеспечить безопасность кластера AKS, см. в статье об [интеграции Azure Active Directory со службой Azure Kubernetes][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Встроенное ведение журналов и мониторинг

Чтобы понять, как работает ваш кластер и развернутые приложения AKS, Azure Monitor для работоспособности контейнеров собирает данные о памяти и процессорах из контейнеров, узлов и контроллеров. Доступны журналы контейнера и можно также [просмотреть журналы мастера Kubernetes][aks-master-logs]. Эти данные мониторинга хранятся в рабочей области Azure Log Analytics и доступны через портал Azure, Azure CLI или конечную точку REST.

Дополнительные сведения см. в статье о [мониторинге работоспособности службы Azure Kubernetes (AKS)][container-health].

## <a name="clusters-and-nodes"></a>Кластеры и узлы

Узлы AKS выполняются на виртуальных машинах Azure. Можно подключить хранилище к узлам и pod, обновить компоненты кластера и использовать GPU. AKS поддерживает кластеры Kubernetes, которые используют несколько пулов узлов для поддержки смешанных операционных систем и контейнеров Windows Server (в настоящее время доступна предварительная версия). Узлы Linux используют настроенный образ операционной системы Ubuntu, а узлы Windows Server — настроенный образ операционной системы Windows Server 2019.

### <a name="cluster-node-and-pod-scaling"></a>Масштабирование узла и pod кластера

Как спрос на изменение ресурсов, число узлов кластера или pod, которые выполняют службы, могут автоматически масштабироваться. Можно использовать автомасштабирование горизонтального pod или кластера. Этот подход к масштабированию позволяет кластеру AKS автоматически изменять требования и запускать только необходимые ресурсы.

Дополнительные сведения см. в статье о [масштабировании кластера службы Azure Kubernetes][aks-scale].

### <a name="cluster-node-upgrades"></a>Обновления узла кластера

Служба Kubernetes Azure предлагает несколько версий Kubernetes. После того, как новая версия станет доступной в AKS, с помощью средств портала Azure или Azure CLI кластер можно обновить. Чтобы свести к минимуму время простоя работающих приложений, в процессе обновления узлы тщательно блокируются и останавливаются.

Дополнительные сведения о версиях жизненного цикла см. в статье о [поддерживаемых версиях Kubernetes в AKS][aks-supported versions]. For steps on how to upgrade, see [Upgrade an Azure Kubernetes Service (AKS) cluster][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>Узлы с графическим процессором

Служба AKS поддерживает создание пулов узлов с процессорами GPU. В настоящее время Azure предоставляет одну или несколько виртуальных машин с поддержкой процессоров GPU. Виртуальные машины с поддержкой процессоров GPU предназначены для рабочих нагрузок с большим объемом вычислений, графической обработки и визуализаций.

Дополнительные сведения см. в статье об [использовании GPU в AKS][aks-gpu].

### <a name="storage-volume-support"></a>Поддержка тома хранилища

Для поддержки рабочих нагрузок приложений можно вставить тома хранилища для постоянных данных. Можно использовать статические и динамические тома. В зависимости от того, сколько подключенных pod предназначено для совместного использования хранилища, можно использовать любое хранилище, поддерживаемое либо дисками Azure для доступа к одному pod, либо файлами Azure для одновременного доступа к нескольким.

Дополнительные сведения см. в статье о [возможности хранения данных приложений в AKS][concepts-storage].

Для начала работы с динамическими постоянными томами см. статьи о [дисках Azure в AKS][azure-disk]и о or [Azure Files][azure-files]файлах Azure в AKS.

## <a name="virtual-networks-and-ingress"></a>Виртуальные сети и входные данные

Кластер AKS можно развернуть в существующей виртуальной сети. В этой конфигурации каждому контейнеру pod в кластере назначается IP-адрес в виртуальной сети. Они могут напрямую взаимодействовать с другими контейнерами pod в кластере, а также другими узлами в виртуальной сети. Также группа контейнеров pod может подключаться к другим службам одноранговой виртуальной сети, а также к локальным сетям через подключения ExpressRoute и VPN типа "сеть — сеть" (S2S).

Дополнительные сведения см. в статье об [основных сетевых понятиях для приложений в AKS][aks-networking].

Чтобы начать работу с входящим трафиком, см. статью о [маршрутизации приложений HTTP][aks-http-routing].

### <a name="ingress-with-http-application-routing"></a>Входящий трафик маршрутизации приложений HTTP

Дополнительный компонент маршрутизации приложений HTTP упрощает доступ к приложениям, развернутым в кластере Службы контейнеров Azure (AKS). При установке этого параметра решение маршрутизации приложений HTTP настраивает контроллер входящего трафика в кластере AKS. После того, как приложения были развернуты, общедоступные DNS-имена будут настроены автоматически. Маршрутизация HTTP-приложений настраивает зону DNS и интегрирует ее с кластером AKS. Затем можно развернуть ресурсы входящего трафика Kubernetes в обычном режиме.

Чтобы начать работу с входящим трафиком, см. статью о [маршрутизации приложений HTTP][aks-http-routing].

## <a name="development-tooling-integration"></a>Интеграция средств разработки

Kubernetes обладает обширной экосистемой средств разработки и управления. К ним можно отнести Helm, Draft а также расширение Kubernetes для Visual Studio Code Эти средства совместимы с AKS.

Кроме того, рабочие среды Azure Dev Spaces предоставляют быстрый итеративный интерфейс разработки Kubernetes для команд. Для того чтобы запускать и отлаживать контейнеры непосредственно в службе Azure Kubernetes (AKS), требуется минимальная конфигурация. Чтобы начать работу, см. статью об [Azure Dev Spaces][azure-dev-spaces].

Проект Azure DevOps обеспечивает простое решение, которое позволяет использовать существующий код и репозиторий Git в Azure. Проект DevOps автоматически создает такие ресурсы Azure, как AKS, конвейер выпуска в Azure DevOps Services, который содержит конвейер сборки для CI, настраивает конвейер выпуска CD и затем создает ресурс Azure Application Insights для мониторинга.

Дополнительные сведения см. в статье о [проекте Azure DevOps][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Поддержка образов Docker и частный реестр контейнеров

Служба Azure Kubernetes поддерживает формат образов Docker. Для частного хранилища образов Docker можно интегрировать AKS с Реестром контейнеров Azure (ACR).

Сведения о том, как создать хранилище частных образов, см. в статье о [Реестре контейнеров Azure][acr-docs].

## <a name="kubernetes-certification"></a>Сертификация Kubernetes

Служба Azure Kubernetes (AKS) была сертифицирована Cloud Native Computing Foundation (CNCF) в качестве согласованной с Kubernetes.

## <a name="regulatory-compliance"></a>Соблюдение нормативных требований

Azure Kubernetes Service (AKS) соответствуют требованиям нормативных документов SOC, ISO, PCI DSS, и HIPAA. Дополнительные сведения см. в документе о [предложениях Azure для соответствия требованиям][compliance-doc].

## <a name="next-steps"></a>Дополнительная информация

Узнайте больше о развертывании и управлении AKS из краткого руководства по Azure CLI.

> [!div class="nextstepaction"]
> [Краткое руководство по развертыванию кластера службы Azure Kubernetes (AKS)][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/azure/devops-project/overview
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md
[aks-master-logs]: view-master-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
