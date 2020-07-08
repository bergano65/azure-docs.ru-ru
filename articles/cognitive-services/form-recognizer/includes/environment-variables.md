---
title: Переменные среды
description: настройка переменных среды
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 05/06/2020
ms.author: pafarley
ms.openlocfilehash: 28362a81461b63440ad752071f11b3603a979995
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86100317"
---
Используя ключ и конечную точку из созданного ресурса, создайте две переменные среды для проверки подлинности:

* `FORM_RECOGNIZER_KEY` — ключ ресурса для проверки подлинности запросов.
* `FORM_RECOGNIZER_ENDPOINT` — конечная точка ресурса для отправки запросов API. Она должна выглядеть так: 
  * `https://<your-custom-subdomain>.cognitiveservices.azure.com`

>[!NOTE]
> Конечные точки для ресурсов не из пробной версии, созданных после 1июля 2019 г., поддерживают пользовательский формат поддомена, показанный ниже. Дополнительные сведения и полный список региональных конечных точек см. в статье [Custom subdomain names for Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains) (Пользовательские имена поддоменов для Cognitive Services). 

Для настройки переменных среды в своей операционной системе воспользуйтесь инструкциями ниже.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx FORM_RECOGNIZER_KEY <replace-with-your-form-recognizer-key>
setx FORM_RECOGNIZER_ENDPOINT <replace-with-your-form-recognizer-endpoint>
```

Добавив переменные среды, закройте и снова откройте окно консоли.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

После добавления переменной среды запустите `source ~/.bashrc` из окна консоли, чтобы применить изменения.

#### <a name="macos"></a>[macOS](#tab/unix)

Измените `.bash_profile` и добавьте переменную среды:

```bash
export FORM_RECOGNIZER_KEY=<replace-with-your-product-name-key>
export FORM_RECOGNIZER_ENDPOINT=<replace-with-your-product-name-endpoint>
```

После добавления переменных среды запустите `source .bash_profile` из окна консоли, чтобы применить изменения.
***
