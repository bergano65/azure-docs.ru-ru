---
title: Известные проблемы с плагином Project Acoustics
titlesuffix: Azure Cognitive Services
description: При использовании Designer Preview для Project Acoustics вы можете столкнуться со следующими известными проблемами.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: 37084480423de90f50beced187eda202b39f8bf1
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68933067"
---
# <a name="project-acoustics-known-issues"></a>Известные проблемы с акустическими характеристиками проекта
При использовании Designer Preview для Project Acoustics вы можете столкнуться со следующими известными проблемами.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Параметры акустики теряются при переименовании сцены

При переименовании сцены все акустические параметры, принадлежащие к этой сцене, не будут автоматически переданы в новую сцену. Однако они по-прежнему будут находиться в старом файле ресурсов. Найдите файл **SceneName_AcousticParameters.asset** внутри каталога **редактора** рядом со своим файлом сцены. Переименуйте файл, добавив имя новой сцены.

## <a name="deploying-to-android-from-some-unity-versions"></a>Развертывание на Android из некоторых версий Unity

В некоторых версиях Unity есть ошибка с развертыванием подключаемых модулей аудио в Android. Убедитесь, что вы не используете версию, затронутую [этой ошибкой](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Я получаю ошибку о том, что не удалось найти файл метаданных System.Security.dll

Установите **.NET 4.x Equivalent** в качестве версии среды выполнения сценариев в параметрах проигрывателя, а затем перезапустите Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>У меня проблемы с проверкой подлинности при подключении к Azure

Еще раз проверьте, что вы использовали правильные учетные данные для своей учетной записи Azure, что ваша учетная запись поддерживает тип узла, запрошенного в сборке, и что системные часы точны.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>После отмены наложения состояние вкладки Bake (Наложение) изменится на "удаление".
Акустика проекта очистит все ресурсы Azure для задания при успешном завершении или отмене. Это может занять до 5 минут.

## <a name="next-steps"></a>Следующие шаги
* Испытайте [Unity](unity-quickstart.md) или [Unreal](unreal-quickstart.md) образец содержимого

