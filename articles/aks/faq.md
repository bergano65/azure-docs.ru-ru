---
title: Вопросы и ответы о Службе Azure Kubernetes (AKS)
description: Найдите ответы на некоторые распространенные вопросы о службе Azure Kubernetes (AKS).
services: container-service
author: mlearned
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 07/08/2019
ms.author: mlearned
ms.openlocfilehash: 0e3a29e6e8f21658f03fb7fc059b54aa167496d4
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147183"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Вопросы и ответы о Службе Azure Kubernetes (AKS)

В этой статье представлены ответы на часто задаваемые вопросы о Службе Azure Kubernetes.

## <a name="which-azure-regions-currently-provide-aks"></a>Какие регионы Azure в настоящее время предоставляют AKS?

Полный список доступных регионов см. в разделе [AKS regions and Availability][aks-regions].

## <a name="does-aks-support-node-autoscaling"></a>Поддерживает ли AKS автомасштабирование узла?

Да, возможность автоматического масштабирования узлов агентов по горизонтали в AKS в настоящее время доступна в предварительной версии. Инструкции см. [в разделе Автоматическое масштабирование кластера в соответствии с потребностями приложений в AKS][aks-cluster-autoscaler] . Автомасштабирование AKS основано на автоматическом [масштабировании Kubernetes][auto-scaler].

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Можно ли развернуть AKS в имеющейся виртуальной сети?

Да, можно развернуть кластер AKS в существующей виртуальной сети с помощью [функции Расширенная сеть][aks-advanced-networking].

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Можно ли ограничить доступ к серверу API Kubernetes?

Да, вы можете ограничить доступ к серверу API Kubernetes, используя [диапазоны IP-адресов, разрешенные сервером API][api-server-authorized-ip-ranges], которые в настоящее время доступны в предварительной версии.

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Можно ли сделать сервер API Kubernetes доступным только в моей виртуальной сети?

На данный момент нет, но это запланировано. Ход выполнения можно отслеживать в [репозитории GitHub AKS][private-clusters-github-issue].

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Можно ли использовать разные размеры виртуальных машин в одном кластере?

Да, вы можете использовать различные размеры виртуальных машин в кластере AKS, создав [несколько пулов узлов][multi-node-pools], которые в настоящее время доступны в предварительной версии.

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Применяются ли обновления для системы безопасности к узлам агентов AKS?

Azure автоматически применяет исправления безопасности к узлам Linux в кластере в соответствии с расписанием по ночам. Однако вы несете ответственность за то, чтобы эти узлы Linux перезагрузились по мере необходимости. Существует несколько вариантов перезагрузки узлов.

