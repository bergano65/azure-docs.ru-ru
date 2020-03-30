---
title: Часто задаваемые вопросы о пространствах Azure Dev
services: azure-dev-spaces
ms.date: 01/28/2020
ms.topic: conceptual
description: Найдите ответы на некоторые из распространенных вопросов о пространствах Azure Dev
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, containers, Helm, service mesh, service mesh routing, kubectl, k8s '
ms.openlocfilehash: e7b4620faa01aa9f6d46c34bafb1c623c338beb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240498"
---
# <a name="frequently-asked-questions-about-azure-dev-spaces"></a>Часто задаваемые вопросы о пространствах Azure Dev

При этом часто задаются вопросы о пространствах Azure Dev Spaces.

## <a name="which-azure-regions-currently-provide-azure-dev-spaces"></a>Какие регионы Azure в настоящее время предоставляют пространства Azure Dev?

Полный список доступных регионов можно найти в [поддерживаемых регионах.][supported-regions]

## <a name="can-i-migrate-my-aks-cluster-with-azure-dev-spaces-to-another-region"></a>Могу ли я перенести кластер AKS с Azure Dev Spaces в другой регион?

Да, если вы хотите переместить кластер AKS с Azure Dev Spaces в другой [поддерживаемый регион,][supported-regions]мы рекомендуем создать новый кластер в другом регионе, а затем установить и настроить пространства Azure Dev spaces и развернуть ресурсы и приложения в новый кластер. Для получения дополнительной информации о миграции AKS [см.][aks-migration]

## <a name="can-i-use-azure-dev-spaces-with-existing-dockerfiles-or-helm-charts"></a>Могу ли я использовать пространства Azure Dev с существующими диаграммами Dockerfiles или Helm?

Да, если в проекте уже есть диаграмма Dockerfile или Helm, эти файлы можно использовать в Azure Dev Spaces. При запуске, `azds prep` `--chart` использовать параметр и указать местоположение диаграммы. Пространства Azure Dev по-прежнему будут генерировать файл *azds.yaml* и *Dockerfile.develop,* но он не будет заменять или изменять существующую диаграмму Dockerfile или диаграмму Helm. Возможно, вам придется изменить *azds.yaml* и *Dockerfile.develop* файлы для того, `azds up`чтобы все работало правильно с существующим приложением при запуске.

При использовании собственной диаграммы Dockerfile или Helm существуют следующие ограничения:
* При использовании только одного Dockerfile он должен включать все необходимые сценарии разработки, такие как язык SDK, а не только время выполнения. При использовании отдельного Dockerfile для Azure Dev Spaces, например Dockerfile.develop, все, что нужно для включения сценариев разработки, должно быть включено в этот Dockerfile.
* Диаграмма Helm должна поддерживать прохождение части или всего тега изображения в качестве значения от *values.yaml.*
* Если вы изменяете что-либо с помощью входа, вы также можете обновить диаграмму Helm, чтобы использовать решение входа, предоставляемое Пространствами Azure Dev Spaces.
* Если вы хотите использовать [возможности разгрома, предоставляемые Azure Dev Spaces,][dev-spaces-routing]все службы для отдельного проекта должны вписываться в одно пространство имен Kubernetes и должны быть развернуты с простым именованием, например *service-a.* В стандартных диаграммах Helm это обновление имен можно сделать, указав значение для свойства *fullnameOverride.*

Чтобы сравнить свой собственный график Dockerfile или Helm с существующей версией, работающую с Azure Dev Spaces, просмотрите файлы, генерируемые в [quickstart.][quickstart-cli]


## <a name="can-i-modify-the-files-generated-by-azure-dev-spaces"></a>Могу ли я изменить файлы, генерируемые пространствами Azure Dev?

Да, при подготовке проекта можно изменить файл *azds.yaml,* Dockerfile и таблицу Helm, [генерируемый Azure Dev Spaces.][dev-spaces-prep] Изменение этих файлов изменяет способ построения и выполнения проекта.

## <a name="can-i-use-azure-dev-spaces-without-a-public-ip-address"></a>Могу ли я использовать пространства Azure Dev без публичного IP-адреса?

Нет, вы не можете предоставить пространства Azure Dev в кластере AKS без публичного IP-адреса. Для области [реуктора необходим][dev-spaces-routing]общедоступный IP-адрес Azure Dev Spaces.

## <a name="can-i-use-my-own-ingress-with-azure-dev-spaces"></a>Могу ли я использовать свой собственный вход с Azure Dev Spaces?

Да, вы можете настроить свой собственный вход вдоль стороны входа Azure Dev Spaces создает. Например, можно использовать [traefik][ingress-traefik] или [NGINX.][ingress-nginx]

## <a name="can-i-use-https-with-azure-dev-spaces"></a>Могу ли я использовать HTTPS с azure Dev Spaces?

