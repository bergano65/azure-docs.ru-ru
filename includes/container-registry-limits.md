---
title: включение файла
description: включение файла
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 11/05/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: bc4ac68cb415a43ac34d36afc2adc30307e6d37c
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73795267"
---
| Ресурс | базовая; | Standard | Premium |
|---|---|---|---|
| Хранилище<sup>1</sup> | 10 ГБ | 100 ГБ| 500 ГБ |
| Максимальный размер уровня изображения | 200 гиб | 200 гиб | 200 гиб |
| Операции чтения за минуту<sup>2, 3</sup> | 1000 | 3000 | 10 000 |
| Операции записи за минуту<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Пропускная способность скачивания в Мбит/с<sup>2</sup> | 30 | 60 | 100 |
| Пропускная способность передачи в Мбит/с<sup>2</sup> | 10 | 20 | 50 |
| Объекты Webhook | 2 | 10 | 100 |
| Георепликация | Недоступно | Недоступно | [Поддерживаются][geo-replication] |
| Доверие к содержимому | Недоступно | Недоступно | [Поддерживаются][content-trust] |
| Доступ к виртуальной сети | Недоступно | Недоступно | [Предварительная версия][vnet] |
| Разрешения в пределах репозитория | Недоступно | Недоступно | [Предварительная версия][token]|
| Маркеры &bull; | Недоступно | Недоступно | 20 000 |
| сопоставления области &bull; | Недоступно | Недоступно | 20 000 |
| &bull; репозиторий на карту области | Недоступно | Недоступно | 500 |


<sup>1</sup> Указанные ограничения хранилища — это объем хранилища, *включаемого* для каждого уровня. Дополнительная ежедневная плата взымается за ГиБ для хранилища изображений выше этих ограничений. Сведения о тарифах см. в разделе [цены на реестр контейнеров Azure][pricing].

<sup>2</sup>*операции чтения*, *Вритеопс*и *пропускная способность* являются минимальными оценками. Реестр контейнеров Azure стремится улучшить производительность по мере использования.

<sup>3</sup> [DOCKER Pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) преобразуется в несколько операций чтения на основе числа слоев в изображении, а также извлечения манифеста.

<sup>4</sup> Операция [DOCKER Push](https://docs.docker.com/registry/spec/api/#pushing-an-image) преобразуется в несколько операций записи в зависимости от числа слоев, которые необходимо отправить. Команда `docker push` включает *операции чтения* для получения манифеста для имеющегося образа.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md