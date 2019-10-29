---
title: Ограничения для пулов узлов Windows Server в службе Kubernetes Azure (AKS)
description: Сведения об известных ограничениях при запуске пулов узлов и рабочих нагрузок приложений Windows Server в службе Kubernetes Azure (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: article
ms.date: 05/31/2019
ms.author: mlearned
ms.openlocfilehash: 2cec24e5ea50360531e247b37369802f3ef4e336
ms.sourcegitcommit: 92d42c04e0585a353668067910b1a6afaf07c709
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/28/2019
ms.locfileid: "72990295"
---
# <a name="current-limitations-for-windows-server-node-pools-and-application-workloads-in-azure-kubernetes-service-aks"></a>Текущие ограничения для пулов узлов Windows Server и рабочих нагрузок приложений в службе Kubernetes Azure (AKS)

В службе Azure Kubernetes Service (AKS) можно создать пул узлов, который будет работать под управлением Windows Server в качестве гостевой ОС на узлах. Эти узлы могут запускать собственные приложения контейнеров Windows, например созданные на .NET Framework. Так как в ОС Linux и Windows предусмотрены значительные отличия в области поддержки контейнеров, некоторые распространенные Kubernetes и компоненты, связанные с Pod, в настоящее время недоступны для пулов узлов Windows.

В этой статье описаны некоторые ограничения и основные понятия ОС для узлов Windows Server в AKS. Пулы узлов для Windows Server сейчас доступны в предварительной версии.

> [!IMPORTANT]
> Функции предварительной версии AKS — это самостоятельная служба. Предварительные версии предоставляются "как есть" и "как есть" и исключаются из соглашений об уровне обслуживания и ограниченной гарантии. Предварительные версии AKS частично покрываются службой поддержки клиентов на основе лучших усилий. Таким образом, эти функции не предназначены для использования в рабочей среде. Дополнительные сведения см. в следующих статьях поддержки:
>
> * [Политики поддержки AKS][aks-support-policies]
> * [Часто задаваемые вопросы о поддержке Azure][aks-faq]

## <a name="which-windows-operating-systems-are-supported"></a>Какие операционные системы Windows поддерживаются?

AKS использует Windows Server 2019 в качестве версии ОС узла и поддерживает только изоляцию процессов. Образы контейнеров, созданные с использованием других версий Windows Server, не поддерживаются. [Совместимость версий контейнера Windows][windows-container-compat]

## <a name="is-kubernetes-different-on-windows-and-linux"></a>Отличается ли Kubernetes в Windows и Linux?

Поддержка пула узлов Window Server включает некоторые ограничения, которые входят в состав вышестоящего сервера Windows Server в Kubernetes проекте. Эти ограничения не относятся к AKS. Дополнительные сведения об этой вышестоящей поддержке Windows Server в Kubernetes см. в разделе [Поддерживаемые функции и ограничения][upstream-limitations] статьи [Введение в поддержку Windows в документе Kubernetes][intro-windows] в проекте Kubernetes.

Kubernetes является историческим, ориентированным на Linux. Многие примеры, используемые в вышестоящем веб-сайте [Kubernetes.IO][kubernetes] , предназначены для использования на узлах Linux. При создании развертываний, использующих контейнеры Windows Server, применяются следующие рекомендации на уровне операционной системы.

- **Identity** — Linux определяет пользователя с помощью целочисленного идентификатора пользователя (UID). Пользователь также имеет буквенно-цифровое имя пользователя для входа в систему, которое Linux преобразуется в UID пользователя. Аналогичным образом Linux определяет группу пользователей по идентификатору целочисленной группы (GID) и преобразует имя группы в соответствующий ему GID.
    - Windows Server использует более крупный двоичный идентификатор безопасности (SID), который хранится в базе данных диспетчера доступа безопасности Windows (SAM). Эта база данных не является общей для узла и контейнеров, а также между контейнерами.
- **Разрешения для файлов** — Windows Server использует список управления доступом на основе идентификаторов безопасности, а не битовую маску разрешений и UID + GID.
- **Пути к файлам** — соглашение в Windows Server заключается в использовании \ вместо/.
    - В спецификациях Pod, в которых подключены тома, правильно укажите путь для контейнеров Windows Server. Например, вместо точки подключения */МНТ/Волуме* в контейнере Linux укажите букву диска и расположение, например */к/Волуме* , чтобы подключить как диск *K:* .

## <a name="what-kind-of-disks-are-supported-for-windows"></a>Какие диски поддерживаются для Windows?

Диски Azure и файлы Azure — это поддерживаемые типы томов, доступ к которым осуществляется в виде томов NTFS в контейнере Windows Server.

## <a name="can-i-run-windows-only-clusters-in-aks"></a>Можно ли запускать кластеры только для Windows в AKS?

Главные узлы (плоскость управления) в кластере AKS размещаются в AKS службе, поэтому вы не будете предоставлять доступ к операционной системе узлов, на которых размещаются главные компоненты. Все кластеры AKS создаются с использованием пула первого узла по умолчанию, который основан на Linux. Этот пул узлов содержит системные службы, необходимые для работы кластера. Рекомендуется запускать по крайней мере два узла в пуле первого узла, чтобы обеспечить надежность кластера и возможность выполнять кластерные операции. Первый пул узлов на основе Linux нельзя удалить, пока не будет удален сам кластер AKS.

## <a name="what-network-plug-ins-are-supported"></a>Какие сетевые подключаемые модули поддерживаются?

Кластеры AKS с пулами узлов Windows должны использовать модель сети Azure CNI (Advanced). Кубенет (базовая) сеть не поддерживается. Дополнительные сведения о различиях в сетевых моделях см. в разделе [Основные понятия сети для приложений в AKS][azure-network-models]. — Для модели сети Azure CNI требуется дополнительное планирование и рекомендации по управлению IP-адресами. Дополнительные сведения о планировании и реализации Azure CNI см. в статье [Настройка сети CNI для Azure в AKS][configure-azure-cni].

## <a name="can-i-change-the-min--of-pods-per-node"></a>Можно ли изменить минимальное число модулей Pod на узел?

В настоящее время для обеспечения надежности кластеров необходимо установить как минимум 30 модулей.

## <a name="how-do-patch-my-windows-nodes"></a>Как исправлять узлы Windows?

Узлы Windows Server в AKS должны быть *обновлены* для получения последних исправлений и обновлений. На узлах в AKS не включены обновления Windows. AKS выпускает новые образы пула узлов, как только будут доступны исправления, клиенты обязаны обновлять пулы узлов, чтобы оставаться в курсе последних исправлений и исправлений. Это также верно для используемой версии Kubernetes. Заметки о выпуске AKS показывают, когда доступны новые версии. Дополнительные сведения об обновлении пула узлов Windows Server см. в статье [Обновление пула узлов в AKS][nodepool-upgrade].

> [!NOTE]
> Обновленный образ Windows Server будет использоваться только в том случае, если обновление кластера (обновление плоскости управления) было выполнено до обновления пула узлов.
>

## <a name="how-many-node-pools-can-i-create"></a>Сколько пулов узлов можно создать?

Кластер AKS может иметь не более восьми (8) пулов узлов. В этих пулах узлов может быть не более 400 узлов. [Ограничения пула узлов][nodepool-limitations].

## <a name="what-can-i-name-my-windows-node-pools"></a>Как можно присвоить имя пулам узлов Windows?

Длина имени должна быть не более 6 (шести) символов. Это текущее ограничение AKS.

## <a name="are-all-features-supported-with-windows-nodes"></a>Поддерживаются ли все функции узлов Windows?

Политики сети и кубенет в настоящее время не поддерживаются для узлов Windows. 

## <a name="can-i-run-ingress-controllers-on-windows-nodes"></a>Можно ли запускать контроллеры входящего трафика на узлах Windows?

Да, входной контроллер, поддерживающий контейнеры Windows Server, может работать на узлах Windows в AKS.

## <a name="can-i-use-azure-dev-spaces-with-windows-nodes"></a>Можно ли использовать Azure Dev Spaces с узлами Windows?

Azure Dev Spaces в настоящее время доступно только для пулов узлов на основе Linux.

## <a name="can-my-windows-server-containers-use-gmsa"></a>Могут ли контейнеры Windows Server использовать gMSA?

Поддержка групповых управляемых учетных записей служб (gMSA) в настоящее время недоступна в AKS.

## <a name="can-i-use-azure-monitor-for-containers-with-windows-nodes-and-containers"></a>Можно ли использовать Azure Monitor для контейнеров с узлами и контейнерами Windows?

Да, вы можете, однако, Azure Monitor не собирает журналы (stdout) из контейнеров Windows. Вы по-прежнему можете присоединиться к потоку журналов stdout из контейнера Windows.

## <a name="what-if-i-need-a-feature-which-is-not-supported"></a>Что делать, если мне нужна функция, которая не поддерживается?

Мы работаем над тем, чтобы приложить все необходимые компоненты Windows в AKS, но если у вас возникают пробелы, проект [AKS-Engine][aks-engine] с открытым кодом предоставляет простой и полностью настраиваемый способ запуска Kubernetes в Azure, включая поддержку Windows. Обязательно ознакомьтесь с нашим планом функций, поступающих в [AKSную схему][aks-roadmap].

## <a name="next-steps"></a>Дальнейшие действия

Чтобы приступить к работе с контейнерами Windows Server в AKS, [Создайте пул узлов под управлением Windows Server в AKS][windows-node-cli].

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
[windows-container-compat]: https://docs.microsoft.com/virtualization/windowscontainers/deploy-containers/version-compatibility#windows-server-2019-host-os-compatibility
