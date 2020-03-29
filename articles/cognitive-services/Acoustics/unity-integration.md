---
title: Интеграция и развертывание проекта Acoustics Unity
titlesuffix: Azure Cognitive Services
description: В этой статье описывается, как интегрировать плагин Project Acoustics Unity в проект Unity.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: conceptual
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: a8ddb0e4ca2ee4396a25a70c8b60b653aebb72d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "72242999"
---
# <a name="project-acoustics-unity-integration"></a>Интеграция проекта Акустика Единство
В этой статье описывается, как интегрировать плагин Project Acoustics Unity в проект Unity.

Требования к программному обеспечению:
* [Unity 2018.2+](https://unity3d.com) для Windows;
* [Пакет «Единство акустики проекта»](https://www.microsoft.com/download/details.aspx?id=57346)

## <a name="import-the-plug-in"></a>Импорт плагина
1. Импортируйте акустику UnityPackage в ваш проект. 
 В Unity перейдите на**пользовательский пакет****импортного пакета** >  **активов.** > 

    ![Меню пакета импорта Unity](media/import-package.png)  

1. Выберите **ProjectAcoustics.unitypackage**.

1. Нажмите кнопку **Import** (Импорт), чтобы интегрировать пакет Unity в свой проект.

    ![Диалоговое окно Unity Import Package (Импорт пакетов в Unity)](media/import-dialog.png)  

Если вы импортируете плагин в существующий проект, ваш проект может уже иметь файл *mcs.rsp* в корне проекта. В этом файле указаны параметры компилятора C'. Слияние содержимого этого файла с файлом mcs.rsp, который поставляется с плагином Project Acoustics.

## <a name="enable-the-plug-in"></a>Включить плагин
Часть инструментария для выпечки требует .NET 4. *x* версия выполнения сценариев. Импорт пакета обновляет настройки игрока Unity. Перезапустите Unity, чтобы этот параметр вступил в силу.

![Панель настроек игроков Unity](media/player-settings.png)

![Панель настроек Unity Player с выбранным .NET 4.5](media/net45.png)

## <a name="set-up-audio-dsp"></a>Настройка аудио DSP
Проект Акустика включает в себя аудио время выполнения DSP, который интегрируется в unity аудио двигатель пространственизатор арамку. Она включает в себя как HRTF основе и панорамирования пространственности. Чтобы включить DSP в Project Acoustics, перейдите на **воспроизведение** > **настройки** > проекта**Audio,** чтобы открыть настройки звука Unity. Выберите **проект Акустика** в качестве **плагина Spatializer** для вашего проекта. Убедитесь, что **DSP Размер буфера** установлен на *лучшую производительность.*

![Меню параметров проекта Unity](media/project-settings.png)  

![Панель настроек Unity Spatializer с выбранным пространственизатором Project Acoustics](media/choose-spatializer.png)

Далее, откройте аудио смеситель (**Окно** > **аудио микшер**). Убедитесь, что у вас есть по крайней мере один миксер, с одной группой. Если у вас его нет, **+** выберите кнопку справа от **Mixers.** Щелкните правой кнопкой мыши нижнюю часть полосы канала в разделе эффектов и добавьте эффект **Microsoft Acoustics Mixer**. Поддерживается только один микшер Project Acoustics.

![Unity Audio Mixer хостинг проекта Акустика смеситель](media/audio-mixer.png)

## <a name="enable-acoustics-on-sound-sources"></a>Включить акустику на источники звука
Создайте источник звука: Выберите флажок **Spatialize** в нижней части панели инспектора AudioSource. Убедитесь, что **пространственная смесь** настроена на полный *3D.*  

![Панель Unity Audio Source](media/audio-source.png)

## <a name="enable-acoustic-design"></a>Включить акустический дизайн
Прикрепите сценарий *AcousticsAdjust* к источнику звука в вашей сцене, чтобы включить дополнительные параметры дизайна источника: Выберите **Компонент добавления** и выберите**Акустику** **Скриптов.** > 

![Скрипт AcousticsAdjust в Unity](media/acoustics-adjust.png)

## <a name="next-steps"></a>Дальнейшие действия
* [Испеките свою сцену с помощью проекта Акустика для единства](unity-baking.md).
* [Создайте учетную запись Azure Batch](create-azure-account.md) для испечь сцену в облаке.
* Изучите [процесс проектирования проекта Acoustics Unity.](unity-workflow.md)
