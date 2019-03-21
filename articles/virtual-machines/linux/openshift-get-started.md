---
title: Общие сведения об использовании OpenShift в Azure | Документация Майкрософт
description: Общие сведения об использовании OpenShift в Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: joraio
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/01/2019
ms.author: haroldw
ms.openlocfilehash: 826085df8d928cab0a05527be8c464af5f4e9180
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58002513"
---
# <a name="openshift-in-azure"></a>Использование OpenShift в Azure

OpenShift — это открытая и расширяемая платформа приложений-контейнеров, которая позволяет использовать Docker и Kubernetes на предприятии.  

OpenShift включает Kubernetes для оркестрации контейнеров и управления ими. Здесь добавлены инструменты для разработчиков и операций, позволяющие:

- быстро разрабатывать приложения;
- легко развертывать и масштабировать решения;
- обслуживать жизненный цикл команд и приложений в долгосрочной перспективе.

Существует несколько версий OpenShift.

- Платформа контейнеров OpenShift
- OpenShift в Azure (полностью управляемый OpenShift ожидается в конце Q1 CY2019)
- OKD (прежнее название — OpenShift Origin);
- OpenShift Dedicated
- OpenShift Online

Из пяти версий, описанных в этой статье, пользователи могут использовать только две версии для развертывания в Azure: платформу контейнеров OpenShift и OKD.

## <a name="openshift-container-platform"></a>Платформа контейнеров OpenShift

OpenShift Container Platform — это [коммерческая версия](https://www.openshift.com) корпоративного класса, которая разрабатывается и поддерживается компанией Red Hat. Чтобы использовать эту версию, клиенты должен приобрести необходимые права доступа к платформе OpenShift Container Platform. Кроме того, они сами устанавливают и администрируют всю инфраструктуру.

Поскольку клиенты «владел» всей платформы, их можно установить в их локальном центре обработки данных или в общедоступном облаке (Azure).

## <a name="openshift-on-azure"></a>OpenShift в Azure

OpenShift в Azure — это полностью управляемое предложение OpenShift, выполняемое в Azure. Эта служба управляется и поддерживается совместно корпорацией Майкрософт и Red Hat. Кластер будет развернут в подписку Azure клиента. Сейчас служба находится в закрытой предварительной версии, а в конце Q1 CY2019 планируется выпустить общедоступную версию. Пользователи, которые хотели би получить закрытую предварительную версию, должны заполнить форму предварительного утверждения [здесь](https://aka.ms/openshiftazureinterest).  Дополнительные сведения будут представлены, когда предложение будет ближе к общедоступной версии.

## <a name="okd-formerly-openshift-origin"></a>OKD (прежнее название — OpenShift Origin);

OKD — это высокоуровневый проект OpenShift с [открытым кодом](https://www.okd.io/), который поддерживается сообществом. OKD можно установить в ОС CentOS или Red Hat Enterprise Linux (RHEL).

## <a name="openshift-dedicated"></a>OpenShift Dedicated

OpenShift Dedicated — это управляемая Red Hat *однотенантная* OpenShift, которая использует платформу контейнеров OpenShift. Red Hat управляет всей базовой инфраструктурой (виртуальными машинами, кластером OpenShift, сетью, хранилищем и т. д.). Кластер относится только к одному клиенту и выполняется в общедоступном облаке (Azure). Начальный кластер включает четыре узла приложений, и все затраты — ежегодные и оплачиваются заранее.

## <a name="openshift-online"></a>OpenShift Online

OpenShift Online — это *мультитенантная* платформа OpenShift (на базе Container Platform) под управлением Red Hat. Red Hat управляет всей базовой инфраструктурой (виртуальными машинами, кластером OpenShift, сетью и хранилищем). 

Чтобы использовать эту версию, клиенты развертывают контейнеры, но при этом они не могут выбирать, на каких узлах эти контейнеры выполняются. Так как OpenShift Online — это мультитенантная среда, контейнеры могут размещаться на тех же узлах виртуальных машин, что и контейнеры других клиентов. Стоимость вычисляется из расчета на один контейнер.

## <a name="next-steps"></a>Дальнейшие действия

- [Общие предварительные требования для OpenShift в Azure](./openshift-prerequisites.md)
- [Развертывание платформы OpenShift Container Platform](./openshift-container-platform.md)
- [Развертывание OKD в Azure](./openshift-okd.md)
- [Развертывание OpenShift в Azure Stack](./openshift-azure-stack.md)
- [Задачи, выполняемые после развертывания](./openshift-post-deployment.md)
- [Устранение неполадок с развертыванием OpenShift](./openshift-troubleshooting.md)
