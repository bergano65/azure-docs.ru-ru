---
title: Часто задаваемые вопросы для Azure Red Hat OpenShift
description: Вот ответы на распространенные вопросы о Microsoft Azure Red Hat OpenShift
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: f468cb294d79c44f92ef95437c0d88639a78b9a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77619500"
---
# <a name="azure-red-hat-openshift-faq"></a>Лазурная красная шляпа OpenShift часто задаваемые вопросы

В этой статье часто рассматриваются часто задаваемые вопросы (часто задаваемые вопросы) о Microsoft Azure Red Hat OpenShift.

## <a name="which-azure-regions-are-supported"></a>Какие регионы Azure поддерживаются?

Просматривайте [поддерживаемые ресурсы](supported-resources.md#azure-regions) для списка глобальных регионов, где поддерживается Azure Red Hat OpenShift.

## <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>Можно ли развернуть кластер в существующую виртуальную сеть?

Нет. Но можно подключить кластер Azure Red Hat OpenShift к существующему VNET с помощью пиринга. Подробнее о том, как [провести виртуальную сеть кластера, можно](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) узнать.

## <a name="what-cluster-operations-are-available"></a>Какие кластерные операции доступны?

Можно только увеличить или сократить количество вычислительных узлов. Никакие другие изменения не `Microsoft.ContainerService/openShiftManagedClusters` допускаются к ресурсу после создания. Максимальное количество вычислительных узлов ограничено 20.

## <a name="what-virtual-machine-sizes-can-i-use"></a>Какие виртуальные размеры машины я могу использовать?

Смотрите [размеры виртуальных машин Azure Red Hat OpenShift](supported-resources.md#virtual-machine-sizes) для списка виртуальных размеров машины, которые можно использовать с кластером Azure Red Hat OpenShift.

## <a name="is-data-on-my-cluster-encrypted"></a>Зашифрованы ли данные о моем кластере?

По умолчанию шифрование находится в состоянии покоя. Платформа Хранения Azure автоматически шифрует ваши данные, прежде чем сохранять их, и расшифровывает данные перед поиском. Подробнее о данных, наиболее подробно йогами читайте в материале [«Служба хранения данных».](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)

## <a name="can-i-use-prometheusgrafana-to-monitor-my-applications"></a>Могу ли я использовать Prometheus/Grafana для мониторинга своих приложений?

Да, вы можете развернуть Prometheus в пространстве имен и отслеживать приложения в пространстве имен.

## <a name="can-i-use-prometheusgrafana-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Могу ли я использовать Prometheus/Grafana для мониторинга метрик, связанных с здоровьем кластеров и емкостью?

Нет, не в настоящее время.

## <a name="is-the-docker-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Является ли реестр Docker доступным внешне, чтобы я мог использовать такие инструменты, как Дженкинс?

Реестр Docker доступен `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` с Тем не менее, сильная гарантия долговечности хранилища не предоставляется. Вы также можете использовать [реестр контейнеров Azure.](https://azure.microsoft.com/services/container-registry/)

## <a name="is-cross-namespace-networking-supported"></a>Поддерживается ли сеть кросс-имен?

Клиентии и индивидуальные админы проекта могут настраивать кросс-именные сети (включая отрицание) на основе проекта с использованием `NetworkPolicy` объектов.

## <a name="can-an-admin-manage-users-and-quotas"></a>Может ли админ управлять пользователями и квотами?

Да. Администратор Azure Red Hat OpenShift может управлять пользователями и квотами в дополнение к доступу ко всем созданным пользователями проектам.

## <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>Могу ли я ограничить кластер только определенными пользователями Azure AD?

Да. Можно ограничить, в каком доступе могут войти пользователи Azure AD в кластер, нанастройки приложения Azure AD. Для получения подробной информации см.: [Как ограничить приложение набором пользователей](https://docs.microsoft.com/azure/active-directory/develop/howto-restrict-your-app-to-a-set-of-users)

## <a name="can-i-restrict-users-from-creating-projects"></a>Могу ли я ограничить пользователей от создания проектов?

Да. Войдите в кластер в качестве администратора Azure Red Hat OpenShift и выполняйте эту команду:

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

Для получения дополнительной информации см. [disabling self-provisioning](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)

## <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>Может ли кластер иметь вычислительные узлы в нескольких регионах Azure?

Нет. Все узлы в кластере Azure Red Hat OpenShift должны происходить из одного региона Azure.

## <a name="are-master-and-infrastructure-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>Астративаются ли узлы мастер и инфраструктуры, как это происходит с Azure Kubernetes Service (AKS)?

Нет. Все ресурсы, включая мастер кластера, работают в подписке клиента. Эти типы ресурсов помещаются в группу ресурсов только для чтения.

## <a name="is-open-service-broker-for-azure-osba-supported"></a>Поддерживается ли открытый сервисный брокер для Azure (OSBA)?

Да. Вы можете использовать OSBA с Azure Red Hat OpenShift. Для получения дополнительной информации можно ознакомиться с информацией по широкому представлению [брокера Open Service Broker для Azure.](https://github.com/Azure/open-service-broker-azure#openshift-project-template)

## <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>Я пытаюсь заглянуть в виртуальную сеть `Failed to get vnet CIDR` в другой подписке, но получать ошибку.

В подписке, которая имеет виртуальную `Microsoft.ContainerService` сеть, убедитесь, что зарегистрировать провайдера с`az provider register -n Microsoft.ContainerService --wait` 

## <a name="what-is-the-azure-red-hat-openshift-aro-maintenance-process"></a>Что такое процесс обслуживания Azure Red Hat OpenShift (ARO)?

Существует три типа обслуживания ARO: обновление, резервное копирование и восстановление данных и т.д., а также обслуживание поставщика облачных услуг.

+ Обновления включают обновление программного обеспечения и CVEs. Исправление CVE происходит при `yum update` запуске и обеспечивает немедленное смягчение.  Параллельно будет создана новая сборка изображений для создания будущих кластеров.

+ Резервное копирование и управление etcd данными — это автоматизированный процесс, который может потребовать простоя кластера в зависимости от действия. Если база данных и т.д. восстанавливается из резервного копирования, время простоя будет происходить. Мы резервное копирование и т.д. ежечасно и сохранить последние 6 часов резервного копирования.

+ Обслуживание, инициированное поставщиком облачных услуг, включает в себя систему сетей, систем хранения данных и региональные сбои. Обслуживание зависит от поставщика облачных услуг и зависит от обновлений, предоставляемых поставщиком.

## <a name="what-is-the-general-upgrade-process"></a>Что такое общий процесс обновления?

Запуск обновления должен быть безопасным процессом для запуска и не должен нарушать кластерные службы. SRE может инициировать процесс обновления, когда новые версии доступны или CVEs являются выдающимися.

Доступные обновления тестируются в сценической среде, а затем применяются к производственным кластерам. При применении временно добавляется новый узлы и узлы обновляются вращающимся способом, чтобы стручки поддерживали количество реплик. Следоражая за передовым опытом, обеспечивает минимальное время простоя.

В зависимости от серьезности ожидающего обновления или обновления процесс может отличаться в том, что обновления могут быть быстро применены для смягчения воздействия службы на CVE. Новое изображение будет построено асинхронно, протестировано и развернуто в виде обновления кластера. Помимо этого, нет никакой разницы между чрезвычайным и плановым обслуживанием. Запланированное техническое обслуживание не запланировано с клиентом.

Уведомления могут быть отправлены через ICM и по электронной почте, если требуется сообщение с клиентом.

## <a name="what-about-emergency-vs-planned-maintenance-windows"></a>Как насчет аварийной и запланированной технической службы окна?

Мы не проводим различия между двумя типами обслуживания. Наши команды доступны 24/7/365 и не используют традиционные запланированные "нерабочие" окна обслуживания.

## <a name="how-will-host-operating-system-and-openshift-software-be-updated"></a>Как будет обновляться операционная система и программное обеспечение OpenShift?

Операционная система хоста и программное обеспечение OpenShift обновляются в рамках нашего общего процесса обновления и создания изображений.

## <a name="whats-the-process-to-reboot-the-updated-node"></a>Каков процесс перезагрузки обновленного узла?

Это должно быть обработано как часть обновления.

## <a name="is-data-stored-in-etcd-encrypted-on-aro"></a>Хранятся ли данные в и т.д. зашифрованных на ARO?

Он не зашифрован на уровне etcd. Возможность включить его в настоящее время не поддерживается. OpenShift поддерживает эту функцию, но для того, чтобы сделать ее на «дорожной карте», требуются инженерные усилия. Данные шифруются на уровне диска. Для получения дополнительной информации обратитесь к [шифрованию данных в Datastore Layer.](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html)

## <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>Могут ли журналы базовых ВМ передаваться в систему анализа журналов клиентов?

Syslog, журналы докеров, журнал и dmesg обрабатываются управляемой службой и не подвергаются воздействию клиентов.

## <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-aro-cluster"></a>Как клиент может получить доступ к таким показателям, как процессор/память на уровне узла, чтобы принять меры по масштабированию, отладке проблем и т.д. Я не могу `kubectl top` работать на кластере ARO.

Клиенты могут получить доступ к метрикам процессора/памяти на уровне узла, используя команду `oc adm top nodes` или `kubectl top nodes` с кластером клиента-админа.  Клиенты также могут получить доступ к `pods` метрикам CPU/Memory с командой `oc adm top pods` или`kubectl top pods`

## <a name="what-is-the-default-pod-scheduler-configuration-for-aro"></a>Что такое конфигурация планировщика стручка по умолчанию для ARO?

ARO использует планировщик по умолчанию, который поставляется в OpenShift. Есть несколько дополнительных механизмов, которые не поддерживаются в ARO. Для получения более подробной информации обратитесь к [документации планировщика по умолчанию](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/scheduler.html#generic-scheduler) и [документации генерального планировщика.](https://github.com/openshift/openshift-azure/blob/master/pkg/startup/v6/data/master/etc/origin/master/scheduler.json)

Расширенное/Таможенное планирование в настоящее время не поддерживается. Для получения более подробной информации обратитесь к [документации о планировании.](https://docs.openshift.com/container-platform/3.11/admin_guide/scheduling/index.html)

## <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>Если мы масштабировать развертывание, как карта доменов дефектов Azure в размещение стручков, чтобы гарантировать, что все стручки для службы не будут выбиты из-за сбоя в одном домене неисправности?

По умолчанию при использовании виртуальных наборов масштабов машин в Azure по умолчанию имеется пять доменов неисправностей. Каждый виртуальный экземпляр машины в наборе масштабов будет помещен в один из этих доменов неисправности. Это гарантирует, что приложения, развернутые в вычислительных узлах в кластере, будут помещены в отдельные домены неисправностей.

Обратитесь к [Выбору правильного количества доменов неисправностей для виртуальной шкалы машин, установленных](https://docs.microsoft.com//azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains) для более подробной информации.

## <a name="is-there-a-way-to-manage-pod-placement"></a>Есть ли способ управлять размещением стручка?

Клиенты имеют возможность получать узлы и просматривать метки в качестве клиента-админа.  Это позволит настроить таргетинг на любой VM в наборе масштабов.

При использовании определенных меток необходимо соблюдать осторожность:

- Хостимя не должна использоваться. Hostname часто вращается с обновлениями и обновлениями и гарантированно изменяется.

- Если у клиента есть запрос на конкретные метки или стратегию развертывания, это может быть выполнено, но потребует инженерных усилий и не поддерживается сегодня.

## <a name="what-is-the-maximum-number-of-pods-in-an-aro-cluster-what-is-the-maximum-number-of-pods-per-node-in-aro"></a>Каково максимальное количество стручков в кластере ARO?Каково максимальное количество стручков на узла в ARO?

 Azure Red Hat OpenShift 3.11 имеет 50-стручковый предел на узел с [ARO, имеющим лимит 20-вычисляемых узлов,](https://docs.microsoft.com/azure/openshift/openshift-faq#what-cluster-operations-are-available)так что крышки максимальное количество стручков поддерживается в кластере ARO до 50'20 и 1000.

## <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>Можем ли мы указать диапазоны IP для развертывания на частном VNET, избегая столкновений с другими корпоративными VNETs один раз заглянул?

Azure Red Hat OpenShift поддерживает вращу VNET и позволяет клиенту предоставить VNET для работы и VNET CIDR, в котором будет работать сеть OpenShift.

Созданный ARO VNET будет защищен и не будет принимать изменения конфигурации. VNET, который заглянул контролируется клиентом и находится в его подписке.

## <a name="does-the-cluster-reside-in-a-customer-subscription"></a>Проживает ли кластер в подписке клиента? 

Управляемое приложение Azure живет в заблокированной группе ресурсов с подпиской клиента. Клиент может просматривать объекты в этой RG, но не изменять.

## <a name="is-the-sdn-module-configurable"></a>Настраиваемый ли модуль SDN?

SDN является openshift-ovs-networkpolicy и не настраивается.

## <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>Какие права UNIX (в IaaS) доступны для мастеров / Инфра / App узлов?

Не применимо к этому предложению. Доступ к узлам запрещен.

## <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>Какие у нас есть права OCP? Кластерный админ? Руководитель проекта?

Для получения подробной информации смотрите [обзор администрирования кластера](https://docs.openshift.com/aro/admin_guide/index.html)Azure Red Hat OpenShift.

## <a name="which-kind-of-federation-with-ldap"></a>Какая федерация с LDAP?

Это будет достигнуто с помощью интеграции Azure AD. 

## <a name="is-there-any-element-in-aro-shared-with-other-customers-or-is-everything-independent"></a>Есть ли элемент в ARO совместно с другими клиентами? Или все независимо?

Каждый кластер Azure Red Hat OpenShift предназначен для данного клиента и живет в рамках подписки клиента. 

## <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>Можем ли мы выбрать какое-либо постоянное решение для хранения данных, например OCS? 

Для выбора можно выбрать два класса хранения: Azure Disk и Azure File.

## <a name="how-is-a-cluster-updated-including-majors-and-minors-due-to-vulnerabilities"></a>Как обновляется кластер (включая специальности и несовершеннолетних из-за уязвимостей)?

Смотрите, [что такое общий процесс обновления?](https://docs.microsoft.com/azure/openshift/openshift-faq#what-is-the-general-upgrade-process)

## <a name="what-azure-load-balancer-is-used-by-aro-is-it-standard-or-basic-and-is-it-configurable"></a>Какой балансизатор Azure Load используется ARO?Является ли он стандартным или основным и является ли он настраиваемым?

ARO использует Standard Azure Load Balancer, и он не настраивается.

## <a name="can-aro-use-netapp-based-storage"></a>Может ли ARO использовать хранилище на основе NetApp?

На данный момент единственными поддерживаемыми вариантами хранения являются классы хранения Azure Disk и Azure File. 


