---
title: включение файла
description: включение файла
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 05/14/2019
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: ee8ff3529524a63ca2e54a64327570197f363538
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2019
ms.locfileid: "67185651"
---
| Resource | Стандартная | Стандарт | Премиум |
|---|---|---|---|
| Хранилище<sup>1</sup> | 10 ГБ | 100 ГБ| 500 ГБ |
| Максимальный размер уровня изображения | 200 гиб | 200 гиб | 200 гиб |
| Операции чтения за минуту<sup>2, 3</sup> | 1000 | 3000 | 10 000 |
| Операции записи за минуту<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Пропускная способность скачивания в Мбит/с<sup>2</sup> | 30 | 60 | 100 |
| Пропускная способность передачи в Мбит/с<sup>2</sup> | 10 | 20 | 50 |
| Веб-перехватчики | 2 | 10 | 100 |
| Георепликация | Н/Д | Н/Д | [Поддерживаются][geo-replication] |
| Доверие к содержимому | Н/Д | Н/Д | [Поддерживаются][content-trust] |

<sup>1</sup> Указанные ограничения хранилища — это объем хранилища, *включаемого* для каждого уровня. Дополнительная ежедневная плата взымается за ГиБ для хранилища изображений выше этих ограничений. Сведения о тарифах см. в разделе [цены на реестр контейнеров Azure][pricing].

<sup>2</sup> *Операции чтения*, *Вритеопс*и *пропускная способность* являются минимальными оценками. Реестр контейнеров Azure стремится улучшить производительность по мере использования.

<sup>3</sup> [DOCKER Pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) преобразуется в несколько операций чтения на основе числа слоев в изображении, а также извлечения манифеста.

<sup>4</sup> Операция [DOCKER Push](https://docs.docker.com/registry/spec/api/#pushing-an-image) преобразуется в несколько операций записи в зависимости от числа слоев, которые необходимо отправить. Команда `docker push` включает *операции чтения* для получения манифеста для имеющегося образа.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md
