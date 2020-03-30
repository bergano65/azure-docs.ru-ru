---
title: Вопросы и ответы о Службе Azure Kubernetes (AKS)
description: Найдите ответы на некоторые из распространенных вопросов о сервисе Azure Kubernetes (AKS).
ms.topic: conceptual
ms.date: 10/02/2019
ms.openlocfilehash: c4bb4328af5df7f729967c7b249847b2ab098770
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79497768"
---
# <a name="frequently-asked-questions-about-azure-kubernetes-service-aks"></a>Вопросы и ответы о Службе Azure Kubernetes (AKS)

В этой статье представлены ответы на часто задаваемые вопросы о Службе Azure Kubernetes.

## <a name="which-azure-regions-currently-provide-aks"></a>Какие регионы Azure в настоящее время предоставляют АКС?

Полный список доступных регионов можно найти в [регионах и доступности AKS.][aks-regions]

## <a name="does-aks-support-node-autoscaling"></a>Поддерживает ли AKS автомасштабирование узла?

Да, возможность автоматического масштабирования узлов агента горизонтально в AKS в настоящее время доступна в предварительном просмотре. [См. Автоматически масштабировать кластер для удовлетворения требований приложений в AKS][aks-cluster-autoscaler] для инструкций. Автомасштабирование AKS основано на [автоскалаторе Kubernetes.][auto-scaler]

## <a name="can-i-deploy-aks-into-my-existing-virtual-network"></a>Можно ли развернуть AKS в имеющейся виртуальной сети?

Да, вы можете развернуть кластер AKS в существующую виртуальную сеть с помощью [расширенной сетевой функции.][aks-advanced-networking]

## <a name="can-i-limit-who-has-access-to-the-kubernetes-api-server"></a>Могу ли я ограничить доступ к серверу API Kubernetes?

Да, вы можете ограничить доступ к серверу API Kubernetes с помощью [авторизованных IP-диапазонов API Server.][api-server-authorized-ip-ranges]

## <a name="can-i-make-the-kubernetes-api-server-accessible-only-within-my-virtual-network"></a>Могу ли я сделать сервер Kubernetes API доступным только в моей виртуальной сети?

Не в это время, но это планируется. Вы можете отслеживать прогресс на [РЕПО AKS GitHub.][private-clusters-github-issue]

## <a name="can-i-have-different-vm-sizes-in-a-single-cluster"></a>Могу ли я иметь различные размеры VM в одном кластере?

Да, вы можете использовать различные виртуальные размеры машины в кластере AKS, создавая [несколько пулов узлов.][multi-node-pools]

## <a name="are-security-updates-applied-to-aks-agent-nodes"></a>Применяются ли обновления для системы безопасности к узлам агентов AKS?

Azure автоматически применяет патчи безопасности к узлам Linux в кластере в ночном расписании. Тем не менее, вы несете ответственность за то, чтобы эти узлы Linux были перезагружены по мере необходимости. У вас есть несколько вариантов перезагрузки узлов:

