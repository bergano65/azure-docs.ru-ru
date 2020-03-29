---
title: Проект Акустика плагин известных проблем
titlesuffix: Azure Cognitive Services
description: Вы можете испытать следующие известные проблемы в проекте акустики.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72243042"
---
# <a name="project-acoustics-known-issues"></a>Проект Акустика известных проблем
В этой статье описаны проблемы, которые могут возникнуть при использовании акустики project Acoustics.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Параметры акустики теряются при переименовании сцены

Если переименовать сцену, все акустические параметры, которые принадлежат этой сцене, автоматически не переходят в новую сцену. Но они все еще существуют в старом файле активов. Ищите файл *«SceneName» _AcousticParameters.актива* в каталоге *редактора* рядом с файлом сцены. Переименуйте файл, чтобы отразить новое название сцены.

## <a name="deploy-to-android-bug-from-some-unity-versions"></a>Развертывание на Android ошибка из некоторых версий Unity

Некоторые версии Unity имеют [ошибку](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player) в том, как они развертывают аудио плагины для Android. Убедитесь, что вы не используете версию, которая влияет на эту ошибку.

## <a name="could-not-find-metadata-file-systemsecuritydll-error"></a>"Не удалось найти ошибку файла метаданных System.Security.dll"

Убедитесь, что **версия выполнения сценариев** в настройках **игрока** эквивалентна *.NET 4.x*и перезапустите Unity.

## <a name="authentication-problems-when-connecting-to-azure"></a>Проблемы аутентификации при подключении к Azure

Проверьте следующие моменты:
- Вы использовали правильные учетные данные для учетной записи Azure.
- Ваша учетная запись поддерживает тип узла, который вы запросили в испечении.
- Ваши системные часы настроены правильно.

## <a name="the-bake-tab-still-shows-deleting-after-you-cancel"></a>Вкладка Bake по-прежнему показывает "удаляние" после отмены
Project Acoustics очищает все ресурсы Azure для работы после успешного завершения или отмены. Этот процесс может занять до 5 минут.

## <a name="next-steps"></a>Дальнейшие действия
* Попробуйте содержание образца [Unity](unity-quickstart.md) или [Unreal.](unreal-quickstart.md)
