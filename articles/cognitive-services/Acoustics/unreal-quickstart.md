---
title: Краткое руководство по использованию Project Acoustics с Unreal
titlesuffix: Azure Cognitive Services
description: Поэкспериментируйте с элементами управления для проектирования Project Acoustics в Unreal и Wwise, используя пример содержимого, и выполните развертывание в Windows Desktop.
services: cognitive-services
author: NoelCross
manager: nitinme
ms.service: cognitive-services
ms.subservice: acoustics
ms.topic: quickstart
ms.date: 03/20/2019
ms.author: noelc
ROBOTS: NOINDEX
ms.openlocfilehash: d3afcded894f72626a4f24bcbe85c34ac1329c29
ms.sourcegitcommit: 824e3d971490b0272e06f2b8b3fe98bbf7bfcb7f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/10/2019
ms.locfileid: "72242926"
---
# <a name="project-acoustics-unrealwwise-quickstart"></a>Краткое руководство по использованию Project Acoustics с Unreal и Wwise
В этом кратком руководстве вы будете экспериментировать с элементами управления для проектирования Project Acoustics, используя пример содержимого для Unreal Engine и Wwise.

Требования к программному обеспечению для использования примера содержимого:
* [Unreal Engine](https://www.unrealengine.com/) 4.22;
* [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) 2019.1.2.

## <a name="download-the-sample-package"></a>Загрузка примера пакета
Скачайте [пример пакета Project Acoustics для Unreal и Wwise](https://www.microsoft.com/download/details.aspx?id=58090). Пример пакета содержит:
- проект Unreal Engine;
- проект Wwise для проекта Unreal;
- подключаемый модуль Wwise для Project Acoustics.

## <a name="set-up-the-project-acoustics-sample-project"></a>Настройка примера проекта Project Acoustics
Сначала установите подключаемый модуль Project Acoustics в Wwise. Затем разверните двоичные файлы Wwise в проект Unreal. Далее необходимо настроить подключаемый модуль Wwise Unreal для поддержки Project Acoustics.

### <a name="install-the-project-acoustics-wwise-plug-in"></a>Установка подключаемого модуля Wwise для Project Acoustics
Откройте средство запуска Wwise. На вкладке **Plugins** (Подключаемые модули) в разделе **Install New Plug-ins** (Установка новых подключаемых модулей) выберите **Add From Directory** (Добавить из каталога). Выберите каталог *AcousticsWwisePlugin\ProjectAcoustics*, добавленный в скачанный пакет.

![Возможность установки подключаемого модуля Wwise в средстве запуска Wwise](media/wwise-install-new-plugin.png)

### <a name="add-wwise-binaries-to-the-project-acoustics-unreal-sample-project"></a>Добавление двоичных файлов Wwise в пример проекта Unreal для Project Acoustics
1. В средстве запуска Wwise щелкните вкладку **Unreal Engine**. 
1. Выберите меню с тремя полосками рядом с разделом **Recent Unreal Engine Projects** (Последние проекты Unreal Engine) и щелкните **Browse for project** (Поиск проекта). Откройте файл формата *UPROJECT* примера проекта Unreal в пакете *AcousticsSample\AcousticsGame\AcousticsGame.uproject*.

   ![Вкладка Unreal в средстве запуска Wwise](media/wwise-unreal-tab.png)

3. Рядом с примером пакета Project Acoustics щелкните **Integrate Wwise in Project** (Интегрировать Wwise в Project).

   ![В средстве запуска Wwise отображается проект Unreal для Acoustics Game с выделенным параметром Integrate (Интеграция).](media/wwise-acoustics-game-project.png)

### <a name="extend-wwise-unreal-plug-in-functionality"></a>Расширение функциональных возможностей подключаемого модуля Unreal для Wwise
Для подключаемого модуля Unreal для Project Acoustics необходимо предоставить дополнительное поведение из API подключаемого модуля Unreal для Wwise. Запустите пакетный файл, предоставленный с подключаемым модулем Unreal для Project Acoustics, чтобы автоматизировать эти изменения.
* В каталоге *AcousticsGame\Plugins\ProjectAcoustics\Resources* запустите *PatchWwise.bat*.

    ![Скрипт для исправления проекта Wwise, выделенного в окне проводника Windows](media/patch-wwise-script.png)

* Если у вас не установлен пакет SDK для DirectX: в зависимости от используемой версии Wwise, возможно, потребуется закомментировать строку, содержащую `DXSDK_DIR` в *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs*:

    ![Редактор кода — комментирование строки DXSDK](media/directx-sdk-comment.png)

* Если компиляция выполняется с использованием Visual Studio 2019: чтобы устранить ошибку компоновки с помощью Wwise, вручную измените в *AcousticsGame\Plugins\Wwise\Source\AkAudio\AkAudio.Build.cs* значение по умолчанию *VSVersion* на **vc150**:

    ![Редактор кода со значением VSVersion, измененным на vc150](media/vsversion-comment.png)

### <a name="open-the-unreal-project"></a>Открытие проекта Unreal 
При открытии проекта Unreal появится запрос на перестроение модулей. Выберите **Да**.

Если происходит сбой открытия проекта из-за сбоев сборки, убедитесь, что для Project Acoustics установлен подключаемый модуль Wwise той же версии, которая использовалась в примере проекта Project Acoustics.

Если вы используете [AudioKinetic Wwise](https://www.audiokinetic.com/products/wwise/) до версии 2019.1, вы не сможете создавать библиотеки звуков на основе примера проекта Project Acoustics. Для этого вам нужно интегрировать Wwise версии 2019.1 в пример проекта.

## <a name="experiment-with-project-acoustics-design-controls"></a>Экспериментирование с элементами управления для проектирования Project Acoustics
Послушайте, как звучит сцена, нажав кнопку воспроизведения в редакторе Unreal. На компьютере используйте для перемещения клавиши W, A, S, D и мышь. Чтобы просмотреть сочетания клавиш для дополнительных элементов управления, нажмите клавишу F1.

Следующие сведения описывают некоторые действия проектирования, которые можно опробовать.

### <a name="modify-occlusion-and-transmission"></a>Изменение окклюзии и передачи
В каждом звуковом субъекте Unreal есть элементы управления для проектирования Project Acoustics для каждого источника.

![Редактор Unreal — элементы управления для проектирования Project Acoustics](media/demo-scene-sound-source-design-controls.png)

Если множитель **окклюзии** больше чем 1 (по умолчанию — 1), окклюзия преувеличена. Если задать значение меньше чем 1, эффект окклюзии будет слабее.

Чтобы обеспечить передачу через стены, переместите ползунок **Transmission (dB)** (Передача (дБ)) с самого низкого уровня.

### <a name="modify-wetness-for-a-source"></a>Изменение влажности для источника
Чтобы изменить скорость изменения влажности с расстоянием, используйте параметр **Perceptual Distance Warp** (Ощущаемое искажение расстояния). Project Acoustics вычисляют уровни влажности в пространстве с помощью симуляции. Уровни плавно изменяются с расстоянием и обеспечивают признаки удаленности расстояния. Чтобы повысить этот эффект, увеличьте уровни влажности, связанные с расстоянием, за счет увеличения значения искажения расстояния. Если задать для искажения значение меньше 1, изменение реверберации на основе расстояния будет слабее. Вы также можете внести более детальные изменения в этот результат, изменив параметр **Wetness (dB)** (Влажность (дБ)).

Увеличьте время затухания во всем пространстве, перемещая ползунок **Decay Time Scale** (Шкала времени затухания). Рассмотрим случай, в котором в результате моделирования время затухания составляет 1,5 секунд. Если установить для параметра **Decay Time Scale** (Шкала времени затухания) значение 2, это приведет к тому, что к источнику будет применяться время затухания 3 секунды.

### <a name="modify-distance-based-attenuation"></a>Изменение ослабления, связанного с расстоянием
Подключаемый модуль микшера Wwise для Project Acoustics учитывает встроенное в Wwise ослабление на основе расстояния для каждого источника. Изменение этой кривой приводит к изменению уровня сухости звука. Подключаемый модуль Project Acoustics будет регулировать уровень влажности, чтобы поддерживать уровни сухости и влажности звука, определенные в настройках моделирования и элементах управления для проектирования.

![Панель кривой затухания Wwise — кривая достигает нуля на границе моделирования](media/demo-sounds-attenuation.png)

Project Acoustics выполняет вычисления в "области моделирования", размещенной вокруг каждого моделируемого расположения игрока. Ресурсы акустических характеристик в примере пакета были смоделированы в области моделирования с радиусом 45 метров. Затухание должно сократиться до 0 при достижении расстояния в 45 метров. Несмотря на то что это снижение не является обязательным требованием, оно представляет собой предупреждение о том, что эффект окклюзии звуков будут создавать только предметы в пределах 45 метров.

## <a name="next-steps"></a>Дополнительная информация
* [Интегрируйте подключаемый модуль Project Acoustics](unreal-integration.md) в проект Unreal.
* [Создайте учетную запись Azure](create-azure-account.md) для собственных сборок сцены.
