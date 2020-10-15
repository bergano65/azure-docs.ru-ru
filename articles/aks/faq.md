---
title: Вопросы и ответы о Службе Azure Kubernetes (AKS)
description: найдите ответы на некоторые распространенные вопросы о Службе Azure Kubernetes (AKS).
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 9506b3430775f137c09fe3d155b203cdcbd14783
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070560"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Вопросы и ответы о Службе Azure Kubernetes (AKS)

В этой статье представлены ответы на часто задаваемые вопросы о Службе Azure Kubernetes.

## <a name="which-azure-regions-currently-provide-aks"></a>В каких регионах Azure в настоящее время предоставляется служба AKS?

Полный список регионов, в которых доступна служба AKS, см. в разделе [Регионы доступности][aks-regions].

## <a name="can-i-spread-an-aks-cluster-across-regions"></a>Может ли кластер AKS охватывать несколько регионов?

Нет. Кластеры AKS являются региональными ресурсами и не могут охватывать несколько регионов. Рекомендации по созданию архитектуры, включающей в себя несколько регионов, см. в [руководстве по обеспечению непрерывности бизнес-процессов и аварийного восстановления][bcdr-bestpractices].

## <a name="can-i-spread-an-aks-cluster-across-availability-zones"></a>Может ли кластер AKS охватывать несколько зон доступности?

Да. Кластер AKS можно развернуть в одной или нескольких [зонах доступности][availability-zones] в [регионах, в которых они поддерживаются][az-regions].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Можно ли ограничить доступ пользователей к серверу API Kubernetes?

Да. Доступ к серверу API можно ограничить двумя способами.

- Используйте [допустимые диапазоны IP-адресов для сервера API][api-server-authorized-ip-ranges], если нужно поддерживать общедоступную конечную точку для сервера API, но ограничить доступ набором доверенных диапазонов IP-адресов.
- Используйте [частный кластер][private-clusters], если сервер API должен быть доступен *только* из виртуальной сети.

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Могут ли в одном кластере быть виртуальные машины разных размеров?

Да, вы можете использовать виртуальные машины разных размеров в кластере AKS, создав [несколько пулов узлов][multi-node-pools].

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Применяются ли обновления для системы безопасности к узлам агентов AKS?

Да, Azure автоматически применяет обновления для системы безопасности к узлам Linux в кластере в соответствии с графиком резервного копирования ночью. Однако вы несете ответственность за обеспечение перезагрузки узлов Linux при необходимости. У вас есть несколько вариантов перезагрузки узла:

