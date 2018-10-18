---
title: Как использовать библиотеку Map Control в службе "Карты Azure" | Документация Майкрософт
description: Узнайте, как использовать клиентскую библиотеку Javascript Map Control в службе "Карты Azure".
author: dsk-2015
ms.author: dkshir
ms.date: 09/05/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 45890b4bd474c010b2b086be0405b79d340aeebd
ms.sourcegitcommit: 616e63d6258f036a2863acd96b73770e35ff54f8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2018
ms.locfileid: "45603165"
---
# <a name="use-the-azure-maps-map-control"></a>Использование библиотеки Map Control в службе Azure Maps

Клиентская библиотека Javascript Map Control позволяет отображать карты и встроенные функциональные возможности службы "Карты Azure" в вашем мобильном или веб-приложении.

## <a name="create-a-new-map-in-a-web-page"></a>Создание карты на веб-странице

Вы можете встроить карту в веб-страницу с помощью клиентской библиотеки Javascript службы Map Control.

1. Создайте файл с именем **MapSearch.html**.

2. Добавьте ссылки на стили и источник сценария службы "Карты Azure" в элемент `<head>` файла:

    ```html
    <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1" type="text/css" />
    <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1"></script>
    ```

3. Чтобы отобразить новую карту в браузере, добавьте ссылку **#map** в элемент `<style>`:

    ```html
    <style>
        #map {
            width: 100%;
            height: 100%;
        }
    </style>
    ```

4. Для инициализации элемента управления карты определите новый раздел в тексте html и создайте сценарий. Используйте в скрипте ключ своей учетной записи службы "Карты Azure". Если вам нужно создать учетную запись или найти ключ, см. статью об [управлении учетной записью и ключами в Azure Maps](how-to-manage-account-keys.md).

    ```html
    <div id="map">
        <script>
            var MapsAccountKey = "<_your account key_>";
            var map = new atlas.Map("map", {
                "subscription-key": MapsAccountKey,
                center: [-122.33263,47.59093],
                zoom: 12
            });
        </script>
    </div>
    ```

5. Откройте файл в веб-браузере и вы увидите готовую для просмотра карту.

## <a name="next-steps"></a>Дополнительная информация

Узнайте, как создать карту, используя полный пример:

> [!div class="nextstepaction"]
> [Create a map](map-create.md) (Создание карты)

Узнайте, как изменить стиль карты:

> [!div class="nextstepaction"]
> [Выбор стиля карты](choose-map-style.md)
