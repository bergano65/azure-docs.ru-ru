---
title: Интеграция с Unity шума и развертывания проекта
titlesuffix: Azure Cognitive Services
description: В этом практическом руководстве объясняется интеграция Unity шума проекта подключаемого модуля в проект Unity.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: how-to
ms.date: 03/20/2019
ms.author: kegodin
ms.openlocfilehash: aa7c5c513d65310bf9bffab29c1d18e7e7a85b49
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60389514"
---
# <a name="project-acoustics-unity-integration"></a>Интеграция с Unity шума проекта
В этом практическом руководстве объясняется интеграция Unity шума проекта подключаемого модуля в проект Unity.

Требования к программному обеспечению:
* [Unity 2018.2+](http://unity3d.com) для Windows;
* [Пакет проекта Unity шума](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plugin"></a>Импорт подключаемого модуля
Импортируйте пакет акустики UnityPackage в свой проект. 
* В Unity перейдите в раздел **Assets > Import Package > Custom Package...** (Ресурсы > Импорт пакета > Пользовательский пакет...).

    ![Снимок экрана из Unity Импорт пакета меню](media/import-package.png)  

* Выберите пакет **ProjectAcoustics.unitypackage**.

Если вы импортируете подключаемый модуль в имеющийся проект, в его корне уже может присутствовать файл **mcs.rsp**, в котором указаны параметры для компилятора C#. Вам необходимо объединить содержимое этого файла с файлом mcs.rsp, входящим в состав подключаемого модуля Project Acoustics.

## <a name="enable-the-plugin"></a>Включение подключаемого модуля
Часть сборки инструментария акустики требует версии среды выполнения сценариев .NET 4.x. В результате импорта пакетов обновятся параметры проигрывателя Unity. Перезапустите Unity, чтобы этот параметр вступил в силу.

![Снимок экрана с панелью параметров проигрывателя в Unity](media/player-settings.png)

![Снимок экрана с выбором .NET 4.5 на панели параметров проигрывателя в Unity](media/net45.png)

## <a name="set-up-audio-dsp"></a>Настройка аудио DSP
Шума проект включает в себя выполнения аудио DSP, которое интегрируется в инфраструктуру spatializer аудио ядра Unity. Он включает в себя как HRTF и панорамирования spatialization. Включить DSP шума проекта, открыв Параметры звука Unity с помощью **изменить > Параметры проекта > аудио**, выбрав **шума проекта** как **подключаемый модуль Spatializer** для вашего проекта. Убедитесь, что **размер буфера DSP** присваивается наилучшей производительности.

![Параметры снимка экрана из Unity проекта панели](media/project-settings.png)  

![Снимок экрана Unity Spatializer панель параметров с spatializer шума проекта выбран](media/choose-spatializer.png)

Затем откройте Mixer аудио (**окна > Mixer аудио**). Убедитесь, что у вас есть хотя бы один микшер с одной группой. Если его нет, нажмите кнопку "+" справа от раздела **Mixers** (Микшеры). Щелкните правой кнопкой мыши нижней полосы канала в разделе эффекты и добавьте **Mixer шума проекта** эффект. Обратите внимание, что одновременно поддерживается только один микшер Project Acoustics.

![Снимок экрана из Unity аудио Mixer размещение проекта шума mixer](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Включить шума в звуковой источников
Создайте источник звука. Установите флажок в нижней части панели проверки AudioSource **Spatialize** (Пространственный звук). Для параметра **Spatial Blend** (Пространственное смешивание) выберите значение 3D.  

![Снимок экрана из Unity аудио источника панели](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Включить акустических разработки
Присоединить сценарий **AcousticsAdjust** к источнику звукового в сцене для включения дополнительных исходных параметров конструктора, щелкнув **добавить компонент** и выбрав **сценарии > настроить шума** :

![Снимок экрана со скриптом AcousticsAdjust в Unity](media/acoustics-adjust.png)

## <a name="next-steps"></a>Дальнейшие действия
* [Внедрение в сцену с шума проекта для Unity](unity-baking.md)
* [Создание учетной записи пакетной службы Azure](create-azure-account.md) внедрим сцены в облаке
* Изучите [процесса разработки проекта Unity шума](unity-workflow.md).