- Вручную на портале Azure или Azure CLI.
- Обновив кластер AKS. Кластер автоматически обновляет [узлы cordon и drain][cordon-drain], после чего включает узлы с помощью последнего образа Ubuntu и новой версии исправлений или дополнительной версии Kubernetes. Дополнительные сведения см. в статье [Обновление кластера службы Azure Kubernetes (AKS)][aks-upgrade].
- С помощью [Kured](https://github.com/weaveworks/kured) (управляющая программа перезагрузки с открытым кодом для Kubernetes). Kured работает в виде [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) и проверяет каждый узел на наличие файла, указывающего на необходимость перезагрузки. Перезагрузки операционной системы управляются в кластере с использованием [процесса cordon и drain][cordon-drain] для обновления кластера.

Дополнительные сведения об использовании kured см. в статье о [применении обновлений безопасности и ядра к узлам в AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Узлы Windows Server

Для узлов Windows Server Центр обновления Windows не выполняет автоматический запуск и применение последних обновлений. Вам необходимо выполнять обновление кластера и пулов узлов Windows Server в кластере AKS по регулярному расписанию, основанному на цикле выпуска Центра обновления Windows и вашем собственном процессе проверки. При обновлении создаются узлы, в которых запускается последняя версия образа и исправления Windows Server. Затем более старые версии узлов удаляются. Дополнительные сведения об обновлении пула узлов в AKS см. в [этой статье][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Почему с AKS создаются две группы ресурсов?

AKS строится на основе нескольких ресурсов инфраструктуры Azure, включая масштабируемые наборы виртуальных машин, виртуальные сети и управляемые диски. Это позволяет использовать многие базовые возможности платформы Azure в управляемой среде Kubernetes, предоставляемой AKS. Например, большинство типов виртуальных машин Azure можно использовать с AKS напрямую, а резервирования Azure можно использовать для автоматического получения скидок на эти ресурсы.

Для обеспечения такой архитектуры каждое развертывание AKS охватывает две группы ресурсов.

1. Первую группу ресурсов создаете вы. Эта группа содержит только ресурс службы Kubernetes. Поставщик ресурсов AKS автоматически создает вторую группу ресурсов во время развертывания. Примером второй группы ресурсов является *MC_myResourceGroup_myAKSCluster_eastus*. Сведения об указании имени второй группы ресурсов см. в следующем разделе.
1. Вторая группа ресурсов, которая называется *группой ресурсов узла*, содержит все ресурсы инфраструктуры, связанные с кластером. Эти ресурсы включают виртуальные машины узла Kubernetes, виртуальную сеть и хранилище. По умолчанию группа ресурсов узла имеет имя наподобие *MC_myResourceGroup_myAKSCluster_eastus*. AKS автоматически удаляет ресурс узла при удалении кластера, поэтому его следует использовать только для ресурсов, которые имеют общий жизненный цикл кластера.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Можно ли указать собственное имя для группы ресурсов узла AKS?

Да. По умолчанию AKS присваивает группе ресурсов узла имя *MC_resourcegroupname_clustername_location*, но вы можете изменить его произвольным образом.

Чтобы указать собственное имя для группы ресурсов, установите для Azure CLI расширение [aks-preview][aks-preview-cli] *0.3.2* или более поздней версии. При создании кластера AKS с помощью команды [az aks create][az-aks-create] используйте параметр *--node-resource-group* и укажите имя группы ресурсов. Если вы [применяете шаблон Azure Resource Manager][aks-rm-template] для развертывания кластера AKS, имя группы ресурсов можно определить с помощью свойства *nodeResourceGroup*.

* Дополнительную группу ресурсов автоматически создает поставщик ресурсов Azure в вашей подписке.
* Вы можете указать пользовательское имя для группы ресурсов только при создании кластера.

При работе с группой ресурсов узла учитывайте, что нельзя:

* указать существующую группу ресурсов для узлов;
* поместить узлы в группу ресурсов в другой подписке;
* изменить имя группы ресурсов узла после создания кластера;
* выбрать имена управляемых ресурсов в группе ресурсов для узлов;
* изменять или удалять созданные Azure теги управляемых ресурсов в группе ресурсов для узлов. (Дополнительные сведения см. в следующем разделе.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Можно ли изменять теги и другие свойства ресурсов AKS в группе ресурсов узла?

Изменение или удаление тегов, созданных в Azure, и других свойств ресурсов в группе ресурсов узла может привести к непредвиденным результатам, таким как ошибки масштабирования и обновления. AKS позволяет создавать и изменять пользовательские теги, созданные конечными пользователями, а также добавлять эти теги при [создании пула узлов](use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool). Это может потребоваться, например, для назначения подразделения или места возникновения затрат. Это также можно сделать, создав политики Azure с областью действия в управляемой группе ресурсов.

Однако изменение всех **тегов, созданных в Azure** , для ресурсов в группе ресурсов узла в кластере AKS является неподдерживаемым действием, которое нарушает цель уровня обслуживания (цель цели). Дополнительные сведения см. в разделе [AKS предлагает соглашение об уровне обслуживания?](#does-aks-offer-a-service-level-agreement)

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

В настоящее время изменить список контроллеров допуска в AKS невозможно.

## <a name="can-i-use-admission-controller-webhooks-on-aks"></a>Можно ли использовать веб-перехватчики контроллеров допуска в AKS?

Да, вы можете использовать веб-перехватчики контроллеров допуска в AKS. Рекомендуется исключить внутренние пространства имен AKS, помеченные **меткой control-plane**. Например, можно добавить приведенный ниже код в конфигурацию веб-перехватчика.

```
namespaceSelector:
    matchExpressions:
    - key: control-plane
      operator: DoesNotExist
```

## <a name="can-admission-controller-webhooks-impact-kube-system-and-internal-aks-namespaces"></a>Могут ли веб-перехватчики контроллеров допуска влиять на kube-system и внутренние пространства имен AKS?

Чтобы обеспечить стабильность системы и предотвратить влияние настраиваемых контроллеров допусков на внутренние службы в kube-system, пространство имен AKS содержит **функцию принудительного применения допуска**, которая автоматически исключает внутренние пространства имен kube-system и AKS. Эта служба гарантирует, что пользовательские контроллеры допуска не влияют на службы, работающие в kube-system.

Если у вас есть критически важный вариант использования для развертывания в kube-system (не рекомендуется), к которому должен применяться пользовательский веб-перехватчик допуска, вы можете добавить приведенную ниже метку или заметку, чтобы функция принудительного применения допуска игнорировала его.

Метка ```"admissions.enforcer/disabled": "true"``` или заметка ```"admissions.enforcer/disabled": true```

## <a name="is-azure-key-vault-integrated-with-aks"></a>Интегрируется ли Azure Key Vault с AKS?

В настоящее время AKS не имеет собственной интеграции с Azure Key Vault. Однако [поставщик Azure Key Vault для хранилища секретов CSI][csi-driver] допускает прямую интеграцию модулей pod Kubernetes с секретами Key Vault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Можно ли запускать контейнеры Windows Server в AKS?

Да, контейнеры Windows Server доступны в AKS. Чтобы запустить контейнеры Windows Server в AKS, создайте пул узлов с Windows Server в качестве гостевой ОС. Контейнеры Windows Server могут использовать только Windows Server 2019. Чтобы приступить к работе, см. статью [Создание кластера AKS с пулом узлов Windows Server][aks-windows-cli].

На поддержку пула узлов в Windows Server распространяется ряд ограничений, которые налагаются вышестоящим сервером Windows Server в проекте Kubernetes. Дополнительные сведения об этих ограничениях см. в статье [Ограничения для контейнеров Windows Server в AKS][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>AKS предлагает соглашение об уровне обслуживания?

AKS предоставляет гарантии в рамках соглашения об уровне обслуживания в качестве дополнительного компонента при заключении [соглашения об уровне обслуживания с гарантией времени непрерывной работы][uptime-sla].

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Можно ли применять скидки на резервирования Azure к узлам агента AKS?

Узлы агента AKS оплачиваются как стандартные виртуальные машины Azure, поэтому если вы приобрели [резервирования Azure][reservation-discounts] для размера виртуальной машины, которые используются в AKS, эти скидки применяются автоматически.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Можно ли переносить кластер между клиентами Azure?

Перемещение кластера AKS между клиентами сейчас не поддерживается.

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Можно ли переносить кластер между подписками?

Перемещение кластеров между подписками в настоящее время не поддерживается.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Можно ли переместить кластеры AKS из текущей подписки Azure в другую? 

Перемещение кластера AKS и связанных с ним ресурсов между подписками Azure не поддерживается.

## <a name="can-i-move-my-aks-cluster-or-aks-infrastructure-resources-to-other-resource-groups-or-rename-them"></a>Можно ли переместить ресурсы инфраструктуры кластера AKS или AKS в другие группы ресурсов или переименовать их?

Перемещение или Переименование кластера AKS и связанных с ним ресурсов не поддерживается.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Почему удаление моего кластера занимает так много времени? 

Большинство кластеров удаляются по запросу пользователя. В некоторых случаях, особенно если клиенты используют собственные группы ресурсов или выполняют задачи между группами ресурсов, удаление может занять больше времени или завершиться сбоем. Если у вас возникла проблема с удалением, убедитесь в том, что группа ресурсов не заблокирована, что ни один из ресурсов, не входящих в группу ресурсов, не связан с ней и т. д.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Можно ли обновить кластер, если имеются модули pod или развертывания в состоянии NodeLost или Unknown?

Можно, но в AKS это не рекомендуется. Обновление желательно выполнять, когда кластер находится в известном и работоспособном состоянии.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Можно ли выполнить обновление, если один или несколько узлов кластера находятся в неработоспособном состоянии или их работа завершена?

Нет, перед обновлением каким-либо образом удалите из кластера все узлы в состоянии сбоя.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>При запуске удаления кластера выводится сообщение об ошибке `[Errno 11001] getaddrinfo failed` 

Чаще всего это вызвано наличием одной или нескольких групп безопасности сети, которые все еще используются и связаны с кластером.  Удалите их и повторите попытку удаления.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>После запуска обновления стало циклически происходить аварийное завершение работы модулей pod, и пробы готовности завершаются неудачно.

Убедитесь в том, что срок действия субъекта-службы не истек.  См. статьи [Субъект-служба AKS](./kubernetes-service-principal.md) и [Обновление учетных данных AKS](./update-credentials.md).

## <a name="my-cluster-was-working-but-suddenly-cannot-provision-loadbalancers-mount-pvcs-etc"></a>Мой кластер работал, но внезапно пропала возможность подготовки балансировщиков нагрузки, подключения PVC и т. д. 

Убедитесь в том, что срок действия субъекта-службы не истек.  См. статьи [Субъект-служба AKS](./kubernetes-service-principal.md) и [Обновление учетных данных AKS](./update-credentials.md).

## <a name="can-i-scale-my-aks-cluster-to-zero"></a>Можно ли масштабировать кластер AKS до нуля?
Вы можете полностью [прерывать работу кластера AKS](start-stop-cluster.md), сохраняя соответствующие затраты на вычисление. Кроме того, вы можете [масштабировать или автоматически масштабировать все или отдельные `User` Пулы узлов](scale-cluster.md#scale-user-node-pools-to-0) до 0, сохраняя только необходимую конфигурацию кластера.
Нельзя напрямую масштабировать [пулы системных узлов](use-system-pools.md) до 0.

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Можно ли использовать интерфейсы API масштабируемого набора виртуальных машин для масштабирования вручную?

Нет, операции масштабирования с использованием интерфейсов API масштабируемого набора виртуальных машин не поддерживаются. Используйте интерфейсы API AKS (`az aks scale`).

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Можно ли использовать масштабируемые наборы виртуальных машин для ручного масштабирования до нуля узлов?

Нет, операции масштабирования с использованием интерфейсов API масштабируемого набора виртуальных машин не поддерживаются.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Можно ли остановить все виртуальные машины или отменить их выделение?

Хотя в AKS есть механизмы устойчивости для поддержки такой конфигурации и восстановления после нее, использовать ее не рекомендуется.

## <a name="can-i-use-custom-vm-extensions"></a>Можно ли использовать пользовательские расширения виртуальных машин?

Нет, AKS является управляемой службой, и управление ресурсами IaaS не поддерживается. Для установки пользовательских и тому подобных компонентов используйте интерфейсы API и механизмы Kubernetes. Например, используйте контроллеры DaemonSet для установки необходимых компонентов.

## <a name="does-aks-store-any-customer-data-outside-of-the-clusters-region"></a>Сохраняет ли AKS данные клиентов за пределами региона кластера?

Функция, позволяющая хранить данные клиентов в одном регионе, в настоящее время доступна только в регионе Юго-Восточной Азии (Сингапур) Азиатско-Тихоокеанский регион Geo. Для всех других регионов данные клиента хранятся в геообъектах.

<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./cluster-autoscaler.md
[aks-advanced-networking]: ./configure-azure-cni.md
[aks-rbac-aad]: ./azure-ad-integration-cli.md
[node-updates-kured]: node-updates-kured.md
[aks-preview-cli]: /cli/azure/ext/aks-preview/aks
[az-aks-create]: /cli/azure/aks#az-aks-create
[aks-rm-template]: /azure/templates/microsoft.containerservice/2019-06-01/managedclusters
[aks-cluster-autoscaler]: cluster-autoscaler.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[aks-windows-cli]: windows-container-cli.md
[aks-windows-limitations]: ./windows-faq.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md
[private-clusters]: ./private-clusters.md
[bcdr-bestpractices]: ./operator-best-practices-multi-region.md#plan-for-multiregion-deployment
[availability-zones]: ./availability-zones.md
[az-regions]: ../availability-zones/az-region.md
[uptime-sla]: ./uptime-sla.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
[csi-driver]: https://github.com/Azure/secrets-store-csi-driver-provider-azure
[vm-sla]: https://azure.microsoft.com/support/legal/sla/virtual-machines/