- Вручную на портале Azure или Azure CLI.
- Обновив кластер AKS. Кластер автоматически обновляет [кордон и дренажные узлы,][cordon-drain] а затем вывешивает новый узла в режиме онлайн с последним изображением Ubuntu и новой версией патча или незначительной версией Kubernetes. Дополнительные сведения см. в статье [Обновление кластера службы Azure Kubernetes (AKS)][aks-upgrade].
- С помощью [Kured](https://github.com/weaveworks/kured), с открытым исходным кодом перезагрузки daemon для Kubernetes. Kured работает как [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) и отслеживает каждый узло на наличие файла, который указывает на необходимость перезагрузки. По всему кластеру перезагрузка ОС управляется тем же [процессом оцепления и утечки,][cordon-drain] что и обновление кластера.

Дополнительные сведения об использовании kured см. в статье о [применении обновлений безопасности и ядра на узлах в AKS][node-updates-kured].

### <a name="windows-server-nodes"></a>Узлы Сервера Windows

Для узлов Windows Server (в настоящее время в предварительном просмотре в AKS) обновление Windows не запускается автоматически и не применяет последние обновления. В обычном графике вокруг цикла выпуска обновления Windows и собственного процесса проверки следует выполнить обновление кластера и пула узлов Windows Server (ы) в кластере AKS. Этот процесс обновления создает узлы, которые работают последние изображения Windows Server и патчи, а затем удаляет старые узлы. Для получения дополнительной информации об этом процессе [см.][nodepool-upgrade]

## <a name="why-are-two-resource-groups-created-with-aks"></a>Почему с AKS создаются две группы ресурсов?

AKS опирается на ряд инфраструктурных ресурсов Azure, включая виртуальные наборы машин, виртуальные сети и управляемые диски. Это позволяет использовать многие основные возможности платформы Azure в управляемой среде Kubernetes, предоставляемой AKS. Например, большинство типов виртуальных машин Azure могут использоваться непосредственно с помощью AKS, а резервирование Azure может использоваться для автоматического получения скидок на эти ресурсы.

Для включения этой архитектуры развертывание AKS охватывает две группы ресурсов:

1. Создается первая группа ресурсов. Эта группа содержит только ресурс службы Kubernetes. Поставщик ресурсов AKS автоматически создает вторую группу ресурсов во время развертывания. Примером второй группы ресурсов является *MC_myResourceGroup_myAKSCluster_eastus*. Для получения информации о том, как указать имя этой второй группы ресурсов, см.
1. Вторая группа ресурсов, известная как *группа ресурсов узлов,* содержит все ресурсы инфраструктуры, связанные с кластером. Эти ресурсы включают виртуальные машины узла Kubernetes, виртуальную сеть и хранилище. По умолчанию группа ресурсов узлов имеет имя, *как MC_myResourceGroup_myAKSCluster_eastus*. AKS автоматически удаляет ресурс узла всякий раз, когда кластер удаляется, поэтому его следует использовать только для ресурсов, которые разделяют жизненный цикл кластера.

## <a name="can-i-provide-my-own-name-for-the-aks-node-resource-group"></a>Могу ли я уведомлять свое имя для группы ресурсов узлов akS?

Да. По умолчанию AKS назовет группу ресурсов узлов *MC_resourcegroupname_clustername_location,* но вы также можете укажите свое собственное имя.

Чтобы указать имя группы ресурсов, установите версию расширения Azure CLI [aks-preview][aks-preview-cli] *0.3.2* или позже. При создании кластера AKS с помощью [команды az aks][az-aks-create] используйте параметр *--узел-ресурс-группа* и укажите имя для группы ресурсов. Если для развертывания кластера AKS [используется шаблон менеджера ресурсов Azure,][aks-rm-template] можно определить имя группы ресурсов с помощью свойства *nodeResourceGroup.*

* Второстепенная группа ресурсов автоматически создается поставщиком ресурсов Azure в собственной подписке.
* Имя группы пользовательских ресурсов можно указать только при создании кластера.

Работая с группой ресурсов узлов, имейте в виду, что вы не можете:

* Укажите существующую группу ресурсов для группы ресурсов узлов.
* Укажите другую подписку для группы ресурсов узлов.
* Измените имя группы ресурсов узла после создания кластера.
* Укажите имена для управляемых ресурсов в группе ресурсов узлов.
* Изменение или удаление тегов управляемых ресурсов в группе ресурсов узлов. (См. дополнительную информацию в следующем разделе.)

## <a name="can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group"></a>Могу ли я изменить теги и другие свойства ресурсов AKS в группе ресурсов узлов?

При изменении или удалении созданных Azure тегов и других свойств ресурсов в группе ресурсов узлов можно получить неожиданные результаты, такие как масштабирование и обновление ошибок. AKS позволяет создавать и изменять пользовательские теги. Возможно, необходимо создать или изменить пользовательские теги, например, для присвоения бизнес-единицы или центра затрат. Изменяя ресурсы в группе ресурсов узлов в кластере AKS, вы нарушаете цель уровня обслуживания (SLO). Для получения дополнительной [Does AKS offer a service-level agreement?](#does-aks-offer-a-service-level-agreement) информации см.

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

В настоящее время вы не можете изменить список контроллеров приема в AKS.

## <a name="is-azure-key-vault-integrated-with-aks"></a>Интегрируется ли Azure Key Vault с AKS?

AkS в настоящее время не интегрирована с Azure Key Vault. Тем не менее, [проект Azure Key Vault FlexVolume для Kubernetes][keyvault-flexvolume] позволяет осуществлять прямую интеграцию из стручков Kubernetes в секреты Key Vault.

## <a name="can-i-run-windows-server-containers-on-aks"></a>Можно ли запускать контейнеры Windows Server в AKS?

Да, контейнеры Windows Server доступны в предварительном просмотре. Для запуска контейнеров Windows Server в AKS создается пул узлов, который запускает Windows Server в качестве гостевой ОС. Контейнеры Windows Server могут использовать только Windows Server 2019. Для начала [см. Создайте кластер AKS с пулом узлов Windows Server.][aks-windows-cli]

Поддержка пула узлов Windows Server включает в себя некоторые ограничения, которые являются частью проекта Windows Server в Kubernetes. Для получения дополнительной информации [Windows Server containers in AKS limitations][aks-windows-limitations]об этих ограничениях см.

## <a name="does-aks-offer-a-service-level-agreement"></a>Предлагает ли AKS соглашение на уровне обслуживания?

В соглашении об уровне обслуживания (SLA) поставщик соглашается возместить клиенту стоимость услуги, если опубликованный уровень обслуживания не будет выполнен. Так как AKS является бесплатным, нет никаких затрат для возмещения, так что AKS не имеет официального SLA. Тем не менее, AKS стремится сохранить доступность по крайней мере 99,5 процента для сервера API Kubernetes.

Важно распознать различие между доступностью службы AKS, которая относится к времени простоя плоскости управления Kubernetes, и наличием вашей конкретной рабочей нагрузки, которая работает на виртуальных машинах Azure. Хотя плоскость управления может быть недоступна, если плоскость управления не готова, рабочие нагрузки кластера, работающие на MMs Azure, все равно могут функционировать. Учитывая, что VMs-м вмхозанным ИТ-отделам выплачиваются, они опираются на финансовые SLA. Узнайте [здесь для получения более подробной информации](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) о Azure VM SLA и о том, как увеличить эту доступность с помощью таких функций, как [зоны доступности.][availability-zones]

## <a name="why-cant-i-set-maxpods-below-30"></a>Почему я не могу установить maxPods ниже 30?

В AKS можно установить `maxPods` значение при создании кластера, используя шаблоны Azure CLI и azure Resource Manager. Однако и Kubenet, и Azure CNI требуют *минимального значения* (подтвержденного во время создания):

| Сети | Минимальные | Максимальная |
| -- | :--: | :--: |
| Лазурный CNI | 30 | 250 |
| Кубенет | 30 | 110 |

Поскольку AKS является управляемой службой, мы развертываем и управляем надстройками и стручками как часть кластера. В прошлом пользователи могли `maxPods` определить значение ниже, чем значение, которое требуется для запуска управляемых стручков (например, 30). ТЕПЕРь AKS вычисляет минимальное количество стручков с помощью этой формулы: ((maxPods или (maxPods - vm_count)) > управляемые стручки с добавлением минимум.

Пользователи не могут переопределить минимальную `maxPods` проверку.

## <a name="can-i-apply-azure-reservation-discounts-to-my-aks-agent-nodes"></a>Могу ли я применить скидки на бронирование Azure в своих узлах агента AKS?

Агентские узлы AKS выставляются как стандартные виртуальные машины Azure, поэтому, если вы приобрели [резервирование Azure][reservation-discounts] для размера VM, которое вы используете в AKS, эти скидки автоматически применяются.

## <a name="can-i-movemigrate-my-cluster-between-azure-tenants"></a>Могу ли я перемещать/мигрировать кластер между арендаторами Azure?

Команда `az aks update-credentials` может использоваться для перемещения кластера AKS между арендаторами Azure. Следуйте инструкциям в [Выберите обновить или создать основной службы,](https://docs.microsoft.com/azure/aks/update-credentials) а затем [обновить кластер aks с новыми учетными данными.](https://docs.microsoft.com/azure/aks/update-credentials#update-aks-cluster-with-new-service-principal-credentials)

## <a name="can-i-movemigrate-my-cluster-between-subscriptions"></a>Могу ли я перемещать/мигрировать кластер между подписками?

Движение кластеров между подписками в настоящее время не поддерживается.

## <a name="can-i-move-my-aks-clusters-from-the-current-azure-subscription-to-another"></a>Могу ли я переместить кластеры AKS из текущей подписки azure в другую? 

Перемещение кластера AKS и связанных с ним ресурсов между подписками Azure не поддерживается.

## <a name="why-is-my-cluster-delete-taking-so-long"></a>Почему удаление кластера занимает так много времени? 

Большинство кластеров удаляются по запросу пользователя; в некоторых случаях, особенно в тех случаях, когда клиенты приносят свою собственную группу ресурсов или выполняют удаление задач cross-RG, может занять дополнительное время или сбой. Если у вас есть проблема с удалениями, перепроверьте, что у вас нет блокировок на RG, что любые ресурсы за пределами RG отмежеваются от RG и т.д.

## <a name="if-i-have-pod--deployments-in-state-nodelost-or-unknown-can-i-still-upgrade-my-cluster"></a>Если у меня есть стручок / развертывание в состоянии "NodeLost" или "Неизвестный", могу ли я все еще обновить свой кластер?

Вы можете, но AKS не рекомендует этого. Обновления в идеале должны выполняться, когда состояние кластера известно и работоспособно.

## <a name="if-i-have-a-cluster-with-one-or-more-nodes-in-an-unhealthy-state-or-shut-down-can-i-perform-an-upgrade"></a>Если у меня есть кластер с одним или несколько узлами в нездоровом состоянии или выключается, могу ли я выполнить обновление?

Нет, пожалуйста, удалите/удалите все узлы в неисправном состоянии или иным образом удалены из кластера до обновления.

## <a name="i-ran-a-cluster-delete-but-see-the-error-errno-11001-getaddrinfo-failed"></a>Я запустил кластерное удаление, но вижу ошибку`[Errno 11001] getaddrinfo failed` 

Чаще всего это происходит из-за того, что пользователи имеют одну или несколько групп сетевой безопасности (НСГ), которые все еще используются и связаны с кластером.  Пожалуйста, удалите их и повторите попытку удалить.

## <a name="i-ran-an-upgrade-but-now-my-pods-are-in-crash-loops-and-readiness-probes-fail"></a>Я запустил обновление, но теперь мои стручки находятся в аварийных циклах, и зонды готовности терпят неудачу?

Пожалуйста, подтвердите, что срок действия вашего услуги не истек.  Пожалуйста, см.: [директор службы AKS](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) и [учетные данные обновления AKS](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="my-cluster-was-working-but-suddenly-can-not-provision-loadbalancers-mount-pvcs-etc"></a>Мой кластер работал, но вдруг не может обеспечить LoadBalancers, горе ПВХ и т.д.? 

Пожалуйста, подтвердите, что срок действия вашего услуги не истек.  Пожалуйста, см.: [директор службы AKS](https://docs.microsoft.com/azure/aks/kubernetes-service-principal) и [учетные данные обновления AKS](https://docs.microsoft.com/azure/aks/update-credentials).

## <a name="can-i-use-the-virtual-machine-scale-set-apis-to-scale-manually"></a>Могу ли я использовать aI-ади и адиенции набора виртуальной шкалы машин для масштабирования вручную?

Нет, масштабирование операций с помощью apIs- системы масштабирования виртуальной машины не поддерживается. Используйте APIs AKS ().`az aks scale`

## <a name="can-i-use-virtual-machine-scale-sets-to-manually-scale-to-0-nodes"></a>Могу ли я использовать виртуальные наборы масштабов машин для ручного масштабирования до 0 узлов?

Нет, масштабирование операций с помощью apIs- системы масштабирования виртуальной машины не поддерживается.

## <a name="can-i-stop-or-de-allocate-all-my-vms"></a>Могу ли я остановить или де-распределить все мои VMs?

Хотя AKS имеет механизмы устойчивости, чтобы выдержать такую конфигурацию и оправиться от нее, это не рекомендуется конфигурации.

## <a name="can-i-use-custom-vm-extensions"></a>Могу ли я использовать пользовательские расширения VM?

Нет AKS является управляемой службой, и манипуляции с ресурсами IaaS не поддерживается. Для установки пользовательских компонентов и т.д. пожалуйста, используйте AIS и механизмы Kubernetes. Например, использовать DaemonSets для установки необходимых компонентов.

<!-- LINKS - internal -->

[aks-upgrade]: ./upgrade-cluster.md
[aks-cluster-autoscale]: ./autoscaler.md
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
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
[api-server-authorized-ip-ranges]: ./api-server-authorized-ip-ranges.md
[multi-node-pools]: ./use-multiple-node-pools.md
[availability-zones]: ./availability-zones.md

<!-- LINKS - external -->
[aks-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[auto-scaler]: https://github.com/kubernetes/autoscaler
[cordon-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[hexadite]: https://github.com/Hexadite/acs-keyvault-agent
[admission-controllers]: https://kubernetes.io/docs/reference/access-authn-authz/admission-controllers/
[keyvault-flexvolume]: https://github.com/Azure/kubernetes-keyvault-flexvol
[private-clusters-github-issue]: https://github.com/Azure/AKS/issues/948
