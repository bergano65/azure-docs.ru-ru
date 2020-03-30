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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79117146"
---
| Ресурс | Basic | Standard | Premium |
|---|---|---|---|
| Хранение<sup>1</sup> | 10 ГБ | 100 ГиБ| 500 ГБ |
| Максимальный размер слоя изображения | 200 GiB | 200 GiB | 200 GiB |
| Операции чтения за минуту<sup>2, 3</sup> | 1000 | 3000 | 10 000 |
| Операции записи за минуту<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Пропускная способность скачивания в Мбит/с<sup>2</sup> | 30 | 60 | 100 |
| Пропускная способность передачи в Мбит/с<sup>2</sup> | 10 | 20 | 50 |
| Веб-перехватчики | 2 | 10 | 500 |
| Георепликация | Недоступно | Недоступно | [Поддерживается][geo-replication] |
| Доверие к содержимому | Недоступно | Недоступно | [Поддерживается][content-trust] |
| Виртуальный доступ к сети | Недоступно | Недоступно | [Предварительный просмотр][vnet] |
| Интеграция частных ссылк | Недоступно | Недоступно | [Предварительный просмотр][plink] |
| Ключи, управляемые клиентом | Недоступно | Недоступно | [Предварительный просмотр][cmk] |
| Разрешения, разбиваемые репозиторием | Недоступно | Недоступно | [Предварительный просмотр][token]|
| &bull;Маркеры | Недоступно | Недоступно | 20 000 |
| &bull;Карты области | Недоступно | Недоступно | 20 000 |
| &bull;Репозитории на карту области | Недоступно | Недоступно | 500 |


<sup>1</sup> Указанные лимиты хранения - это количество *включенного* хранилища для каждого уровня. Дополнительная ежедневная плата взымается за ГиБ для хранилища изображений выше этих ограничений. Для получения информации о тарифах [см.][pricing]

<sup>2</sup>*Операции чтения*, *операции записи* и *пропускная способность* — это минимальные оценки. Реестр контейнеров Azure стремится повысить производительность по мере использования.

<sup>3</sup> [Притяжение докера](https://docs.docker.com/registry/spec/api/#pulling-an-image) переводится на несколько считываемых операций в зависимости от количества слоев на изображении, а также извлечения манифеста.

<sup>4</sup> Толчок [докера](https://docs.docker.com/registry/spec/api/#pushing-an-image) переводится как несколько операций записи, в зависимости от количества слоев, которые должны быть перемещены. Команда `docker push` включает *операции чтения* для получения манифеста для имеющегося образа.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md