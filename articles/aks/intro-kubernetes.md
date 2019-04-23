---
title: Общие сведения о службе Azure Kubernetes
description: Узнайте о возможностях и преимуществах Службы Azure Kubernetes при развертывании и администрировании контейнерных приложений в Azure.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: overview
ms.date: 03/05/2019
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a147d95701af166c650411a91fb24e3448cfec09
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995036"
---
# <a name="azure-kubernetes-service-aks"></a>Служба Azure Kubernetes (AKS)

Служба Azure Kubernetes (AKS) упрощает развертывание управляемого кластера Kubernetes в Azure. AKS снижает сложность управления службой Kubernetes и связанные временные затраты, делегируя ответственность за большую их часть облаку Azure. Размещенная в Azure служба Kubernetes отвечает за критические задачи, в частности за мониторинг работоспособности и техническое обслуживание. Azure управляет мастером Kubernetes. Вы только управляете и обслуживаете узлы агентов. Как управляемая среда Kubernetes, AKS предоставляется бесплатно, оплачиваются только узлы агента в кластерах, а не мастера.

С помощью Azure CLI или шаблона на основе параметров развертывания на портале Azure можно создать кластер AKS, например шаблоны Resource Manager и Terraform. При развертывании кластера AKS мастер Kubernetes и все узлы развертываются и настраиваются под ваши потребности. Дополнительные компоненты, например дополнительные сети, интеграция Azure Active Directory и мониторинг, можно также настроить во время развертывания.

Дополнительные сведения о Kubernetes см. в статье о [ключевых концепциях Kubernetes для службы Azure Kubernetes (AKS)][concepts-clusters-workloads].

Прежде чем начать работу, изучите [Краткое руководство по развертыванию кластера службы Azure Kubernetes][aks-portal] (с помощью портала) или [Краткое руководство по развертыванию кластера службы Azure Kubernetes][aks-cli] (с помощью CLI).

## <a name="access-security-and-monitoring"></a>Доступ, безопасность и мониторинг

Для повышения безопасности и управления AKS позволяет интегрировать с Azure Active Directory и использовать управление доступом на основе ролей Kubernetes. Также можно отслеживать работоспособность кластера и ресурсов.

### <a name="identity-and-security-management"></a>Управление идентификаторами и безопасностью

Чтобы ограничить доступ к ресурсам кластера, служба AKS поддерживает [Управление доступом на основе ролей (RBAC)][kubernetes-rbac]. RBAC позволяет управлять доступом к ресурсам и пространствам имен Kubernetes, а также разрешениями для доступа к этим ресурсам. Кластер AKS также может быть настроен для интеграции с Azure Active Directory. В интеграции Azure Active Directory на основе идентификатора и группового членства можно настроить доступ к Kubernetes. Доступ к ресурсам AKS, а также интегрированное взаимодействие единого входа можно предоставить существующим пользователям и группам Azure Active Directory.

Дополнительные сведения об идентификаторе см. в статье о [возможностях контроля доступа и идентификации в AKS][concepts-identity].

