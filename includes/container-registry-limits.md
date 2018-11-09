---
title: включение файла
description: включение файла
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 08/30/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4021d1ac5a0b3d9d9bf19e9fb7f74952f2471c30
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2018
ms.locfileid: "49458874"
---
| Ресурс | базовая; | Стандартная | Премиум |
|---|---|---|---|---|
| Хранилище<sup>1</sup> | 10 ГБ | 100 ГБ| 500 ГБ |
| Максимальный размер уровня образа | 20 ГиБ | 20 ГиБ | 50 ГиБ |
| Операции чтения за минуту<sup>2, 3</sup> | 1000 | 3000 | 10 000 |
| Операции записи за минуту<sup>2, 4</sup> | 100 | 500 | 2 000 |
| Пропускная способность скачивания в Мбит/с<sup>2</sup> | 30 | 60 | 100 |
| Пропускная способность передачи в Мбит/с<sup>2</sup> | 10 | 20 | 50 |
| Объекты Webhook | 2 | 10 | 100 |
| Георепликация | Недоступно | Недоступно | [Поддерживается][geo-replication] |
| Доверие к содержимому (предварительная версия) | Недоступно | Недоступно | [Поддерживается][content-trust] |

<sup>1</sup> Указанные пределы хранения — это количество *включенных* хранилищ для каждого уровня. Дополнительная ежедневная плата взымается за ГиБ для хранилища изображений выше этих ограничений. Дополнительные сведения см. в статье [Цены на реестр контейнеров Azure][pricing].

<sup>2</sup> *Операции чтения*, *операции записи* и *пропускная способность* — это минимальные оценки. ACR стремится к повышению производительности по мере использования.

<sup>3</sup> [docker pull](https://docs.docker.com/registry/spec/api/#pulling-an-image) преобразовывает несколько операций чтения в зависимости от количества слоев изображения, а также извлечение манифеста.

<sup>4</sup> [docker push](https://docs.docker.com/registry/spec/api/#pushing-an-image) преобразовывает несколько операций записи, исходя из количества слоев, которые нужно отправить. Команда `docker push` включает *операции чтения* для получения манифеста для имеющегося образа.

<!-- LINKS - External -->
[pricing]: https://azure.microsoft.com/pricing/details/container-registry/

<!-- LINKS - Internal -->
[geo-replication]: ../articles/container-registry/container-registry-geo-replication.md
[content-trust]: ../articles/container-registry/container-registry-content-trust.md