---
title: включить файл
description: включить файл
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 06/18/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 3f68ca0fc577e6cf3f896ede0418f11f59756701
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86512625"
---
| Ресурс | Basic | Standard | Premium |
|---|---|---|---|
| Добавленное хранилище<sup>1</sup> (гиб) | 10 | 100 | 500 |
| Ограничение хранилища (тиб) | 20| 20 | 20 |
| Максимальный размер уровня изображения (гиб) | 200 | 200 | 200 |
| Операции чтения за минуту<sup>2, 3</sup> | 1000 | 3000 | 10 000 |
| Операции записи за минуту<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Пропускная способность скачивания в Мбит/с<sup>2</sup> | 30 | 60 | 100 |
| Пропускная способность передачи в Мбит/с<sup>2</sup> | 10 | 20 | 50 |
| Веб-перехватчики | 2 | 10 | 500 |
| Георепликация | Недоступно | Недоступно | [Поддерживаются][geo-replication] |
| Доверие к содержимому | Недоступно | Недоступно | [Поддерживаются][content-trust] |
| Приватный канал с частными конечными точками | Недоступно | Недоступно | [Поддерживаются][plink] |
| &bull;Частные конечные точки | Недоступно | Недоступно | 10 |
| Получение доступа к виртуальной сети конечной точки службы | Недоступно | Недоступно | [Предварительный просмотр][vnet] |
| Ключи, управляемые клиентом | Недоступно | Недоступно | [Поддерживаются][cmk] |
| Разрешения уровня репозитория | Недоступно | Недоступно | [Предварительный просмотр][token]|
| &bull; Токены | Недоступно | Недоступно | 20 000 |
| &bull; Карты области | Н/Д | Н/Д | 20 000 |
| &bull; Репозитории для карт областей | Недоступно | Недоступно | 500 |


<sup>1</sup> хранилище, которое входит в дневную частоту для каждого уровня. Для дополнительного хранилища вы платите за дополнительную ежедневную ставку на гиб, вплоть до предела хранилища. Дополнительные сведения см. в статье [Цены на Реестр контейнеров Azure][pricing].

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