Да, вы можете настроить свой собственный вход с httpS с помощью [traefik][ingress-https-traefik] или [NGINX][ingress-https-nginx].

## <a name="can-i-use-azure-dev-spaces-on-a-cluster-that-uses-cni-rather-than-kubenet"></a>Можно ли использовать пространства Azure Dev в кластере, который использует CNI, а не kubenet? 

Да, можно использовать пространства Azure Dev пробелы в кластере AKS, который использует CNI для создания сетей. Например, можно использовать пространства Azure Dev пробелы в кластере AKS с [существующими контейнерами Windows,][windows-containers]который использует CNI для создания сетей. Более подробную информацию об использовании CNI для создания сетей с Azure Dev Spaces можно найти [здесь](configure-networking.md#using-azure-cni).

## <a name="can-i-use-azure-dev-spaces-with-windows-containers"></a>Можно ли использовать пространства Azure Dev с контейнерами Windows?

В настоящее время Azure Dev Spaces предназначен для работы только на стручках и узлах Linux, но вы можете запускать Пространства Azure Dev в кластере AKS с [существующими контейнерами Windows.][windows-containers]

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-api-server-authorized-ip-address-ranges-enabled"></a>Можно ли использовать пространства Azure Dev в кластерах AKS с включенными диапазонами IP-адресов сервера API?

Да, можно использовать пространства Azure Dev в кластерах AKS с [включенными диапазонами IP-адресов сервера API.][aks-auth-range] Более подробная информация об использовании кластеров AKS с диапазонами авторизованных IP-адресов сервера API включена с Azure Dev Spaces [доступна здесь.](configure-networking.md#using-api-server-authorized-ip-ranges)

## <a name="can-i-use-azure-dev-spaces-on-aks-clusters-with-restricted-egress-traffic-for-cluster-nodes"></a>Можно ли использовать пространства Azure Dev в кластерах AKS с ограниченным трафиком для кластерных узлов?

Да, можно использовать пространства Azure Dev на кластерах AKS с [ограниченным трафиком для кластерных узлов,][aks-restrict-egress-traffic] включенных после того, как были разрешены правильные F-DN. Более подробная информация об использовании кластеров AKS с ограниченным трафиком на вход для кластерных узлов, включенных с Azure Dev Spaces, доступна [здесь.](configure-networking.md#ingress-and-egress-network-traffic-requirements)

## <a name="can-i-use-azure-dev-spaces-on-rbac-enabled-aks-clusters"></a>Можно ли использовать пространства Azure Dev в кластерах AKS с поддержкой RBAC?

Да, вы можете использовать пространства Azure Dev в кластерах AKS с включенным RBAC или без нее.

## <a name="what-happens-when-i-enable-ingress-for-project-in-visual-studio"></a>Что происходит, когда я включаю вход для проекта в Visual Studio?

При использовании Visual Studio для подготовки проекта у вас есть возможность включить вход для вашего сервиса. Включение входа создает общедоступную точку доступа к службе при запуске кластера AKS, что является необязательным. Если вы не включите вход, ваша служба доступна только внутри кластера AKS.

## <a name="can-i-use-pod-managed-identities-with-azure-dev-spaces"></a>Могу ли я использовать управляемые дипподылки с Azure Dev Spaces?

В настоящее время Azure Dev Spaces не поддерживает использование [управляемых идентификаторов pod][aks-pod-managed-id] на кластерах AKS с включенным пространством Azure Dev Spaces. Если у вас установлены идентификационные данные стручка и вы хотите удалить их, вы можете найти более подробную информацию в [примечаниях удалить.][aks-pod-managed-id-uninstall]

[aks-auth-range]: ../aks/api-server-authorized-ip-ranges.md
[aks-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-auth-range-ranges]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[aks-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-migration]: ../aks/aks-migration.md
[aks-pod-managed-id]: ../aks/developer-best-practices-pod-security.md#use-pod-managed-identities
[aks-pod-managed-id-uninstall]: https://github.com/Azure/aad-pod-identity#uninstall-notes
[aks-restrict-egress-traffic]: ../aks/limit-egress-traffic.md
[dev-spaces-prep]: how-dev-spaces-works-prep.md
[dev-spaces-routing]: how-dev-spaces-works-routing.md#how-routing-works
[ingress-nginx]: how-to/ingress-https-nginx.md#configure-a-custom-nginx-ingress-controller
[ingress-traefik]: how-to/ingress-https-traefik.md#configure-a-custom-traefik-ingress-controller
[ingress-https-nginx]: how-to/ingress-https-nginx.md#configure-the-nginx-ingress-controller-to-use-https
[ingress-https-traefik]: how-to/ingress-https-traefik.md#configure-the-traefik-ingress-controller-to-use-https
[quickstart-cli]: quickstart-cli.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[windows-containers]: how-to/run-dev-spaces-windows-containers.md