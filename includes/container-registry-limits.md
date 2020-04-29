---
title: Включить имя файла
description: включить файл
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 03/11/2020
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 0090f02382e024e5539383328b55d58798002d63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79117146"
---
| Ресурс | Basic | Standard | Premium |
|---|---|---|---|
| Хранилище<sup>1</sup> | 10 ГБ | 100 ГиБ| 500 ГБ |
| Максимальный размер уровня изображения | 200 гиб | 200 гиб | 200 гиб |
| Операции чтения за минуту<sup>2, 3</sup> | 1000 | 3000 | 10 000 |
| Операции записи за минуту<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Пропускная способность скачивания в Мбит/с<sup>2</sup> | 30 | 60 | 100 |
| Пропускная способность передачи в Мбит/с<sup>2</sup> | 10 | 20 | 50 |
| Веб-перехватчики | 2 | 10 | 500 |
| Георепликация | Недоступно | Недоступно | [Поддерживается][geo-replication] |
| Доверие к содержимому | Недоступно | Недоступно | [Поддерживается][content-trust] |
| Доступ к виртуальной сети | Недоступно | Недоступно | [Предварительный просмотр][vnet] |
| Интеграция с частной ссылкой | Недоступно | Недоступно | [Предварительный просмотр][plink] |
| Ключи, управляемые клиентом | Недоступно | Недоступно | [Предварительный просмотр][cmk] |
| Разрешения в пределах репозитория | Недоступно | Недоступно | [Предварительный просмотр][token]|
| &bull;Описателе | Недоступно | Недоступно | 20 000 |
| &bull;Карты области | Недоступно | Недоступно | 20 000 |
| &bull;Число репозиториев на карту области | Недоступно | Недоступно | 500 |


<sup>1</sup> Указанные ограничения хранилища — это объем хранилища, *включаемого* для каждого уровня. Дополнительная ежедневная плата взымается за ГиБ для хранилища изображений выше этих ограничений. Сведения о тарифах см. в разделе [цены на реестр контейнеров Azure][pricing].

<sup>2</sup>*Операции чтения*, *операции записи* и *пропускная способность* — это минимальные оценки. Реестр контейнеров Azure стремится улучшить производительность по мере использования.

<sup>3</sup> [DOCKER Pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) преобразуется в несколько операций чтения на основе числа слоев в изображении, а также извлечения манифеста.

<sup>4</sup> Операция [DOCKER Push](https://docs.docker.com/registry/spec/api/#pushing-an-image) преобразуется в несколько операций записи в зависимости от числа слоев, которые необходимо отправить. Команда `docker push` включает *операции чтения* для получения манифеста для имеющегося образа.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
[vnet]: ../articles/container-registry/container-registry-vnet.md
[plink]: ../articles/container-registry/container-registry-private-link.md
[cmk]: ../articles/container-registry/container-registry-customer-managed-keys.md
[token]: ../articles/container-registry/container-registry-repository-scoped-permissions.md