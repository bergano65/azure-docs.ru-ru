---
title: Часто задаваемые вопросы о Azure Dev Spaces
titleSuffix: Azure Dev Spaces
services: azure-dev-spaces
ms.service: azure-dev-spaces
author: zr-msft
ms.author: zarhoads
ms.date: 09/25/2019
ms.topic: conceptual
description: Найдите ответы на некоторые распространенные вопросы о Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
ms.openlocfilehash: 317a942f94f3852cc11b8c0f004ed55097d05bc6
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014223"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Часто задаваемые вопросы о Azure Dev Spaces

Здесь рассматриваются часто задаваемые вопросы о Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Какие регионы Azure в настоящее время предоставляют Azure Dev Spaces?

Полный список доступных регионов см. в разделе [Поддерживаемые регионы и конфигурации][supported-regions].

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Можно ли использовать Azure Dev Spaces без общедоступного IP-адреса?

Нет, вы не можете подготавливать Azure Dev Spaces в кластере AKS без общедоступного IP-адреса. [Azure dev Spaces для маршрутизации требуется][dev-spaces-routing]общедоступный IP-адрес.

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Можно ли использовать собственный входной параметр с Azure Dev Spaces?

Да, можно настроить собственный входящий трафик на стороне, созданной Azure Dev Spaces. Например, можно использовать [траефик][ingress-traefik].

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Можно ли использовать HTTPS с Azure Dev Spaces?

Да, можно настроить собственный вход с использованием протокола HTTPS с помощью [траефик][ingress-https-traefik].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Можно ли использовать Azure Dev Spaces в кластере, который использует CNI, а не кубенет? 

Да, можно использовать Azure Dev Spaces в кластере AKS, который использует CNI для работы в сети. Например, можно использовать Azure Dev Spaces в кластере AKS с [существующими контейнерами Windows][windows-containers], которые используют CNI для работы в сети.

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Можно ли использовать Azure Dev Spaces с контейнерами Windows?

В настоящее время Azure Dev Spaces предназначен для работы только в Pod и узлах Linux, но вы можете запускать Azure Dev Spaces в кластере AKS с [существующими контейнерами Windows][windows-containers].

### <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Можно ли использовать Azure Dev Spaces в кластерах AKS с разрешенными диапазонами IP-адресов для сервера API?

Да, можно использовать Azure Dev Spaces в кластерах AKS с разрешенными [диапазонами IP-адресов сервера API][aks-auth-range] . При [создании][aks-auth-range-create] кластера необходимо [разрешить дополнительные диапазоны на основе вашего региона][aks-auth-range-ranges]. Можно также [Обновить][aks-auth-range-update] и существующий кластер, чтобы разрешить эти дополнительные диапазоны.

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md