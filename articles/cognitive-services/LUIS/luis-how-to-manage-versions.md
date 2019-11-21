---
title: Manage versions - LUIS
titleSuffix: Azure Cognitive Services
description: Версии позволяют создавать и публиковать различные модели. Прежде чем вносить изменения в активную модель рекомендуется клонировать ее текущую в другую версию приложения.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: diberry
ms.openlocfilehash: 138b84a9b7f54782fd6254304a3fdcf4dba83182
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74221925"
---
# <a name="use-versions-to-edit-and-test-without-impacting-staging-or-production-apps"></a>Использование версий для изменения и проверки без влияния на промежуточные или рабочие приложения

Версии позволяют создавать и публиковать различные модели. Прежде чем вносить изменения в активную модель рекомендуется клонировать ее текущую в [другую](luis-concept-version.md) версию приложения. 

Для работы с версиями откройте приложение, выбрав его имя на странице **Мои приложения**, на верхней панели выберите **Управление**, а затем нажмите **Версии** левой панели навигации. 

The list of versions shows which versions are published, where they are published, and which version is currently active. 

> [!div class="mx-imgBorder"]
> [![Manage section, versions page](./media/luis-how-to-manage-versions/versions-import.png "раздел "Управление", страница "Версии"")](./media/luis-how-to-manage-versions/versions-import.png#lightbox)

## <a name="clone-a-version"></a>Клонирование версии

1. Выберите версию, которую необходимо клонировать, а затем на панели инструментов нажмите **Клонировать**. 

2. В диалоговом окне**Версия клона** введите имя для новой версии, например "0,2".

   ![Диалоговое окно "Версия клона"](./media/luis-how-to-manage-versions/version-clone-version-dialog.png)
 
     > [!NOTE]
     > Идентификатор версии может состоять только из символов, цифр или точки ".". Его длина не может превышать 10 символов.
 
   Новая версия с указанным именем создается и настраивается как активная версия.

## <a name="set-active-version"></a>Настройте активную версию

Select a version from the list, then select **Activate** from the toolbar. 

> [!div class="mx-imgBorder"]
> [![Manage section, versions page, make a version action](./media/luis-how-to-manage-versions/versions-other.png "Manage section, versions page, make a version action")](./media/luis-how-to-manage-versions/versions-other.png#lightbox)

## <a name="import-version"></a>Импорт версии

You can import a `.json` or a `.lu` version of your application.

1. Select **Import** from the toolbar, then select the format. 

2. В всплывающем окне **Импорт новой версии** введите имя новой версии, которое должно состоять из 10 символов. You only need to set a version ID if the version in the file already exists in the app.

    ![Раздел "Управление", страница "Версии", импорт новой версии](./media/luis-how-to-manage-versions/versions-import-pop-up.png)

    После импорта версии она становится активной.

### <a name="import-errors"></a>Import errors

* Tokenizer errors: If you get a **tokenizer error** when importing, you are trying to import a version that uses a different [tokenizer](luis-language-support.md#custom-tokenizer-versions) than the app currently uses. To fix this, see [Migrating between tokenizer versions](luis-language-support.md#migrating-between-tokenizer-versions).

<a name = "export-version"></a>

## <a name="other-actions"></a>Другие действия

* Чтобы **удалить** версию выберите ее из списка, а затем на панели инструментов нажмите **Удалить**. Нажмите кнопку **ОК**. 
* Чтобы **переименовать** версию выберите ее из списка, а затем на панели инструментов нажмите **Переименовать**. Введите новое имя и выберите **Готово**. 
* Чтобы **экспортировать** версию выберите ее из списка, а затем на панели инструментов нажмите **Экспортировать приложение**. Choose JSON to export for backup, choose **Export for container** to [use this app in a LUIS container](luis-container-howto.md).  