- Вручную на портале Azure или Azure CLI.
- Обновив кластер AKS. Кластер автоматически обновляет [узлы Cordon и сток][cordon-drain] , а затем переводит новый узел в оперативный режим с помощью последнего образа Ubuntu и новой версии исправления или вспомогательной версии Kubernetes. Дополнительные сведения см. в статье [Обновление кластера AKS][aks-upgrade].
- С помощью [куред](https://github.com/weaveworks/kured)можно запустить управляющую программу с открытым исходным кодом для Kubernetes. Куред запускается как [демон](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) и отслеживает каждый узел на наличие файла, указывающего на необходимость перезагрузки. В кластере перезагрузки ОС управляются одним и тем же [Cordon и процессом очистки][cordon-drain] при обновлении кластера.

Дополнительные сведения об использовании куред см. в статье [применение обновлений безопасности и ядра к узлам в AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Узлы Windows Server

Для узлов Windows Server (на данный момент в предварительной версии в AKS) Центр обновления Windows не выполняет автоматический запуск и не применяет последние обновления. По регулярному расписанию Центр обновления Windows цикла выпуска и собственного процесса проверки необходимо выполнить обновление кластера и пулов узлов Windows Server в кластере AKS. В процессе обновления создаются узлы, на которых выполняется последняя версия образа и исправления Windows Server, а затем удаляются старые узлы. Дополнительные сведения об этом процессе см. [в разделе Обновление пула узлов в AKS][nodepool-upgrade].

## <a name="why-are-two-resource-groups-created-with-aks"></a>Почему с AKS создаются две группы ресурсов?

Каждое развертывание AKS охватывает две группы ресурсов:

1. Вы создадите первую группу ресурсов. Эта группа содержит только ресурс службы Kubernetes. Поставщик ресурсов AKS автоматически создает вторую группу ресурсов во время развертывания. Примером второй группы ресурсов является *MC_myResourceGroup_myAKSCluster_eastus*. Сведения о том, как указать имя второй группы ресурсов, см. в следующем разделе.
1. Вторая группа ресурсов, называемая *группой ресурсов узла*, содержит все ресурсы инфраструктуры, связанные с кластером. Эти ресурсы включают виртуальные машины узла Kubernetes, виртуальную сеть и хранилище. По умолчанию группа ресурсов узла имеет имя, например *MC_myResourceGroup_myAKSCluster_eastus*. AKS автоматически удаляет ресурс узла при каждом удалении кластера, поэтому его следует использовать только для ресурсов, которые совместно используют жизненный цикл кластера.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Можно ли указать собственное имя для группы ресурсов узла AKS?

Да. По умолчанию AKS будет наименовать группу ресурсов узла *MC_clustername_resourcegroupname_location*, но можно также указать собственное имя.

Чтобы указать собственное имя группы ресурсов, установите расширение [AKS-preview][aks-preview-cli] Azure CLI версии *0.3.2* или более поздней. При создании кластера AKS с помощью команды [AZ AKS Create][az-aks-create] используйте параметр *--node-Resource-Group* и укажите имя группы ресурсов. При [использовании шаблона Azure Resource Manager][aks-rm-template] для развертывания кластера AKS можно определить имя группы ресурсов с помощью свойства *нодересаурцеграуп* .

* Вторичная группа ресурсов автоматически создается поставщиком ресурсов Azure в собственной подписке.
* Имя настраиваемой группы ресурсов можно указать только при создании кластера.

При работе с группой ресурсов узла следует помнить, что вы не можете:

* Укажите существующую группу ресурсов для группы ресурсов узла.
* Укажите другую подписку для группы ресурсов узла.
* Измените имя группы ресурсов узла после создания кластера.
* Укажите имена управляемых ресурсов в группе ресурсов узла.
* Изменение или удаление тегов управляемых ресурсов в группе ресурсов узла. (Дополнительные сведения см. в следующем разделе.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Можно ли изменить теги и другие свойства ресурсов AKS в группе ресурсов узла?

При изменении или удалении тегов, созданных Azure, и других свойств ресурсов в группе ресурсов узла можно получить непредвиденные результаты, такие как ошибки масштабирования и обновления. AKS позволяет создавать и изменять пользовательские теги. Может потребоваться создать или изменить пользовательские теги, например, чтобы назначить подразделение или центр затрат. Изменяя ресурсы в группе ресурсов узла в кластере AKS, вы разбиваете цель уровня обслуживания (уровень обслуживания). Дополнительные сведения см [. в разделе о том, что AKS предлагает соглашение об уровне обслуживания?](#does-aks-offer-a-service-level-agreement)

## <a name="what-kubernetes-admission-controllers-does-aks-support-can-admission-controllers-be-added-or-removed"></a>Какие контроллеры допуска Kubernetes поддерживает AKS? Можно ли добавлять и удалять контроллеры допуска?

AKS поддерживает следующие [контроллеры][admission-controllers]допусков:

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

Сейчас вы не можете изменить список контроллеров допусков в AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Интегрируется ли Azure Key Vault с AKS?

В настоящее время AKS не интегрирован с Azure Key Vault. Однако [проект Azure Key Vault флексволуме для Kubernetes][keyvault-flexvolume] обеспечивает прямую интеграцию из Kubernetes Pods в Key Vault секреты.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Можно ли запускать контейнеры Windows Server в AKS?

Да, контейнеры Windows Server доступны в предварительной версии. Чтобы запустить контейнеры Windows Server в AKS, создайте пул узлов под управлением Windows Server в качестве гостевой ОС. Контейнеры Windows Server могут использовать только Windows Server 2019. Чтобы приступить к работе, см. раздел [Создание кластера AKS с пулом узлов Windows Server][aks-windows-cli].

Поддержка Windows Server для пула узлов включает некоторые ограничения, которые входят в состав вышестоящего Kubernetes в проекте. Дополнительные сведения об этих ограничениях см. [в статье контейнеры Windows Server в разделе ограничения AKS][aks-windows-limitations].

## <a name="does-aks-offer-a-service-level-agreement"></a>Предлагает ли AKS соглашение об уровне обслуживания?

В соглашении об уровне обслуживания (SLA) поставщик соглашается платить клиенту на стоимость службы, если опубликованный уровень обслуживания не будет удовлетворен. Так как AKS бесплатно, затраты на возмещение не предоставляются, поэтому AKS не имеет формального соглашения об уровне обслуживания. Однако AKS ищет доступность по крайней мере 99,5% для сервера API Kubernetes.

## <a name="why-cant-i-set-maxpods-below-30"></a>Почему я не могу установить Максподс ниже 30?

В AKS можно задать `maxPods` значение при создании кластера с помощью шаблонов Azure CLI и Azure Resource Manager. Однако для Кубенет и Azure CNI требуется *минимальное значение* (проверка во время создания):

| Сети | Минимум | Максимум |
| -- | :--: | :--: |
| Azure CNI | 30 | 250 |
| кубенет | 30 | 110 |

Так как AKS является управляемой службой, мы развертываем надстройки и модули управления ими в составе кластера. Раньше пользователи могли определить `maxPods` значение меньше, чем значение, которое требуется для выполнения управляемых модулей Pod (например, 30). AKS теперь вычисляет минимальное количество модулей Pod с помощью следующей формулы: ((Максподс или (Максподс * vm_count)) > минимальное число модулей управления для управляемых надстроек.

Пользователи не могут переопределить минимальную `maxPods` проверку.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Можно ли применять скидки на резервирование Azure к узлам агента AKS?

Узлы агента AKS выставляются как стандартные виртуальные машины Azure, поэтому, если вы приобрели [резервирование Azure][reservation-discounts] для размера виртуальной машины, используемой в AKS, эти скидки применяются автоматически.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Можно ли переместить или перенести кластер между клиентами Azure?

`az aks update-credentials` Команду можно использовать для перемещения кластера AKS между клиентами Azure. Следуйте инструкциям в разделе [выберите, чтобы обновить или создать субъект-службу](https://docs.microsoft.com/azure/aks/update-credentials) , а затем [Обновите кластер AKS с новыми учетными данными](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-credentials).

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Можно ли переместить или перенести кластер между подписками?

Перемещение кластеров между подписками в настоящее время не поддерживается.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Можно ли переместить кластеры AKS из текущей подписки Azure в другую? 

Перемещение кластера AKS и связанных с ним ресурсов между подписками Azure не поддерживается.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Почему Удаление кластера занимает так много времени? 

Большинство кластеров удаляются по запросу пользователя. в некоторых случаях, особенно если клиенты используют собственную группу ресурсов или выполнение RG задач может занять дополнительное время или завершиться сбоем. Если у вас возникли проблемы с удалением, убедитесь, что у вас нет блокировок на RG, что все ресурсы за пределами RG не связаны с RG и т. д.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Если у меня есть модуль Pod/развертывания в состоянии "Ноделост" или "неизвестно", можно ли по-прежнему обновить кластер?

Вы можете, но AKS не рекомендует. В идеале следует выполнять обновления, если состояние кластера известно и является работоспособным.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Если у меня есть кластер с одним или несколькими узлами в неработоспособном состоянии или завершение работы, можно ли выполнить обновление?

Нет, удалите или удалите все узлы в состоянии сбоя или в противном случае удалено из кластера перед обновлением.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>При выполнении удаления кластера отображается сообщение об ошибке`[Errno 11001] getaddrinfo failed` 

Чаще всего это вызвано тем, что пользователи, имеющие одну или несколько групп безопасности сети (группы безопасности сети), все еще используются и связаны с кластером.  Удалите их и повторите попытку удаления.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Я запустил обновление, но теперь модули Pod находятся в циклах аварийного восстановления, и проверки готовности не пройдены?

Убедитесь, что срок действия субъекта-службы не истек.  См.: [AKS субъекта-службы](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) и [AKS учетные данные обновления](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>Мой кластер работал, но внезапно не может подготавливать LoadBalancers, подключать постоянные виртуальные каналы и т. д.? 

Убедитесь, что срок действия субъекта-службы не истек.  См.: [AKS субъекта-службы](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) и [AKS учетные данные обновления](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Можно ли использовать API-интерфейсы масштабируемого набора виртуальных машин для масштабирования вручную?

Нет, операции масштабирования с использованием API-интерфейсов масштабируемого набора виртуальных машин не поддерживаются. Используйте API-интерфейсы AKS`az aks scale`().

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Можно ли использовать масштабируемые наборы виртуальных машин для ручного масштабирования в 0 узлов?

Нет, операции масштабирования с использованием API-интерфейсов масштабируемого набора виртуальных машин не поддерживаются.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Можно ли отменить или отменить выделение всех виртуальных машин?

Хотя AKS обладает механизмами устойчивости для защиты таких конфигураций и восстановления из нее, это не рекомендуемая конфигурация.

## <a name="can-i-use-custom-vm-extensions"></a>Можно ли использовать пользовательские расширения виртуальной машины?

AKS не является управляемой службой, и управление ресурсами IaaS не поддерживается. Установка пользовательских компонентов и т. д. Используйте API-интерфейсы и механизмы kubernetes. Например, используйте Daemonset для установки необходимых компонентов.

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
