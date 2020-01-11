---
title: Настройка сети для Azure Dev Spaces в различных сетевых топологиях
services: azure-dev-spaces
ms.date: 01/10/2020
ms.topic: conceptual
description: Описание требований к сети для запуска Azure Dev Spaces в Azure Kubernetes Services
keywords: Azure Dev Spaces, пространства разработки, Docker, Kubernetes, Azure, AKS, служба Kubernetes Azure, контейнеры, CNI, кубенет, SDN, сеть
ms.openlocfilehash: 51604e2862a4d2ff575906fa2ba480ddd10504ed
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/11/2020
ms.locfileid: "75897927"
---
# <a name="configure-networking-for-azure-dev-spaces-in-different-network-topologies"></a>Настройка сети для Azure Dev Spaces в различных сетевых топологиях

Azure Dev Spaces выполняется в кластерах Azure Kubernetes Service (AKS) с конфигурацией сети по умолчанию. Если вы хотите изменить конфигурацию сети кластера AKS, например размещение кластера за брандмауэром, использование групп безопасности сети или политик сети, необходимо включить дополнительные рекомендации для запуска Azure Dev Spaces.

![Конфигурация виртуальной сети](media/configure-networking/virtual-network-clusters.svg)

## <a name="aks-clusters-with-different-virtual-network-or-subnet-configurations"></a>Кластеры AKS с разными конфигурациями виртуальной сети или подсети

Кластер AKS может иметь другую конфигурацию виртуальной сети или подсети для ограничения входящего или исходящего трафика для кластера AKS. Например, кластер может находиться за брандмауэром, например брандмауэром Azure, или же можно использовать группы безопасности сети или пользовательские роли, чтобы ограничивать сетевой трафик.

Azure Dev Spaces имеет определенные требования для входящего *и* исходящего сетевого трафика, а также входящего *трафика.* Если вы используете Azure Dev Spaces в кластере AKS с конфигурацией виртуальной сети или подсети, которая разрешает трафик для кластера AKS, необходимо следовать следующим требованиям к входящему и входящему трафику и исходящим данным, чтобы Azure Dev Spaces правильно работать.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Требования к сетевому трафику входящих и исходящих данных

Azure Dev Spaces требуется входящий и исходящий трафик для следующих полных доменных имен:

| Полное доменное имя.                       | Port       | Использование      |
|----------------------------|------------|----------|
| cloudflare.docker.com      | HTTPS: 443 | Извлечение образов DOCKER для Azure Dev Spaces |
| gcr.io                     | HTTPS: 443 | Извлечение Helm изображений для Azure Dev Spaces |
| storage.googleapis.com     | HTTPS: 443 | Извлечение Helm изображений для Azure Dev Spaces |
| аздс-*. аздс. IO             | HTTPS: 443 | Для взаимодействия с Azure Dev Spaces серверными службами для контроллера Azure Dev Spaces. Точное полное доменное имя можно найти в *датапланефкдн* в `USERPROFILE\.azds\settings.json` |

Обновите брандмауэр или конфигурацию безопасности, чтобы разрешить сетевой трафик для всех указанных выше полных доменных имен. Например, если вы используете брандмауэр для защиты сети, необходимо добавить указанные выше полные доменные имена в правило приложения брандмауэра, чтобы разрешить входящий и исходящий трафик этих доменов.

### <a name="ingress-only-network-traffic-requirements"></a>Только входящие требования к сетевому трафику

Azure Dev Spaces обеспечивает маршрутизацию на уровне пространства имен Kubernetes, а также открытый доступ к службам с помощью собственного полного доменного имени. Чтобы обе эти функции работали, обновите брандмауэр или конфигурацию сети, чтобы разрешить общий вход на внешний IP-адрес контроллера Azure Dev Spaces входящего трафика в кластере. Кроме того, можно создать [внутреннюю подсистему балансировки нагрузки][aks-internal-lb] и добавить правило NAT в брандмауэр, чтобы перевести общедоступный IP-адрес брандмауэра на IP-адрес внутренней подсистемы балансировки нагрузки. Можно также использовать [траефик][traefik-ingress] или [nginx][nginx-ingress] для создания пользовательского контроллера входящего трафика.

## <a name="aks-cluster-network-requirements"></a>Требования к сети кластера AKS

AKS позволяет использовать [Сетевые политики][aks-network-policies] для управления входящим и исходящим трафиком между модулями Pod в кластере, а также исходящим трафиком из Pod. Azure Dev Spaces имеет определенные требования для входящего *и* исходящего сетевого трафика, а также входящего *трафика.* Если вы используете Azure Dev Spaces в кластере AKS с политиками сети AKS, то для правильной работы Azure Dev Spaces необходимо следовать следующим требованиям к входящему и входящему трафику, а также порядку исходящего трафика.

### <a name="ingress-and-egress-network-traffic-requirements"></a>Требования к сетевому трафику входящих и исходящих данных

Azure Dev Spaces позволяет напрямую взаимодействовать с Pod в пространстве разработчика в кластере для отладки. Чтобы эта функция работала, добавьте сетевую политику, которая разрешает входящий и исходящий обмен данными с IP-адресами инфраструктуры Azure Dev Spaces, которая [зависит от региона][dev-spaces-ip-auth-range-regions].

