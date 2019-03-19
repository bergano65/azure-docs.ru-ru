---
title: Известные проблемы с плагином Project Acoustics
titlesuffix: Azure Cognitive Services
description: При использовании Designer Preview для Project Acoustics вы можете столкнуться со следующими известными проблемами.
services: cognitive-services
author: kylestorck
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: resources
ms.date: 08/17/2018
ms.author: kylestorck
ms.openlocfilehash: 4cbede768409596365bea0cdbbb451cc5195ac4b
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58136265"
---
# <a name="project-acoustics-known-issues"></a>Проект шума известные проблемы
При использовании Designer Preview для Project Acoustics вы можете столкнуться со следующими известными проблемами.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Параметры акустики теряются при переименовании сцены

При переименовании сцены, все, что не акустических параметры, которые принадлежат к сцене будет автоматически передавать в сцену новый. Они будут по-прежнему существуют в старый файл ресурсов тем не менее. Найдите файл **SceneName_AcousticParameters.asset** внутри каталога **редактора** рядом со своим файлом сцены. Переименуйте файл, добавив имя новой сцены.

## <a name="unity-crashes-when-closing-project"></a>Unity аварийно завершает работу при закрытии проекта

В последних версиях Unity (2018.2 +) есть известная ошибка, когда происходит сбой Unity при закрытии проекта. Эта проблема Unity [обсуждается здесь](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="trouble-deploying-to-android"></a>Проблемы при развертывании в Android
Чтобы использовать Project Acoustics в Android, измените целевой объект сборки на Android. В некоторых версиях Unity имеется ошибка развертывания аудио подключаемых модулей. Убедитесь, что вы не используете версии, затронутых [Эта ошибка](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Я получаю ошибку о том, что не удалось найти файл метаданных System.Security.dll

Установите **.NET 4.x Equivalent** в качестве версии среды выполнения сценариев в параметрах проигрывателя, а затем перезапустите Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>У меня проблемы с проверкой подлинности при подключении к Azure

Еще раз проверьте, что вы использовали правильные учетные данные для своей учетной записи Azure, что ваша учетная запись поддерживает тип узла, запрошенного в сборке, и что системные часы точны.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>После отмены наложения состояние вкладки Bake (Наложение) изменится на "удаление".
Проект шума освободит все ресурсы Azure, для успешного завершения или отмены задания. Это может занять около 5 минут.

## <a name="next-steps"></a>Дальнейшие действия
* Попробуйте [Unity](unity-quickstart.md) или [Unreal](unreal-quickstart.md) пример содержимого