Чтобы защитить кластер AKS, см. раздел [Интеграция Azure Active Directory со службой Azure Kubernetes][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Встроенное ведение журналов и мониторинг

Чтобы понять, как работает ваш кластер и развернутые приложения AKS, Azure Monitor для работоспособности контейнеров собирает данные о памяти и процессорах из контейнеров, узлов и контроллеров. Доступны журналы контейнера, и также можно [просмотреть журналы мастера Kubernetes][aks-master-logs]. Эти данные мониторинга хранятся в рабочей области Azure Log Analytics и доступны через портал Azure, Azure CLI или конечную точку REST.

Дополнительные сведения см. в разделе [Мониторинг работоспособности службы Azure Kubernetes (AKS) (предварительная версия)][container-health].

## <a name="cluster-and-node"></a>Кластер и узел

Узлы AKS выполняются на виртуальных машинах Azure. Можно подключить хранилище к узлам и pod, обновить компоненты кластера и использовать GPU.

### <a name="cluster-node-and-pod-scaling"></a>Масштабирование узла и pod кластера

Как спрос на изменение ресурсов, число узлов кластера или pod, которые выполняют службы, могут автоматически масштабироваться. Можно использовать автомасштабирование горизонтального pod или кластера. Этот подход к масштабированию позволяет кластеру AKS автоматически изменять требования и запускать только необходимые ресурсы.

Дополнительные сведения см. в разделе [Масштабирование кластера службы Azure Kubernetes][aks-scale].

### <a name="cluster-node-upgrades"></a>Обновления узла кластера

Служба Kubernetes Azure предлагает несколько версий Kubernetes. После того, как новая версия станет доступной в AKS, с помощью средств портала Azure или Azure CLI кластер можно обновить. Чтобы свести к минимуму время простоя работающих приложений, в процессе обновления узлы тщательно блокируются и останавливаются.

Дополнительные сведения о версиях жизненного цикла см. в разделе [Поддерживаемые версии Kubernetes в AKS][aks-supported versions]. Сведения об обновлении см. в статье [Обновление кластера службы Azure Kubernetes (AKS)][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>Узлы с графическим процессором

Служба AKS поддерживает создание пулов узлов с процессорами GPU. В настоящее время Azure предоставляет одну или несколько виртуальных машин с поддержкой процессоров GPU. Виртуальные машины с поддержкой процессоров GPU предназначены для рабочих нагрузок с большим объемом вычислений, графической обработки и визуализаций.

Дополнительные сведения см. в статье [Использование GPU в AKS][aks-gpu].

### <a name="storage-volume-support"></a>Поддержка тома хранилища

Для поддержки рабочих нагрузок приложений можно вставить тома хранилища для постоянных данных. Можно использовать статические и динамические тома. В зависимости от того, сколько подключенных pod предназначено для совместного использования хранилища, можно использовать любое хранилище, поддерживаемое либо дисками Azure для доступа к одному pod, либо файлами Azure для одновременного доступа к нескольким.

Дополнительные сведения см. в статье о [возможности хранения данных приложений в AKS][concepts-storage].

Для начала работы с динамическими постоянными томами см. статьи о [дисках Azure в AKS][azure-disk] и о [файлах Azure в AKS][azure-files].

## <a name="virtual-networks-and-ingress"></a>Виртуальные сети и входные данные

Кластер AKS можно развернуть в существующей виртуальной сети. В этой конфигурации каждому контейнеру pod в кластере назначается IP-адрес в виртуальной сети. Они могут напрямую взаимодействовать с другими контейнерами pod в кластере, а также другими узлами в виртуальной сети. Также группа контейнеров pod может подключаться к другим службам одноранговой виртуальной сети, а также к локальным сетям через подключения ExpressRoute и VPN типа "сеть — сеть" (S2S).

Дополнительные сведения см. в статье об [основных сетевых понятиях для приложений в AKS][aks-networking].

Чтобы начать работу с входящим трафиком, см. раздел [Маршрутизация приложений HTTP][aks-http-routing].

### <a name="ingress-with-http-application-routing"></a>Входящий трафик маршрутизации приложений HTTP

Дополнительный компонент маршрутизации приложений HTTP упрощает доступ к приложениям, развернутым в кластере Службы контейнеров Azure (AKS). При установке этого параметра решение маршрутизации приложений HTTP настраивает контроллер входящего трафика в кластере AKS. После того, как приложения были развернуты, общедоступные DNS-имена будут настроены автоматически. Маршрутизация HTTP-приложений настраивает зону DNS и интегрирует ее с кластером AKS. Затем можно развернуть ресурсы входящего трафика Kubernetes в обычном режиме.

Чтобы начать работу с входящим трафиком, см. раздел [Маршрутизация приложений HTTP][aks-http-routing].

## <a name="development-tooling-integration"></a>Интеграция средств разработки

Kubernetes обладает обширной экосистемой средств разработки и управления. К ним можно отнести Helm, Draft а также расширение Kubernetes для Visual Studio Code Эти средства совместимы с AKS.

Кроме того, рабочие среды Azure Dev Spaces предоставляют быстрый итеративный интерфейс разработки Kubernetes для команд. Для того чтобы запускать и отлаживать контейнеры непосредственно в службе Azure Kubernetes (AKS), требуется минимальная конфигурация. Чтобы начать работу, см. раздел [Рабочие среды Azure для разработчиков][azure-dev-spaces].

Проект Azure DevOps обеспечивает простое решение, которое позволяет использовать существующий код и репозиторий Git в Azure. Проект DevOps автоматически создает такие ресурсы Azure, как AKS, конвейер выпуска в Azure DevOps Services, который содержит конвейер сборки для CI, настраивает конвейер выпуска CD и затем создает ресурс Azure Application Insights для мониторинга.

Дополнительные сведения см. [Tutorial: Deploy your ASP.NET Core App to Azure Kubernetes Service (AKS) with the Azure DevOps Project][azure-devops] (Руководство. Разверните ASP.NET Core App в службе Azure Kubernetes (AKS) с помощью проекта Azure DevOps.).

## <a name="docker-image-support-and-private-container-registry"></a>Поддержка образов Docker и частный реестр контейнеров

Служба Azure Kubernetes поддерживает формат образов Docker. Для частного хранилища образов Docker можно интегрировать AKS с Реестром контейнеров Azure (ACR).

Как создать хранилище частных образов, см. в разделе [Общие сведения о частных Реестрах контейнеров Docker в Azure][acr-docs].

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
