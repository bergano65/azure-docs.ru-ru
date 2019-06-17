---
title: включение файла
description: включение файла
services: digital-twins
author: kingdomofends
ms.service: digital-twins
ms.topic: include
ms.date: 01/11/2019
ms.author: adgera
ms.custom: include file
ms.openlocfilehash: ac6b008597b6d6e557a0cc412c00c2202231bc3d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66157910"
---
> [!NOTE]
> Для составных запросов обычно требуются три компонента:
> * Заголовок **Content-Type**:
>   * `application/json; charset=utf-8`
>   * `multipart/form-data; boundary="USER_DEFINED_BOUNDARY"`
> * Объект **Content-Disposition**:
>   * `form-data; name="metadata"`
> * Содержимое файла для отправки.
>
> Применяемые **Content-Type** и **Content-Disposition** будут различаться в зависимости от сценария использования.

Составные запросы выполняются программно (с использованием C#), с помощью клиента REST или средства, например [Postman](https://docs.microsoft.com/azure/digital-twins/how-to-configure-postman#multi). Клиентские средства REST могут предоставлять различные уровни поддержки для сложных составных запросов. Параметры конфигурации также могут немного отличаться в зависимости от средства. Проверьте, какое средство лучше всего подходит для вас.

> [!IMPORTANT]
> Составные запросы к API управления Azure Digital Twins, как правило, состоят из двух частей:
> * метаданные больших двоичных объектов (такие как связанный тип MIME), которые объявлены с помощью **Content-Type** и/или **Content-Disposition**;
> * содержимое большого двоичного объекта, в том числе неструктурированное содержимое файла для отправки.
>
> Ни одна из двух частей не требуется для запросов **PATCH**. Они необходимы для **POST** или операций создания.

[Исходный код для быстрого определения степени заполненности](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/api/update.cs) содержит полные примеры C# для отправки составных запросов к API управления Azure Digital Twins.