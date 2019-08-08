---
title: Краткое руководство по использованию Project Acoustics с Unreal
titlesuffix: Azure Cognitive Services
description: Поэкспериментируйте с элементами управления для проектирования Project Acoustics в Unreal и Wwise, используя пример содержимого, и выполните развертывание в Windows Desktop.
services: cognitive-services
author: kegodin
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: kegodin
ROBOTS: NOINDEX
ms.openlocfilehash: 06023b2758d09fe8ebe7c1301ef1a03d9c54aa41
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704768"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Краткое руководство по использованию Project Acoustics с Unreal и Wwise
В этом кратком руководстве вы будете экспериментировать с элементами управления для проектирования Project Acoustics, используя предоставленный пример содержимого для Unreal Engine и Wwise.

Требования к программному обеспечению:
* [Unreal Engine](https://www.unrealengine.com/) 4.21;
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.6.

## <a name="download-the-sample-package"></a>Загрузка примера пакета
Скачайте [пример пакета Project Acoustics для Unreal и Wwise](https://www.microsoft.com/download/details.aspx?id=58090). Пример пакета содержит проект Unreal Engine, проект Wwise для этого проекта Unreal и подключаемый модуль Wwise для Project Acoustics.

## <a name="set-up-the-project-acoustics-sample-project"></a>Настройка примера проекта Project Acoustics
Чтобы настроить пример проекта Unreal/Wwise для Project Acoustics, необходимо сначала установить подключаемый модуль Project Acoustics в Wwise. Затем разверните двоичные файлы Wwise в проекте Unreal и настройте в подключаемом модуле Wwise Unreal поддержку Project Acoustics.

### <a name="install-the-project-acoustics-wwise-plugin"></a>Установка подключаемого модуля Wwise для Project Acoustics
Откройте средство запуска Wwise, а затем на вкладке **Plugins** (Подключаемые модули) в разделе **Install New Plugins** (Установка новых подключаемых модулей) выберите **Add From Directory** (Добавить из каталога). Выберите каталог `AcousticsWwisePlugin\ProjectAcoustics`, содержащийся в скачанном пакете.

![Снимок экрана со средством запуска Wwise — выбор установки подключаемого модуля Wwise](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Добавление двоичных файлов Wwise в пример проекта Unreal для Project Acoustics
В средстве запуска Wwise перейдите на вкладку **Unreal Engine**, а затем щелкните меню с тремя полосками рядом с разделом **Recent Unreal Engine Projects** (Последние проекты Unreal Engine) и выберите **Browse for project** (Поиск проекта). Откройте файл с примером проекта Unreal `.uproject` в пакете `AcousticsSample\AcousticsGame\AcousticsGame.uproject`.

![Снимок экрана со средством запуска Wwise — вкладка Unreal Engine](media/wwise-unreal-tab.png)

Затем рядом с примером пакета Project Acoustics щелкните **Integrate Wwise into Project...** (Интегрировать Wwise в Project...).

![Снимок экрана со средством запуска Wwise — проект Unreal для Project Acoustics](media/wwise-acoustics-game-project.png)

### <a name="extend-wwises-unreal-plugin-functionality"></a>Расширение функциональных возможностей подключаемого модуля Unreal для Wwise
Для подключаемого модуля Unreal для Project Acoustics необходимо предоставить дополнительное поведение из API подключаемого модуля Unreal для Wwise. Запустите пакетный файл, предоставленный с подключаемым модулем Unreal для Project Acoustics, чтобы автоматизировать эти изменения:
* В `AcousticsGame\Plugins\ProjectAcoustics\Resources` выполните файл `PatchWwise.bat`.

    ![Снимок экрана с проводником Windows окно — скрипт для исправления проекта Wwise](media/patch-wwise-script.png)

* Если пакет SDK DirectX не установлен, в зависимости от используемой версии Wwise, возможно, потребуется закомментировать строку, содержащую `DXSDK_DIR` в `AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs`:

    ![Снимок экрана с редактором кода — комментирование строки DXSDK](media/directx-sdk-comment.png)

### <a name="open-the-unreal-project"></a>Откройте проект Unreal. 
Вам будет предложено перестроить модули. Щелкните Yes (Да).

>Если происходит сбой открытия проекта из-за сбоев сборки, убедитесь, что для Project Acoustics установлен подключаемый модуль Wwise той же версии, которая использовалась в примере проекта Project Acoustics.

>Если [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2018.1.6 не используется, вам потребуется заново создать библиотеки звуков до проигрывания аудио в примере проекта.

## <a name="experiment-with-project-acoustics-design-controls"></a>Экспериментирование с элементами управления для проектирования Project Acoustics
Послушайте, как звучит сцена, нажав кнопку воспроизведения в редакторе Unreal. На компьютере используйте для перемещения клавиши W, A, S, D и мышь. Чтобы просмотреть сочетания клавиш для дополнительных элементов управления, нажмите клавишу **F1**. Ниже приведены некоторые действия проектирования, которые можно опробовать.

### <a name="modify-occlusion-and-transmission"></a>Изменение окклюзии и передачи
В каждом звуковом субъекте Unreal есть элементы управления проектирования Project Acoustics для каждого источника:

![Снимок экрана с редактором Unreal — элементы управления для проектирования Project Acoustics](media/demo-scene-sound-source-design-controls.png)

Если коэффициент **окклюзии** больше чем 1 (по умолчанию — 1), окклюзия будет преувеличена. Если задать значение меньше чем 1, эффект окклюзии будет более слабым.

Чтобы обеспечить передачу через стены, переместите ползунок **Transmission (dB)** (Передача (дБ)) с самого низкого уровня. 

### <a name="modify-wetness-for-a-source"></a>Изменение влажности для источника
Чтобы изменить скорость изменения влажности с расстоянием, используйте параметр **Perceptual Distance Warp** (Ощущаемое искажение расстояния). Project Acoustics на основе моделирования вычисляет во всем пространстве уровни влажности, которые плавно изменяются с расстоянием и обеспечивают признаки удаленности расстояния. Увеличение значения искажения расстояния повышает этот эффект за счет увеличения уровней влажности, связанных с расстоянием. Если задать для искажения значение меньше 1, изменение реверберации на основе расстояния будет более слабым. Этот эффект можно настраивать точнее, перемещая ползунок **Wetness (dB)** (Влажность (дБ)).

Увеличьте время затухания во всем пространстве, переместив ползунок **Decay Time Scale** (Шкала времени затухания). Рассмотрим случай, в котором в результате моделирования время затухания составляет 1,5 с. Если установить для параметра **Decay Time Scale** (Шкала времени затухания) значение 2, это приведет к тому, что к источнику будет применяться время затухания 3 с.

### <a name="modify-distance-based-attenuation"></a>Изменение ослабления, связанного с расстоянием
Подключаемый модуль микшера Wwise для Project Acoustics учитывает встроенное в Wwise ослабление на основе расстояния для каждого источника. Изменение этой кривой приведет к изменению уровня сухости звука. Подключаемый модуль Project Acoustics будет регулировать уровень влажности, чтобы поддерживать уровни сухости и влажности звука, определенные в настройках моделирования и элементах управления для проектирования.

![Снимок экрана с панелью кривой затухания Wwise — кривая достигает нуля на границе моделирования](media/demo-sounds-attenuation.png)

Project Acoustics выполняет вычисления в области моделирования для конкретного расположения каждого моделируемого игрока. Акустические ресурсы в примере пакета были смоделированы с областью моделирования в 45 м, а ослабевание звука разработано таким образом, чтобы уменьшаться до 0 при достижении расстояния в 45 м. Несмотря на то что это снижение не является обязательным требованием, оно представляет собой предупреждение о том, что эффект окклюзии звуков будут создавать только предметы в пределах 45 м.

## <a name="next-steps"></a>Дополнительная информация
* [Интегрируйте подключаемый модуль Project Acoustics](unreal-integration.md) в проект Unreal.
* [Создайте учетную запись Azure](create-azure-account.md) для собственных сборок сцены.


