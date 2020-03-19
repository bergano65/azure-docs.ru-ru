---
title: включить файл
description: включить файл
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 03/11/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0090f02382e024e5539383328b55d58798002d63
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117146"
---
| Ресурс | Basic | Standard | Premium |
|---|---|---|---|
| Хранилище<sup>1</sup> | 10 ГБ | 100 ГиБ| 500 ГБ |
| Максимальный размер слоя образа | 200 ГиБ | 200 ГиБ | 200 ГиБ |
| Операции чтения за минуту<sup>2, 3</sup> | 1000 | 3000 | 10 000 |
| Операции записи за минуту<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Пропускная способность скачивания в Мбит/с<sup>2</sup> | 30 | 60 | 100 |
| Пропускная способность передачи в Мбит/с<sup>2</sup> | 10 | 20 | 50 |
| Веб-перехватчики | 2 | 10 | 500 |
| Георепликация | Недоступно | Недоступно | [Поддерживаются][geo-replication] |
| Доверие к содержимому | Недоступно | Недоступно | [Поддерживаются][content-trust] |
| Доступ к виртуальной сети | Недоступно | Недоступно | [Предварительный просмотр][vnet] |
| Интеграция с Приватным каналом | Недоступно | Недоступно | [Предварительный просмотр][plink] |
| Ключи, управляемые клиентом | Недоступно | Недоступно | [Предварительный просмотр][cmk] |
| Разрешения уровня репозитория | Недоступно | Недоступно | [Предварительный просмотр][token]|
| &bull; Токены | Недоступно | Недоступно | 20 000 |
| &bull; Карты области | Недоступно | Недоступно | 20 000 |
| &bull; Репозитории для карт областей | Недоступно | Недоступно | 500 |


<sup>1</sup> Указанные ограничения хранилища — это количество *включенных* хранилищ для каждого уровня. Дополнительная ежедневная плата взымается за ГиБ для хранилища изображений выше этих ограничений. Дополнительные сведения см. в статье [Цены на Реестр контейнеров Azure][pricing].

<sup>2</sup>*Операции чтения*, *операции записи* и *пропускная способность* — это минимальные оценки. Реестр контейнеров Azure стремится обеспечить высокую производительность по мере использования.

<sup>3</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) преобразовывает несколько операций чтения в зависимости от количества слоев образа, а также от извлеченного манифеста.

<sup>4</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) преобразовывает несколько операций записи, исходя из количества слоев, которые нужно отправить. Команда `docker push` включает *операции чтения* для получения манифеста для имеющегося образа.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md