---
title: Вопросы и ответы о Службе Azure Kubernetes (AKS)
description: Ответы на некоторые распространенные вопросы о службе Azure Kubernetes (AKS).
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/08/2019
ms.author: mlearned
ms.openlocfilehash: 495f182ed450d0fac69b31ea2996bacc60863fea
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67672777"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Вопросы и ответы о Службе Azure Kubernetes (AKS)

В этой статье представлены ответы на часто задаваемые вопросы о Службе Azure Kubernetes.

## <a name="which-azure-regions-currently-provide-aks"></a>В каких регионах Azure в настоящее время предоставляет AKS?

Полный список доступных регионов см. в разделе [AKS регионы и доступность][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>Поддерживает ли AKS автомасштабирование узла?

Да, возможность автоматически горизонтальное масштабирование узлов агентов в AKS в настоящее время доступна в предварительной версии. См. в разделе [автоматически масштабировать кластер в соответствии с требованиями приложения в AKS][aks-cluster-autoscaler] for instructions. AKS autoscaling is based on the [Kubernetes autoscaler][auto-scaler].

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Можно ли развернуть AKS в имеющейся виртуальной сети?

Да, можно развернуть кластер AKS в имеющейся виртуальной сети с помощью [дополнительные сетевые возможности][aks-advanced-networking].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Можно ли ограничить, у кого есть доступ к серверу Kubernetes API?

Да, вы можете ограничить доступ к серверу Kubernetes API с помощью [API сервера авторизации диапазоны IP-адресов][api-server-authorized-ip-ranges], которая доступна в предварительной версии.

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Можно ли сделать сервера Kubernetes API доступны только в пределах виртуальной сети?

В настоящее время нет, но это запланирована. Вы можете отслеживать ход выполнения [репозиторий AKS GitHub][private-clusters-github-issue].

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Может иметь разные размеры виртуальной Машины в одном кластере?

Да, вы можете использовать различных размеров виртуальных машин в кластере AKS, создав [несколько пулов узлов][multi-node-pools], которая доступна в предварительной версии.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Применяются ли обновления для системы безопасности к узлам агентов AKS?

Azure автоматически применяет исправления системы безопасности к узлам Linux в кластере по ночам. Тем не менее вы несете ответственность за то, что эти Linux, узлы перезагружаются как требуется. У вас есть несколько вариантов для перезагрузки узлов:

- Вручную на портале Azure или Azure CLI.
- Обновив кластер AKS. Обновление кластера [cordon и drain узлы][cordon-drain] automatically and then bring a new node online with the latest Ubuntu image and a new patch version or a minor Kubernetes version. For more information, see [Upgrade an AKS cluster][aks-upgrade].
- С помощью [Kured](https://github.com/weaveworks/kured), управляющая программа перезагрузки с открытым исходным кодом для Kubernetes. Kured работает в виде [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) и проверяет каждый узел на наличие файла, который указывает, что требуется перезагрузка. В кластере после перезагрузки ОС управляются тем же [cordon и drain процесс][cordon-drain] как обновление кластера.

Дополнительные сведения об использовании kured см. в разделе [применения обновлений безопасности и ядра на узлы в AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Узлы Windows Server

Для узлов Windows Server (в настоящее время в предварительной версии в AKS) обновление Windows автоматически запустить и применить последние обновления. По регулярному расписанию вокруг цикла выпуска обновления Windows, а также процесс проверки следует выполнить обновление на пулы узлов Windows Server в кластере AKS. В этом процессе создаются узлы под управлением последней версии образа Windows Server и исправления, а затем удаляет старые узлы. Дополнительные сведения об этом процессе см. в разделе [обновить пуле узел в AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Почему с AKS создаются две группы ресурсов?

Каждое развертывание AKS охватывает две группы ресурсов:

1. Созданием первой группы ресурсов. Эта группа содержит только ресурс службы Kubernetes. Поставщик ресурсов AKS автоматически создает второй группы ресурсов во время развертывания. Например, вторая группа ресурсов *MC_myResourceGroup_myAKSCluster_eastus*. Сведения о том, как указать имя этой второй группы ресурсов см. в разделе ниже.
1. Вторая группа ресурсов, называемые *группы ресурсов узла*, содержит все ресурсы инфраструктуры, связанной с кластером. Эти ресурсы включают виртуальные машины узла Kubernetes, виртуальную сеть и хранилище. По умолчанию группы ресурсов узла имеет имя, например *MC_myResourceGroup_myAKSCluster_eastus*. AKS автоматически удаляет ресурс узла всякий раз, когда кластер удаляется, поэтому его следует использовать только для ресурсов, которые совместно жизненного цикла кластера.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Предоставить собственный имя группы ресурсов узла AKS?

Да. По умолчанию AKS присвоит имя группы ресурсов узла *MC_clustername_resourcegroupname_location*, однако также можно указать собственное имя.

Чтобы задать собственное имя группы ресурсов, установите [предварительной версии aks][aks-preview-cli] версия расширения Azure CLI *версии 0.3.2* или более поздней версии. При создании кластера AKS с помощью [создать az aks][az-aks-create] команды, используйте *--узел resource-group* параметр и укажите имя группы ресурсов. Если вы [использовать шаблон Azure Resource Manager][aks-rm-template] развертывание кластера AKS, можно определить имя группы ресурсов с помощью *nodeResourceGroup* свойство.

* Эта группа вторичный ресурс автоматически создается поставщиком ресурсов Azure в вашей подписке.
* Имя группы ресурсов можно указать только в том случае, при создании кластера.

При работе с группой ресурсов узла, имейте в виду, что вы не можете:

* Укажите существующую группу ресурсов для группы ресурсов узла.
* Укажите другую подписку для группы ресурсов узла.
* Измените имя группы ресурсов узла, после создания кластера.
* Укажите имена для управляемых ресурсов в группе ресурсов узла.
* Изменить или удалить теги из управляемых ресурсов в группе ресурсов узла. (См. Дополнительные сведения в следующем разделе).

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Можно ли изменять метки и другие свойства и ресурсы AKS в группе ресурсов узла?

Если изменить или удалить теги, созданный в Azure и другие свойства ресурсов в группе ресурсов узла, можно получить непредвиденные результаты, например, масштабирование и обновление ошибки. AKS позволяет создавать и изменять настраиваемые теги. Можно создать или изменить пользовательские теги, например, чтобы назначить портале business center единица или затрат. Изменяя ресурсы в группе ресурсов узла в кластере AKS, нарушить цель уровня обслуживания (SLO). Дополнительные сведения см. в разделе [Does AKS предлагают соглашение об уровне обслуживания?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Какие контроллеры допуска Kubernetes поддерживает AKS? Можно ли добавлять и удалять контроллеры допуска?

AKS поддерживает следующие [контроллеров допуском][admission-controllers]:

- *NamespaceLifecycle*
- *LimitRanger*
- *ServiceAccount*
- *DefaultStorageClass*
- *DefaultTolerationSeconds*
- *MutatingAdmissionWebhook*
- *ValidatingAdmissionWebhook*
- *ResourceQuota*
- *DenyEscalatingExec*
- *AlwaysPullImages*

В настоящее время невозможно изменить список контроллеров допуском в AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Интегрируется ли Azure Key Vault с AKS?

AKS не интегрированы с хранилищем ключей Azure в настоящее время в собственном коде. Тем не менее [FlexVolume хранилище ключей Azure для Kubernetes проекта][keyvault-flexvolume] позволяет осуществлять прямое интеграции из модулей POD Kubernetes для секретов Key Vault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Можно ли запускать контейнеры Windows Server в AKS?

Да, контейнеры Windows Server доступны в предварительной версии. Чтобы запустить контейнеры Windows Server в AKS, создайте пул узлов под управлением Windows Server как гостевой ОС. Контейнеры Windows Server можно использовать только в Windows Server 2019. Чтобы приступить к работе, см. в разделе [создание кластера AKS с помощью пул узлов Windows Server][aks-windows-cli].

Поддержка Server окно узлов в пуле включает в себя некоторые ограничения, которые являются частью вышестоящего сервера Windows в проекте Kubernetes. Дополнительные сведения об этих ограничениях см. в разделе [контейнеры Windows Server в AKS ограничения][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS предлагает соглашение об уровне обслуживания?

В соглашении уровня обслуживания (SLA) поставщик соглашается покрыть клиента стоимости использования службы, если уровень опубликованной службы не выполняется. Так как AKS предоставляется бесплатно, без затрат, позволяющих покрыть, поэтому AKS не формальное соглашение об уровне ОБСЛУЖИВАНИЯ. Тем не менее AKS стремится обеспечить доступность по крайней мере 99,5% для сервера Kubernetes API.

## <a name="why-cant-i-set-maxpods-below-30"></a>Почему не удается задать maxPods ниже 30?

В AKS, можно задать `maxPods` значение при создании кластера с помощью шаблонов Azure CLI и Azure Resource Manager. Тем не менее, Kubenet и Azure CNI требуют *минимальное значение* (проверяется во время создания):

| Сеть | Минимальная | Максимальная |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| Kubenet | 30 | 110 |

Поскольку AKS — это управляемая служба, мы развертывание и управление надстройками и модулей как часть кластера. В прошлом, пользователи могут определять `maxPods` значение меньше, чем значение управляемых модулей, необходимая для работы (например, 30). AKS теперь вычисляет минимальное количество модулей с помощью следующей формулы: ((maxPods или (maxPods * vm_count)) > минимум модулей управляемые надстройки.

Пользователи не смогут переопределять минимальное `maxPods` проверки.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Можно применить скидки резервирования Azure к моей узлам агентов AKS?

Узлам агентов AKS оплачиваются как стандартных виртуальных машинах, поэтому, если вы приобрели [Azure резервирования][reservation-discounts] для размера виртуальной Машины, которую вы используете в AKS, автоматически применяются эти скидки.

<!-- LINKS - internal -->

[aks-regions]: ./quotas-skus-regions.md#region-availability
[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
[virtual-kubelet]: virtual-kubelet.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md
[reservation-discounts]: ../billing/billing-save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948