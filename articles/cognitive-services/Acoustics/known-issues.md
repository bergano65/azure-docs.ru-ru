---
title: Известные проблемы с плагином Project Acoustics
titlesuffix: Azure Cognitive Services
description: При использовании Designer Preview для Project Acoustics вы можете столкнуться со следующими известными проблемами.
services: cognitive-services
author: kylestorck
manager: cgronlun
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 08/17/2018
ms.author: kylestorck
ms.openlocfilehash: 02f56306110e904f41c1b94346c58b33ce0b575c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55156196"
---
# <a name="known-issues"></a>Известные проблемы
При использовании Designer Preview для Project Acoustics вы можете столкнуться со следующими известными проблемами.

## <a name="acoustic-parameters-are-lost-when-you-rename-a-scene"></a>Параметры акустики теряются при переименовании сцены

При переименовании сцены все параметры акустики, относящиеся к этой сцене, не будут автоматически перенесены в новую сцену, но останутся в прежнем файле ресурса. Найдите файл **SceneName_AcousticParameters.asset** внутри каталога **редактора** рядом со своим файлом сцены. Переименуйте файл, добавив имя новой сцены.

## <a name="runtime-voxels-are-a-different-size-than-scene-preview-voxels"></a>Объемные пиксели во время выполнения имеют другой размер, чем объемные пиксели при предварительном просмотре сцены.

Если вы выполните **расчет** на вкладке **проб** и просмотрите объемные пиксели, а затем выполните сборку сцены и просмотрите объемные пиксели в среде выполнения для этой же сцены, то увидите, что их размеры отличаются. Объемные пиксели, показанные во время предварительного проектирования, — это пиксели, используемые в моделировании. Объемные пиксели, показанные в среде выполнения, используются для интерполяции между точками размещения проб. Это может привести к несогласованности, когда порталы будут казаться открытыми во время выполнения, но на самом деле будут закрыты.

## <a name="unity-crashes-when-closing-project"></a>Unity аварийно завершает работу при закрытии проекта

В последних версиях Unity (2018.2 +) есть известная ошибка, когда происходит сбой Unity при закрытии проекта. Эта проблема Unity [обсуждается здесь](https://issuetracker.unity3d.com/issues/crash-on-assetdatabase-getassetimporterversions-when-closing-a-specific-unity-project).

## <a name="trouble-deploying-to-android"></a>Проблемы при развертывании в Android
Чтобы использовать Project Acoustics в Android, измените целевой объект сборки на Android. В некоторых версиях Unity имеется ошибка развертывания аудиомодулей. Вам нужно убедиться, что вы используете версию, в которой не возникает [эта ошибка](https://issuetracker.unity3d.com/issues/android-ios-audiosource-playing-through-google-resonance-audio-sdk-with-spatializer-enabled-does-not-play-on-built-player).

## <a name="i-get-an-error-that-could-not-find-metadata-file-systemsecuritydll"></a>Я получаю ошибку о том, что не удалось найти файл метаданных System.Security.dll

Установите **.NET 4.x Equivalent** в качестве версии среды выполнения сценариев в параметрах проигрывателя, а затем перезапустите Unity.

## <a name="im-having-authentication-problems-when-connecting-to-azure"></a>У меня проблемы с проверкой подлинности при подключении к Azure

Еще раз проверьте, что вы использовали правильные учетные данные для своей учетной записи Azure, что ваша учетная запись поддерживает тип узла, запрошенного в сборке, и что системные часы точны.

## <a name="canceling-a-bake-leaves-the-bake-tab-in-deleting-state"></a>После отмены наложения состояние вкладки Bake (Наложение) изменится на "удаление".
Project Acoustics очистит все ресурсы Azure, которые были направлены для завершения или отмены задания. Это займет не более 5 минут.

## <a name="next-steps"></a>Дополнительная информация
* Приступите к работе с [интеграцией акустики в проект Unity](getting-started.md).

