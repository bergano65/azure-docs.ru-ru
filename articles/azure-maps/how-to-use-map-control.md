---
title: Как использовать библиотеку Map Control в службе "Карты Azure" | Документация Майкрософт
description: Узнайте, как использовать клиентскую библиотеку Javascript Map Control в службе "Карты Azure".
author: dsk-2015
ms.author: dkshir
ms.date: 10/08/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 850f9b28c112c11fd98a8abc81a1811cd26d81cc
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49166038"
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

3. Чтобы отобразить новую карту в браузере, добавьте ссылку **#map** в элемент `<style>`.

    ```html
    <style>
        #map {
            width: 100%;
            height: 100%;
        }
    </style>
    ```

4. Для инициализации элемента управления картой определите новый раздел в тексте html и создайте сценарий. Используйте в скрипте ключ своей учетной записи службы "Карты Azure". Если вам нужно создать учетную запись или найти ключ, см. статью об [управлении учетной записью и ключами в Azure Maps](how-to-manage-account-keys.md). Метод **setLanguage** задает язык, который используется для метки схемы и элементов управления. Полный список поддерживаемых языков см. в разделе [Поддерживаемые языки](https://docs.microsoft.com/azure/azure-maps/supported-languages).

    ```html
    <div id="map">
        <script>
            atlas.setSubscriptionKey("<_your account key_>");
            atlas.setLanguage("en");
            var map = new atlas.Map("map", {
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
