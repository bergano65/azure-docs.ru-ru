---
title: включить файл
description: включить файл
services: azure-resource-manager
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: include
ms.date: 01/03/2020
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 6d8f29e9272f3e05bb585c032240cce67c37d2bf
ms.sourcegitcommit: 2c59a05cb3975bede8134bc23e27db5e1f4eaa45
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/05/2020
ms.locfileid: "75665146"
---
1. Чтобы просмотреть теги для ресурса или группы ресурсов, найдите существующие теги в разделе Обзор. Если теги ранее не применялись, то список будет пустым.
   
     ![Просмотр тегов для ресурса или группы ресурсов](./media/resource-manager-tag-resources/view-tags.png)
1. Чтобы добавить тег, выберите **Click here to add tags** (Щелкните здесь, чтобы добавить теги).

1. Укажите имя и значение. Выберите **+** для добавления тега.
   
     ![Добавление тега](./media/resource-manager-tag-resources/add-tag.png)
1. При необходимости продолжайте добавлять теги. Затем нажмите кнопку **Сохранить**.
   
     ![Сохранение тегов](./media/resource-manager-tag-resources/save-tags.png)
1. Теперь теги отображаются в обзоре.
   
     ![Отображение тегов](./media/resource-manager-tag-resources/view-new-tags.png)
1. Чтобы добавить или удалить тег, выберите **Изменить**.
   
1. Чтобы удалить тег, щелкните значок корзины. Затем нажмите кнопку **Сохранить**.

     ![Удаление тега](./media/resource-manager-tag-resources/delete-tag.png)


Вот как можно выполнить пакетное назначение тегов нескольким ресурсам.

1. В любом списке ресурсов установите флажки для ресурсов, которым требуется назначить тег.

     ![Выбор нескольких ресурсов](./media/resource-manager-tag-resources/select-multiple-resources.png)

1. Выберите **Назначить теги**.

     ![Назначение тегов](./media/resource-manager-tag-resources/assign-tags.png)

1. После каждого имени и значения выберите **+** . По завершении щелкните **Назначить**.

     ![Выбор элемента "Назначить"](./media/resource-manager-tag-resources/select-assign.png)

Чтобы просмотреть все ресурсы с тегом, сделайте следующее.

1. В меню портала Azure выберите **Все службы**. Выберите **Общие**, а затем — **теги**.

     ![Поиск по тегу](./media/resource-manager-tag-resources/find-tags-general.png)

1. Выберите тег для просмотра ресурсов.

     ![Выбор тега](./media/resource-manager-tag-resources/select-tag.png)

1. Будут отображены все ресурсы с этим тегом.

     ![Просмотр ресурсов по тегу](./media/resource-manager-tag-resources/view-resources-by-tag.png)
