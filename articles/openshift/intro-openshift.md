---
title: Общие сведения об Azure Red Hat OpenShift
description: Узнайте о возможностях и преимуществах службы Microsoft Azure Red Hat OpenShift при развертывании и администрировании контейнерных приложений.
author: jimzim
ms.author: jzim
ms.service: container-service
ms.topic: overview
ms.date: 05/08/2019
ms.custom: mvc
ms.openlocfilehash: 4be388eec3851154fd0f6af37d03a468fc52197b
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/19/2020
ms.locfileid: "76276072"
---
# <a name="azure-red-hat-openshift"></a>Azure Red Hat OpenShift

Служба Microsoft *Azure Red Hat OpenShift* позволяет развертывать полностью управляемые [кластеры](https://www.openshift.com/) OpenShift.

Azure Red Hat OpenShift расширяет платформу [Kubernetes](https://kubernetes.io/). Для использования контейнеров в рабочей среде с Kubernetes требуются дополнительные средства и ресурсы, такие как реестр образов, управление хранением, сетевые решения, а также средства ведения журнала и мониторинга, все из которых должны иметь свою версию и быть совместно протестированы. Создание контейнерных приложений требует даже большей работы над интеграцией с ПО промежуточного слоя, платформами, базами данных и средствами CI/CD. Служба Azure Red Hat OpenShift объединяет все это в рамках одной платформы, что упрощает эксплуатацию для ИТ-отделов, предоставляя отделам по работе с приложениями только те задания, которые им нужно выполнить.

Корпорация Майкрософт и Red Hat совместно выполняют задачи разработки, администрирования и поддержки службы Azure Red Hat OpenShift, обеспечивая интегрированный процесс поддержки. На этой платформе нет виртуальных машин, которыми вам нужно управлять, и не требуется устанавливать исправления. Исправление, обновление и отслеживание главного узла, а также узлов инфраструктуры и приложений от вашего имени осуществляется компанией Red Hat и корпорацией Майкрософт. Кластеры Azure Red Hat OpenShift развертываются в подписке Azure и включаются в счет Azure.

Вы можете выбрать собственный реестр, сеть, хранилище и решения CI/CD или использовать встроенные решения для автоматического управления исходным кодом, создания контейнеров и приложений, а также их развертывания, масштабирования, управления состоянием работоспособности и многого другого. Служба Azure Red Hat OpenShift предоставляет возможности интеграции единого входа через Azure Active Directory.

Чтобы приступить к работе, выполните действия из руководства по [созданию кластера Azure Red Hat OpenShift](tutorial-create-cluster.md).

## <a name="access-security-and-monitoring"></a>Доступ, безопасность и мониторинг

Для повышения безопасности и улучшения управления Azure Red Hat OpenShift позволяет выполнить интеграцию с Azure Active Directory и использовать управление доступом на основе ролей Kubernetes (RBAC). Также можно отслеживать работоспособность кластера и ресурсов.

## <a name="cluster-and-node"></a>Кластер и узел

Узлы Azure Red Hat OpenShift выполняются на виртуальных машинах Azure. Можно подключить хранилище к узлам и pod, обновить компоненты кластера и использовать GPU.

## <a name="virtual-networks-and-ingress"></a>Виртуальные сети и входные данные

Вы можете [подключить кластер Azure Red Hat OpenShift к существующей виртуальной сети](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network) с помощью пирингового соединения. В этой конфигурации модули pod могут подключаться к другим службам в одноранговой виртуальной сети.

Дополнительные сведения см. в разделе [Подключение виртуальной сети кластера к существующей виртуальной сети](tutorial-create-cluster.md#optional-connect-the-clusters-virtual-network-to-an-existing-virtual-network).

## <a name="kubernetes-certification"></a>Сертификация Kubernetes

Служба Azure Red Hat OpenShift была сертифицирована Cloud Native Computing Foundation (CNCF) в качестве согласованной с Kubernetes.

## <a name="next-steps"></a>Дальнейшие действия

Ознакомьтесь с необходимыми условиями для Azure Red Hat OpenShift:

> [!div class="nextstepaction"]
> [Настройка среды разработки](howto-setup-environment.md)