### <a name="ingress-only-network-traffic-requirements"></a>Только входящие требования к сетевому трафику

Azure Dev Spaces обеспечивает маршрутизацию между модулями Pod между пространствами имен. Например, пространства имен с включенным Azure Dev Spaces могут иметь связь типа «родители-потомки», которая позволяет маршрутизировать сетевой трафик между модулями Pod в родительском и дочернем пространствах имен. Чтобы эта функция работала, добавьте сетевую политику, которая разрешает трафик между пространствами имен, в которые направляется сетевой трафик, например пространства имен "родители-потомки". Кроме того, если контроллер входящего трафика развертывается в пространстве имен *аздс* , то контроллеру входящего трафика необходимо взаимодействовать с модулями, оснащенными пространством разработки Azure, в другом пространстве имен. Для правильной работы контроллера входящего трафика необходимо разрешить сетевой трафик из пространства имен *аздс* в пространство имен, в котором выполняются Инструментированные модули хранения данных.

## <a name="using-azure-container-networking-with-azure-dev-spaces"></a>Использование сети контейнеров Azure с Azure Dev Spaces

По умолчанию кластеры AKS настроены для использования [кубенет][aks-kubenet] для работы в сети, которая работает с Azure dev Spaces. Вы также можете настроить кластер AKS для использования [сетевого интерфейса контейнера Azure (CNI)][aks-cni]. Чтобы использовать Azure Dev Spaces с Azure CNI в кластере AKS, разрешите виртуальной сети и адресным пространствам подсети до 10 частных IP-адресов для модулей Pod, развернутых Azure Dev Spaces. Дополнительные сведения о разрешении частных IP-адресов доступны в [документации по AKS Azure CNI][aks-cni-ip-planning].

## <a name="using-api-server-authorized-ip-ranges-with-azure-dev-spaces"></a>Использование IP-диапазонов с авторизацией сервера API с Azure Dev Spaces

Кластеры AKS позволяют настроить дополнительную безопасность, которая ограничивает IP-адрес, который может взаимодействовать с кластерами, например с помощью пользовательских виртуальных сетей или путем [защиты доступа к серверу API с помощью разрешенных IP-диапазонов][aks-ip-auth-ranges]. Чтобы использовать Azure Dev Spaces при использовании этой дополнительной защиты при [создании][aks-ip-auth-range-create] кластера, необходимо [разрешить дополнительные диапазоны на основе вашего региона][dev-spaces-ip-auth-range-regions]. Можно также [Обновить][aks-ip-auth-range-update] существующий кластер, чтобы разрешить эти дополнительные диапазоны. Также необходимо разрешить IP-адрес любых компьютеров разработки, подключающихся к кластеру AKS для отладки, для подключения к серверу API.

## <a name="using-aks-private-clusters-with-azure-dev-spaces"></a>Использование частных кластеров AKS с Azure Dev Spaces

В настоящее время Azure Dev Spaces не поддерживается для [частных кластеров AKS][aks-private-clusters].

## <a name="azure-dev-spaces-client-requirements"></a>Требования к клиенту Azure Dev Spaces

Azure Dev Spaces использует средства на стороне клиента, такие как расширение CLI Azure Dev Spaces, расширение Visual Studio Code и расширение Visual Studio, чтобы взаимодействовать с кластером AKS для отладки. Чтобы использовать средства Azure Dev Spaces на стороне клиента, разрешите трафик с компьютеров разработки в домен *аздс-\*. azds.IO* . Точное полное доменное имя см. в разделе *датапланефкдн* in `USERPROFILE\.azds\settings.json`. При использовании [IP-диапазонов разрешенного сервера API][auth-range-section]необходимо также разрешить IP-адрес любых компьютеров разработки, подключающихся к кластеру AKS для отладки, для подключения к серверу API.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как в Azure Dev Spaces можно разрабатывать более сложные приложения в нескольких контейнерах и как упростить совместную разработку, используя разные версии и ветви кода в разных средах.

> [!div class="nextstepaction"]
> [Краткое руководство. Коллективная разработка в Kubernetes с использованием Azure Dev Spaces][team-quickstart]

[aks-cni]: ../aks/configure-azure-cni.md
[aks-cni-ip-planning]: ../aks/configure-azure-cni.md#plan-ip-addressing-for-your-cluster
[aks-kubenet]: ../aks/configure-kubenet.md
[aks-internal-lb]: ../aks/internal-lb.md
[aks-ip-auth-ranges]: ../aks/api-server-authorized-ip-ranges.md
[aks-ip-auth-range-create]: ../aks/api-server-authorized-ip-ranges.md#create-an-aks-cluster-with-api-server-authorized-ip-ranges-enabled
[aks-ip-auth-range-update]: ../aks/api-server-authorized-ip-ranges.md#update-a-clusters-api-server-authorized-ip-ranges
[aks-network-policies]: ../aks/use-network-policies.md
[aks-private-clusters]: ../aks/private-clusters.md
[auth-range-section]: #using-api-server-authorized-ip-ranges-with-azure-dev-spaces
[dev-spaces-ip-auth-range-regions]: https://github.com/Azure/dev-spaces/tree/master/public-ips
[traefik-ingress]: how-to/ingress-https-traefik.md
[nginx-ingress]: how-to/ingress-https-nginx.md
[team-quickstart]: quickstart-team-development.md