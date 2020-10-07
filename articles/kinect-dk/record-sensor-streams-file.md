---
title: Краткое руководство. Запись потоков датчика Azure Kinect в файл
description: Из этого краткого руководства вы узнаете, как записывать в файл потоки данных из пакета SDK для датчика.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: quickstart
keywords: azure, kinect, запись, воспроизведение, сканер, matroska, mkv, потоки, глубина, rgb, камера, цвет, imu, аудио, датчик
ms.openlocfilehash: 3dab147b593bf012bd6cd9c95d0195e84a2cbcf1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/05/2020
ms.locfileid: "85277835"
---
# <a name="quickstart-record-azure-kinect-sensor-streams-to-a-file"></a>Краткое руководство. Запись потоков датчика Azure Kinect в файл

В этом кратком руководстве содержатся сведения о том, как можно использовать [средство записи Azure Kinect](azure-kinect-recorder.md) для записи потоков данных в файл из пакета SDK для датчика.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

В этом кратком руководстве предполагается, что:

- устройство Azure Kinect DK подключено к главному компьютеру и на него надлежащим образом подается питание;
- [настройка](set-up-azure-kinect-dk.md) оборудования завершена.

## <a name="create-recording"></a>Создание записи

1. Откройте командную строку и укажите путь к [средству записи Azure Kinect](azure-kinect-recorder.md), которое находится в каталоге установленных средств (файл `k4arecorder.exe`). Например: `C:\Program Files\Azure Kinect SDK\tools\k4arecorder.exe`.
2. Выполните запись в течение 5 секунд.

    `k4arecorder.exe -l 5 %TEMP%\output.mkv`

3. По умолчанию средство записи использует режим глубины "NFOV без привязки" и выводит изображение RGB с частотой 30 кадров/с (1080p), включая данные IMU. Полный обзор параметров записи и советы см. в статье [Средство записи Azure Kinect](azure-kinect-recorder.md).

## <a name="play-back-recording"></a>Воспроизведение записи

Для воспроизведения записи можно использовать [средство просмотра Azure Kinect](azure-kinect-viewer.md).

1. Запустите [`k4aviewer.exe`](azure-kinect-viewer.md)
2. Разверните вкладку **Открыть запись** и откройте запись.

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы знаете, как записывать потоки датчика в файл. Следующий шаг:

> [!div class="nextstepaction"]
> [Создание первого приложения Azure Kinect](build-first-app.md)
