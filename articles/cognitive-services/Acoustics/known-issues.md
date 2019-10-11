---
title: Известные проблемы с подключаемыми модулями акустических характеристик проекта
titlesuffix: Azure Cognitive Services
description: Вы можете столкнуться со следующими известными проблемами в акустических элементах проекта.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: b71b93f271608d946d964f70dae9eefbef77e87b
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72243042"
---
# <a name="project-acoustics-known-issues"></a>Известные проблемы с акустическими характеристиками проекта
В этой статье описываются проблемы, которые могут возникнуть при использовании акустических характеристик проекта.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Параметры акустики теряются при переименовании сцены

При переименовании сцены все акустические параметры, принадлежащие к этой сцене, не переносятся в новую сцену автоматически. Но они все еще существуют в старом файле ресурсов. Найдите файл *[сцененаме] _AcousticParameters. Asset* в каталоге *редактора* рядом с файлом сцены. Переименуйте файл в соответствии с новым именем сцены.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>Ошибки развертывания в Android из некоторых версий Unity

В некоторых версиях Unity есть [Ошибка](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) в том, как они развертывают подключаемые модули аудио в Android. Убедитесь, что вы не используете версию, затронутую этой ошибкой.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>Ошибка "не удается найти файл метаданных System. Security. dll"

Убедитесь, что **версия среды выполнения скриптов** в параметрах **проигрывателя** равна *.NET 4. x*, и перезапустите Unity.

## <a name="authentication-problems-when-connecting-to-azure"></a>Проблемы проверки подлинности при подключении к Azure

Проверьте следующее:
- Вы использовали правильные учетные данные для учетной записи Azure.
- Ваша учетная запись поддерживает тип узла, запрошенный в внедрить.
- Системные часы заданы правильно.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>На вкладке внедрить по-прежнему отображается «удаление» после отмены
Акустика проекта очищает все ресурсы Azure для задания после успешного завершения или отмены. Этот процесс может занять до 5 минут.

## <a name="next-steps"></a>Следующие шаги
* Испытайте [Unity](unity-quickstart.md) или [нереальный](unreal-quickstart.md) образец содержимого.
