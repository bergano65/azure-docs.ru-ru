---
title: Общие сведения об использовании OpenShift в Azure | Документация Майкрософт
description: Общие сведения об использовании OpenShift в Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: 80f0ec57aa287bae9b10d9a4cdb013014aab3332
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082552"
---
# <a name="openshift-in-azure"></a>Использование OpenShift в Azure

OpenShift — это открытая и расширяемая платформа приложений-контейнеров, которая позволяет использовать Docker и Kubernetes на предприятии.  

OpenShift включает Kubernetes для оркестрации контейнеров и управления ими. Здесь добавлены инструменты для разработчиков и операций, позволяющие:

- быстро разрабатывать приложения;
- легко развертывать и масштабировать решения;
- обслуживать жизненный цикл команд и приложений в долгосрочной перспективе.

Доступно несколько версий OpenShift.  В настоящее время для развертывания клиентов в Azure доступны только два из этих версий: Платформа контейнера OpenShift и ОКД (прежнее OpenShift).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift — это полностью управляемое предложение OpenShift, работающее в Azure. Эта служба управляется и поддерживается совместно корпорацией Майкрософт и Red Hat. Дополнительные сведения см. в документации по [службе Azure Red Hat OpenShift](https://docs.microsoft.com/azure/openshift/) .

## <a name="openshift-container-platform"></a>Платформа контейнеров OpenShift

OpenShift Container Platform — это [коммерческая версия](https://www.openshift.com) корпоративного класса, которая разрабатывается и поддерживается компанией Red Hat. Чтобы использовать эту версию, клиенты должен приобрести необходимые права доступа к платформе OpenShift Container Platform. Кроме того, они сами устанавливают и администрируют всю инфраструктуру.

Поскольку клиенты «владеют» всей платформы, они могут установить ее в локальном центре обработки данных или в общедоступном облаке (например, Azure).

## <a name="okd"></a>OKD

OKD — это высокоуровневый проект OpenShift с [открытым кодом](https://www.okd.io/), который поддерживается сообществом. OKD можно установить в ОС CentOS или Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Следующие шаги

- [Общие предварительные требования для OpenShift в Azure](./openshift-prerequisites.md)
- [Развертывание платформы OpenShift Container Platform](./openshift-container-platform.md)
- [Развертывание самостоятельно управляемой платформы контейнера OpenShift предложение Marketplace](./openshift-marketplace-self-managed.md)
- [Развертывание OpenShift в Azure Stack](./openshift-azure-stack.md)
- [Задачи, выполняемые после развертывания](./openshift-post-deployment.md)
- [Устранение неполадок с развертыванием OpenShift](./openshift-troubleshooting.md)
