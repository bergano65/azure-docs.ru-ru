---
title: Вопросы и ответы о Службе Azure Kubernetes (AKS)
description: Ответы на распространенные вопросы о Службе Azure Kubernetes (AKS).
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 05/06/2019
ms.author: iainfou
ms.openlocfilehash: f365fcd61944fbae131ab79a1c3660aaf02fa8d7
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65073933"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Вопросы и ответы о Службе Azure Kubernetes (AKS)

В этой статье представлены ответы на часто задаваемые вопросы о Службе Azure Kubernetes.

## <a name="which-azure-regions-provide-the-azure-kubernetes-service-aks-today"></a>В каких регионах Azure сейчас доступна Служба Azure Kubernetes (AKS)?

Полный список регионов, в которых доступна AKS, см. в разделе [Регионы доступности][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>Поддерживает ли AKS автомасштабирование узла?

Да, автоматическое масштабирование доступно через [Kubernetes autoscaler][auto-scaler] для Kubernetes 1.10. Дополнительные сведения о том, как вручную настроить и использовать инструмент автомасштабирования кластера см. в разделе [автомасштабирования кластеров в AKS][aks-cluster-autoscale].

Также можно использовать автомасштабирования встроенные кластера (в настоящее время в предварительной версии в AKS) для управления масштабированием узлов. Дополнительные сведения см. в разделе [автоматически масштабировать кластер в соответствии с требованиями приложения в AKS][aks-cluster-autoscaler].

## <a name="does-aks-support-kubernetes-role-based-access-control-rbac"></a>Поддерживает ли AKS для Kubernetes управление доступом на основе ролей (RBAC)?

Да, Kubernetes RBAC включается по умолчанию при создании кластеров с помощью Azure CLI. RBAC можно включить для кластеров, созданных с помощью портала Azure или шаблонов.

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Можно ли развернуть AKS в имеющейся виртуальной сети?

Да, вы можете развернуть кластер AKS в существующей виртуальной сети с помощью [расширенного сетевого взаимодействия][aks-advanced-networking].

## <a name="can-i-restrict-the-kubernetes-api-server-to-only-be-accessible-within-my-virtual-network"></a>Можно ли ограничить доступ к серверу API Kubernetes только моей виртуальной сетью?

На данный момент нет. Сервер API Kubernetes представляется с виде общедоступного полного доменного имени (FQDN). Вы можете управлять доступом к своему кластеру с помощью [управления доступом на основе ролей Kubernetes (RBAC) и Azure Active Directory (AAD)][aks-rbac-aad].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Применяются ли обновления для системы безопасности к узлам агентов AKS?

Azure автоматически применяет исправления системы безопасности к узлам Linux в кластере по ночам. Тем не менее вы несете ответственность за то, что эти Linux, узлы перезагружаются как требуется. У вас есть несколько вариантов выполнения перезагрузки узла.

- Вручную на портале Azure или Azure CLI.
- Обновив кластер AKS. Кластер автоматически обновляет [узлы cordon и drain][cordon-drain], после чего создает их резервную копию с помощью последнего образа Ubuntu и новой версии исправлений или дополнительной версии Kubernetes. Дополнительные сведения см. в статье [Обновление кластера службы Azure Kubernetes (AKS)][aks-upgrade].
- С помощью [Kured](https://github.com/weaveworks/kured) (управляющая программа перезагрузки с открытым исходным кодом для Kubernetes). Kured работает в виде [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) и проверяет каждый узел на наличие файла, указывающего на необходимость перезагрузки. Перезагрузки операционной системы управляются в кластере с использованием [процесса cordon и drain][cordon-drain] для обновления кластера.

Дополнительные сведения об использовании kured см. в статье о [применении обновлений безопасности и ядра на узлах в AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Узлы Windows Server

Для узлов Windows Server (в настоящее время в предварительной версии в AKS) обновление Windows автоматически запустить и применить последние обновления. По регулярному расписанию вокруг цикла выпуска обновления Windows, а также процесс проверки следует выполнить обновление на пулы узлов Windows Server в кластере AKS. В этом процессе создаются узлы под управлением последней версии образа Windows Server и исправления, а затем удаляет старые узлы. Дополнительные сведения об этом процессе см. в разделе [обновить пуле узел в AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Почему с AKS создаются две группы ресурсов?

Каждое развертывание AKS охватывает две группы ресурсов:

- Первая группа ресурсов создается пользователем и содержит только ресурс службы Kubernetes. Во время развертывания поставщик ресурсов AKS автоматически создает вторую группу, такую как *MC_myResourceGroup_myAKSCluster_eastus*. Сведения о том, как можно указать имя этой второй группы ресурсов см. в разделе в следующем разделе.
- Эта вторая группа ресурсов, такая как *MC_myResourceGroup_myAKSCluster_eastus*, содержит все ресурсы инфраструктуры, связанные с кластером. Эти ресурсы включают виртуальные машины узла Kubernetes, виртуальную сеть и хранилище. Эта отдельная группа ресурсов создается, чтобы упростить процесс очистки ресурсов.

Если вы создаете ресурсы для использования с кластером AKS (например, учетные записи хранения или зарезервированные общедоступные IP-адреса), поместите их в автоматически созданную группу ресурсов.

## <a name="can-i-provide-my-own-name-for-the-aks-infrastructure-resource-group"></a>Предоставить собственный имя группы ресурсов AKS инфраструктуры?

Да. По умолчанию поставщик ресурсов AKS автоматически создает дополнительный группу ресурсов во время развертывания, такие как *MC_myResourceGroup_myAKSCluster_eastus*. Чтобы соответствовать корпоративной политике, разработчик может предоставить собственное имя для этого управляемого кластера (*MC_*) группы ресурсов.

Чтобы задать собственное имя группы ресурсов, установите [предварительной версии aks] [ aks-preview-cli] версия расширения Azure CLI *версии 0.3.2* или более поздней версии. При создании кластера AKS с помощью [создать az aks] [ az-aks-create] команды, используйте *--узел resource-group* параметр и укажите имя группы ресурсов. Если вы [использовать шаблон Azure Resource Manager] [ aks-rm-template] развертывание кластера AKS, можно определить имя группы ресурсов с помощью *nodeResourceGroup* свойство.

* Эта группа ресурсов создается автоматически, поставщик ресурсов Azure в вашей подписке.
* Имя группы ресурсов можно указать только в том случае, при создании кластера.

Не поддерживаются следующие сценарии:

* Нельзя указать существующую группу ресурсов для *MC_* группы.
* Нельзя указать другой подписки для *MC_* группы ресурсов.
* Невозможно изменить *MC_* имя группы ресурсов после создания кластера.
* Нельзя указать имена для управляемых ресурсов в пределах *MC_* группы ресурсов.
* Нельзя изменить или удалить теги из управляемых ресурсов в пределах *MC_* группы ресурсов (см. Дополнительные сведения в следующем разделе).

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-mc-resource-group"></a>Можно ли изменять теги и другие свойства ресурсов AKS в группе ресурсов MC_*?

Изменение и удаление тегов, созданных в Azure, и других свойств ресурсов в группе *MC_** может привести к непредвиденным результатам, таким как ошибки масштабирования и обновления. Поддерживается создание и изменение дополнительных настраиваемых тегов, например, для назначения бизнес-единицы или места возникновения затрат. Изменение ресурсов в *MC_** в кластере AKS нарушает цель уровня обслуживания (SLO). Дополнительные сведения см. в разделе [AKS предлагает соглашение об уровне обслуживания?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Какие контроллеры допуска Kubernetes поддерживает AKS? Можно ли добавлять и удалять контроллеры допуска?

AKS поддерживает следующие [контроллеры допуска][admission-controllers]:

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

Сейчас изменить список контроллеров допуска в AKS невозможно.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Интегрируется ли Azure Key Vault с AKS?

В данный момент AKS не имеет собственной интеграции с Azure Key Vault. Кроме того, [Azure Key Vault FlexVolume для проекта Kubernetes][keyvault-flexvolume] допускает прямую интеграцию из модулей (pod) Kubernetes в секреты KeyVault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Можно ли запускать контейнеры Windows Server в AKS?

Да, контейнеры Windows Server доступны в предварительной версии. Чтобы запустить контейнеры Windows Server в AKS, создайте пул узлов под управлением Windows Server как гостевой ОС. Контейнеры Windows Server можно использовать только Windows Server 2019. Чтобы начать работу, [создание кластера AKS с помощью пул узлов Windows Server][aks-windows-cli].

Поддержка пула узлов сервера окна включает некоторые ограничения, которые являются частью вышестоящего сервера Windows в проекте Kubernetes. Дополнительные сведения об этих ограничениях см. в разделе [контейнеры Windows Server в AKS ограничения][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS предлагает соглашение об уровне обслуживания?

В соглашении об уровне обслуживания (SLA) поставщик соглашается возместить клиенту стоимость использования службы, если уровень обслуживания не достигнут. Так как сама служба AKS является бесплатной, нет суммы для возмещения и, следовательно, никакого официального SLA. Тем не менее мы стремимся поддерживать доступность AKS не менее 99,5 % для сервера API Kubernetes.

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
[aks-rm-template]: /rest/api/aks/managedclusters/createorupdate#managedcluster
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: windows-node-limitations.md

<!-- LINKS - external -->

[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
