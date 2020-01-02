---
title: Часто задаваемые вопросы о Azure Dev Spaces
services: azure-dev-spaces
ms.date: 09/25/2019
ms.topic: conceptual
description: Найдите ответы на некоторые распространенные вопросы о Azure Dev Spaces
keywords: 'Docker, Kubernetes, Azure, служба контейнеров Azure, служба Azure Kubernetes, контейнеры, Helm, сетка службы, сетка службы маршрутизации, kubectl, k8s '
ms.openlocfilehash: 2baab0812061bec7dcf08d35056804313d873889
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/25/2019
ms.locfileid: "74482301"
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

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Можно ли использовать Azure Dev Spaces в кластерах AKS с разрешенными диапазонами IP-адресов для сервера API?

Да, можно использовать Azure Dev Spaces в кластерах AKS с разрешенными [диапазонами IP-адресов сервера API][aks-auth-range] . При [создании][aks-auth-range-create] кластера необходимо [разрешить дополнительные диапазоны на основе вашего региона][aks-auth-range-ranges]. Можно также [Обновить][aks-auth-range-update] существующий кластер, чтобы разрешить эти дополнительные диапазоны.

### <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Можно ли использовать Azure Dev Spaces в кластерах AKS с ограниченным трафиком исходящего трафика для узлов кластера?

Да, можно использовать Azure Dev Spaces в кластерах AKS с [ограниченным трафиком исходящего трафика для узлов кластера][aks-restrict-egress-traffic] , когда разрешены следующие полные доменные имена:

| ПОЛН                                    | Порт      | Использование      |
|-----------------------------------------|-----------|----------|
| cloudflare.docker.com | HTTPS:443 | Извлечение образов Linux Alpine и других Azure Dev Spaces |
| gcr.io | HTTP: 443 | Извлечение Helm/с образами |
| storage.googleapis.com | HTTP: 443 | Извлечение Helm/с образами |
| аздс —<guid>.<location>. azds.io | HTTPS:443 | Для взаимодействия с Azure Dev Spaces серверных служб для контроллера. Точное полное доменное имя можно найти в "Датапланефкдн" в% USERPROFILE%\.аздс\сеттингс.жсон |


[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-routing]: how-dev-spaces-works.md#how-routing-works
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[supported-regions]: about.md#supported-regions-and-configurations
[windows-containers]: how-to/run-dev-spaces-windows-containers.md