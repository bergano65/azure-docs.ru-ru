---
title: Ограничения узлов Windows Server
titleSuffix: Azure Kubernetes Service
description: Узнайте об известных ограничениях при запуске пулов узлов Windows Server и рабочих нагрузок приложений в службе Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 12/18/2019
ms.openlocfilehash: 934acf06a779c1c3b0b13e74b196b174dd944e66
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886676"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Текущие ограничения для пулов узлов Windows Server и рабочих нагрузок приложений в службе Azure Kubernetes (AKS)

В службе Azure Kubernetes (AKS) можно создать пул узлов, который запускает Windows Server в качестве гостевой ОС на узлах. Эти узлы могут запускать нативные приложения для контейнеров Windows, например, встроенные в рамочку .NET. Поскольку существуют значительные различия в том, как Linux и Windows OS обеспечивают поддержку контейнеров, некоторые общие функции Kubernetes и pod- related в настоящее время не доступны для пулов узлов Windows.

В этой статье излагаются некоторые ограничения и концепции ОС для узлов Windows Server в AKS. Пулы узлов для Windows Server в настоящее время находятся в предварительном просмотре.

> [!IMPORTANT]
> Функции предварительного просмотра AKS являются отказом от самообслуживания. Предварительные просмотры предоставляются "как есть" и "по мере возможности" и исключаются из соглашений об уровне обслуживания и ограниченной гарантии. Анонсы AKS частично покрываются поддержкой клиентов на основе наилучших усилий. Таким образом, эти функции не предназначены для использования в производстве. Для получения дополнительной информации, пожалуйста, смотрите следующие статьи поддержки:
>
> * [Политики поддержки AKS][aks-support-policies]
> * [Часто задаваемые вопросы о поддержке Azure][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>Какие операционные системы Windows поддерживаются?

AKS использует Windows Server 2019 в качестве версии хост-ОС и поддерживает только изоляцию процессов. Контейнерные изображения, созданные с использованием других версий Windows Server, не поддерживаются. [Совместимость контейнерной версии Windows][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Отличается ли Kubernetes от Windows и Linux?

Поддержка пула узла Window Server включает в себя некоторые ограничения, которые являются частью восходящего Windows Server в проекте Kubernetes. Эти ограничения не являются специфическими для AKS. Более подробную информацию об этой восходящей поддержке Windows Server в Kubernetes можно узнать в разделе [Поддерживаемая функциональность и ограничения][upstream-limitations] поддержки Intro to Windows в документе [Kubernetes][intro-windows] из проекта Kubernetes.

Kubernetes исторически Linux-ориентированной. Многие примеры, используемые в веб-сайте [Kubernetes.io,][kubernetes] предназначены для использования на узлах Linux. При создании развертываний, в которые используются контейнеры Windows Server, применяются следующие соображения на уровне ОС:

- **Identity** - Linux идентифицирует пользователя по идентификатору пользователя (UID). Пользователь также имеет имя буквеннумерического пользователя для входа в систему, которое Linux переводится как UID пользователя. Аналогичным образом Linux идентифицирует группу пользователей по идентификатору группы integer (GID) и переводит название группы на соответствующее GID.
    - Windows Server использует более крупный двоичный идентификатор безопасности (SID), который хранится в базе данных Менеджера доступа Windows (SAM). Эта база данных не передается между узлами и контейнерами или между контейнерами.
- **Разрешения файлов** - Windows Server использует список управления доступом на основе SID, а не бит-маску разрешений и UID-GID
- **Пути файлов** - конвенция на Windows Server заключается в использовании вместо /.
    - В спецификациях стручка, которые устанавливают объемы, укажите траекторию правильно для контейнеров Windows Server. Например, вместо точки крепления */mnt/volume* в контейнере Linux укажите букву диска и место, такое как */K/Volume* для установки в качестве *диска K:* drive.

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Какие диски поддерживаются для Windows?

Azure Disks и Azure Files — это поддерживаемые типы громкости, доступ к которые используются в виде томов NTFS в контейнере Windows Server.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Можно ли запускать только кластеры Windows в AKS?

Мастер-узлы (плоскость управления) в кластере AKS размещаются AKS службой, вы не будете подвергаться воздействию операционной системы узлов, принимающих мастер-компоненты. Весь кластер AKS создается с пулом первого узла по умолчанию, основанным на Linux. Этот пул узлов содержит системные службы, которые необходимы для функционирования кластера. Рекомендуется запускать по крайней мере два узла в первом пуле узлов, чтобы обеспечить надежность кластера и возможность выполнения кластерных операций. Первый пул узлов на основе Linux не может быть удален, если не будет удален сам кластер AKS.

## <a name="what-network-plug-ins-are-supported"></a>Какие сетевые плагины поддерживаются?

Кластеры AKS с пулами узлов Windows должны использовать сетевую модель Azure CNI (продвинутая). Сеть Kubenet (основная) не поддерживается. Для получения дополнительной информации о различиях в сетевых [моделях, см.][azure-network-models] - Модель сети Azure CNI требует дополнительного планирования и рассмотрения управления IP-адресами. Для получения дополнительной информации о том, как планировать и реализовывать Azure CNI, можно [узнать в сети Azure CNI в AKS.][configure-azure-cni]

## <a name="can-i-change-the-max--of-pods-per-node"></a>Могу я изменить макс. - стручков на узла?

Да. Для последствий и вариантов, которые доступны, [см.][maximum-number-of-pods]

## <a name="how-do-patch-my-windows-nodes"></a>Как патч мои узлы Windows?

Узлы Windows Server в AKS должны быть *обновлены,* чтобы получить последние исправления и обновления патчей. Обновления Windows не включены на узлах в AKS. AKS выпускает новые изображения пула узлов, как только патчи доступны, это ответственность клиентов для обновления узлов бассейны, чтобы оставаться в курсе на патчи и hotfix. Это также верно для используемой версии Kubernetes. Примечания к выпуску AKS будут указывать, когда появляются новые версии. Для получения дополнительной информации об обновлении пула узлов Windows Server [см.][nodepool-upgrade]

> [!NOTE]
> Обновленное изображение Windows Server будет использоваться только в том случае, если обновление кластера (обновление плоскости управления) было выполнено до обновления пула узлов
>

## <a name="how-do-i-rotate-the-service-principal-for-my-windows-node-pool"></a>Как повернуть основной сервис для пула узлов Windows?

Во время предварительного просмотра пулы узлов Windows не поддерживают основное вращение службы в качестве ограничения предварительного просмотра. Чтобы обновить основной сервис, создайте новый пул узлов Windows и перевизьте ваши стручки из старого пула в новый. Как только это будет завершено, удалите старый пул узлов.

## <a name="how-many-node-pools-can-i-create"></a>Сколько пулов узлов можно создать?

Кластер AKS может иметь максимум 10 пулов узлов. Вы можете иметь максимум 1000 узлов через эти пулы узлов. [Ограничения пула узлов][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Как назвать пулы узлов Windows?

Вы должны сохранить имя максимум 6 (шесть) символов. Это текущее ограничение AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Все ли функции поддерживаются узлами Windows?

Сетевые политики и kubenet в настоящее время не поддерживаются узлами Windows. 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Могу ли я запустить контроллеры входа на узлах Windows?

Да, ingress-контроллер, который поддерживает контейнеры Windows Server, может работать на узлах Windows в AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Можно ли использовать пространства Azure Dev с узлами Windows?

Пространства Azure Dev в настоящее время доступны только для пулов узлов на основе Linux.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Могут ли контейнеры Windows Server использовать gMSA?

Поддержка управляемых групповых сервисных учетных записей (gMSA) в настоящее время недоступна в AKS.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Можно ли использовать Azure Monitor для контейнеров с узлами Windows и контейнерами?

Да, вы можете, однако Azure Monitor не собирает журналы (stdout) из контейнеров Windows. Вы все еще можете прикрепить к живой поток уступчивых журналов из контейнера Windows.

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>Что делать, если мне нужна функция, которая не поддерживается?

Мы прилагаем все необходимые функции к Windows в AKS, но если вы столкнетесь с пробелами, проект с открытым исходным кодом, [восходящий акс-двигатель][aks-engine] обеспечивает простой и полностью настраиваемый способ работы Kubernetes в Azure, включая поддержку Windows. Пожалуйста, не забудьте проверить нашу дорожную карту функций ближайшие [AKS дорожной карты][aks-roadmap].

## <a name="next-steps"></a>Дальнейшие действия

Чтобы начать работу с контейнерами Windows Server в AKS, [создайте пул узлов, который запускает Windows Server в AKS.][windows-node-cli]

<!-- LINKS - external -->
[kubernetes]: https://kubernetes.io
[aks-engine]: https://github.com/azure/aks-engine
[upstream-limitations]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/#supported-functionality-and-limitations
[intro-windows]: https://kubernetes.io/docs/setup/production-environment/windows/intro-windows-in-kubernetes/
[aks-roadmap]: https://github.com/Azure/AKS/projects/1

<!-- LINKS - internal -->
[azure-network-models]: concepts-network.md#azure-virtual-networks
[configure-azure-cni]: configure-azure-cni.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[windows-node-cli]: windows-container-cli.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-outbound-traffic]: ../load-balancer/load-balancer-outbound-connections.md#defaultsnat
[nodepool-limitations]: use-multiple-node-pools.md#limitations
[preview-support]: support-policies.md#preview-features-or-feature-flags
[windows-container-compat]: /virtualization/windowscontainers/deploy-containers/version-compatibility?tabs=windows-server-2019%2Cwindows-10-1909
[maximum-number-of-pods]: configure-azure-cni.md#maximum-pods-per-node
