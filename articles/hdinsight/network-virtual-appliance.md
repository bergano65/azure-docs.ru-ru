---
title: Настройка сетевого виртуального устройства в Azure HDInsight
description: Узнайте, как настроить ряд дополнительных функций для сетевого виртуального устройства в Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: cbc2104ae3c55ae3670867b7a253d812f3a4be0e
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864712"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Настройка сетевого виртуального устройства в Azure HDInsight

> [!Important]
> Следующие сведения требуются **только** в том случае, если требуется настроить сетевой виртуальный модуль (NVA), отличный от брандмауэра Azure.

Брандмауэр Azure автоматически настраивается на разрешение трафика для многих распространенных важных сценариев. Для использования другого сетевого виртуального устройства потребуется настроить ряд дополнительных функций. При настройке сетевого виртуального устройства учитывайте следующие факторы:

* В разделе "Конечные точки" должны быть указаны конечные точки для включенных служб.
* Зависимости IP-адресов предназначены для трафика, отличного от HTTP и S (трафик TCP и UDP).
* Полные доменные конечные точки HTTP и HTTPS можно разместить на устройстве NVA.
* Конечные точки HTTP и HTTPS с подстановочными знаками — это зависимости, которые могут различаться в зависимости от количества квалификаторов.
* Назначьте таблицу маршрутов, созданную в подсети HDInsight.

## <a name="service-endpoint-capable-dependencies"></a>Зависимости, поддерживающие конечную точку службы

| **Конечная точка** |
|---|
| Azure SQL |
| Хранилище Azure |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>Зависимости IP-адреса

| **Конечная точка** | **Сведения** |
|---|---|
| \*:123 | Проверка часов NTP. Трафик проверяется в нескольких конечных точках на порте 123. |
| IP-адреса, опубликованные [здесь](hdinsight-management-ip-addresses.md) | Эти IP-адреса являются службой HDInsight. |
| Частные IP-адреса AAD DS для кластеров ESP |
| \*: 16800 для активации KMS Windows |
| \*12000 для Log Analytics |

### <a name="fqdn-httphttps-dependencies"></a>Зависимости FQDN протокола HTTP/HTTPS

> [!Important]
> Приведенный ниже список содержит лишь несколько наиболее важных полных доменных имен. Для настройки NVA [в этом файле](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)можно получить дополнительные полные доменные имена (в основном служба хранилища Azure и служебная шина Azure).

| **Конечная точка**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Дальнейшие действия

* [Ограничение исходящего трафика с помощью брандмауэра](./hdinsight-restrict-outbound-traffic.md)
* [Архитектура виртуальной сети Azure HDInsight](hdinsight-virtual-network-architecture.md)
