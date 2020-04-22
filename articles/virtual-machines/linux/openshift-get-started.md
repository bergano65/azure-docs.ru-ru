---
title: Обзор OpenShift в Azure
description: Общие сведения об использовании OpenShift в Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 05/7/2019
ms.author: haroldw
ms.openlocfilehash: c338e9a6a793d1c2d0557d70242996175d5a85ed
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/22/2020
ms.locfileid: "81759455"
---
# <a name="openshift-in-azure"></a>Использование OpenShift в Azure

OpenShift — это открытая и расширяемая платформа приложений-контейнеров, которая позволяет использовать Docker и Kubernetes на предприятии.  

OpenShift включает Kubernetes для оркестрации контейнеров и управления ими. Здесь добавлены инструменты для разработчиков и операций, позволяющие:

- быстро разрабатывать приложения;
- легко развертывать и масштабировать решения;
- обслуживать жизненный цикл команд и приложений в долгосрочной перспективе.

Есть несколько версий OpenShift доступны.  Из этих версий только две доступны сегодня для развертывания клиентов в Azure: Платформа OpenShift Container Platform и OKD (ранее OpenShift Origin).

## <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Microsoft Azure Red Hat OpenShift — это полностью управляемое предложение OpenShift, работающее в Azure. Эта служба управляется и поддерживается совместно корпорацией Майкрософт и Red Hat. Для получения более подробной информации можно ознакомиться с документацией [службы OpenShift Azure Red Hat.](https://docs.microsoft.com/azure/openshift/)

## <a name="openshift-container-platform"></a>Платформа контейнеров OpenShift

OpenShift Container Platform — это [коммерческая версия](https://www.openshift.com) корпоративного класса, которая разрабатывается и поддерживается компанией Red Hat. Чтобы использовать эту версию, клиенты должен приобрести необходимые права доступа к платформе OpenShift Container Platform. Кроме того, они сами устанавливают и администрируют всю инфраструктуру.

Поскольку клиенты «владеют» всей платформой, они могут установить ее в своем центре обработки данных или в общедоступном облаке (например, Azure).

## <a name="okd"></a>OKD

OKD — это высокоуровневый проект OpenShift с [открытым кодом](https://www.okd.io/), который поддерживается сообществом. OKD можно установить в ОС CentOS или Red Hat Enterprise Linux (RHEL).

## <a name="next-steps"></a>Следующие шаги

- [Общие предварительные требования для OpenShift в Azure](./openshift-container-platform-3x-prerequisites.md)
- [Развертывание платформы OpenShift Container Platform](./openshift-container-platform-3x.md)
- [Развертывание OpenShift контейнерплатформы самоуправляемой Marketplace Предложение](./openshift-container-platform-3x-marketplace-self-managed.md)
- [Развертывание OpenShift в Azure Stack](./openshift-azure-stack.md)
- [Задачи, выполняемые после развертывания](./openshift-container-platform-3x-post-deployment.md)
- [Устранение неполадок с развертыванием OpenShift](./openshift-container-platform-3x-troubleshooting.md)
