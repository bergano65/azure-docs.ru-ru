---
title: Краткое руководство. Настройка Azure Kinect DK
description: В этом кратком руководстве содержатся инструкции по настройке оборудования Azure Kinect DK.
author: tesych
ms.author: tesych
ms.prod: kinect-dk
ms.topic: quickstart
ms.date: 02/12/2020
keywords: azure, kinect, dev kit, azure dk, настройка, оборудование, быстрый, usb, мощность, средство просмотра, датчик, потоковая передача, установка, SDK, встроенное ПО
ms.custom:
- CI 114092
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: b33565f319de77385c966f742fa52391f4869eed
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77211284"
---
# <a name="quickstart-set-up-your-azure-kinect-dk"></a>Краткое руководство. Настройка Azure Kinect DK

В этом кратком руководстве описано, как настроить Azure Kinect DK. Мы покажем, как тестировать визуализацию потока данных датчика и использовать [средство просмотра Azure Kinect](azure-kinect-viewer.md).

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="system-requirements"></a>Требования к системе

Проверьте [требования к системе](system-requirements.md), чтобы убедиться, что конфигурация главного компьютера соответствует всем минимальным требованиям Azure Kinect DK.

## <a name="set-up-hardware"></a>Настройка оборудования

> [!NOTE]
> Не забудьте удалить защитную пленку с камеры перед использованием устройства.

1. Подключите соединитель питания к разъему питания на задней панели устройства. Подключите USB-адаптер питания к другому концу кабеля, а затем подключите адаптер к розетке питания.
2. Подключите один конец кабеля USB к устройству, а другой к порту USB 3.0 на компьютере.
   >[!NOTE]
   >Для достижения лучших результатов подключите кабель непосредственно к устройству и компьютеру. Избегайте использования расширений или дополнительных адаптеров.

3. Убедитесь, что индикатор питания (рядом с USB-кабелем) постоянно светиться белым.
  
   Включение устройства занимает несколько секунд. Устройство готово к работе, когда светодиодный индикатор на передней панели отключается.  

   Дополнительные сведения о индикаторе питания см. в разделе [What does the light mean?](hardware-specification.md#what-does-the-light-mean) (Что означает цвет индикатора?)

    ![Все функции устройства](./media/quickstarts/full-device-features.png)

## <a name="download-the-sdk"></a>Скачивание пакета SDK

1. Щелкните ссылку, чтобы [скачать SDK](sensor-sdk-download.md).
2. Установите пакет SDK на компьютере.

## <a name="update-firmware"></a>Обновление встроенного ПО

Для корректной работы SDK требуется последняя версия встроенного ПО устройства. Чтобы узнать и обновить версию прошивки, выполните действия, описанные в статье [Update Azure Kinect DK firmware](update-device-firmware.md) (Обновление прошивки Azure Kinect DK).

## <a name="verify-that-the-device-streams-data"></a>Убедитесь, что устройство передает данные

1. Запустите [средство просмотра Azure Kinect](azure-kinect-viewer.md). Вы можете запустить этот инструмент с помощью одного из этих приведенных ниже методов.
   - Вы можете запустить средство просмотра из командной строки или двойным щелчком кнопки мыши. Файл, `k4aviewer.exe`, находится в каталоге инструментов SDK (например, `C:\Program Files\Azure Kinect SDK vX.Y.Z\tools\k4aviewer.exe`, где `X.Y.Z` — установленная версия SDK).
   - Вы можете запустить средство просмотра Azure Kinect из меню **Пуск** устройства.
2. В средстве просмотра Azure Kinect выберите **Open Device** (Открыть устройство) > **Пуск**.

    ![Средство просмотра Azure Kinect](./media/quickstarts/viewer.png)

3. Убедитесь, что средство визуализирует каждый поток датчика:
   - камера глубины;
   - камера для цветной съёмки;
   - инфракрасная камера;
   - IMU.
   - Микрофоны

    ![Средство визуализации](./media/quickstarts/visualization-tool.png)

Вы завершили установку Azure Kinect DK. Теперь вы можете начать разработку своего приложения или интеграцию служб.

При возникновении каких либо проблем, откройте средство [устранения неполадок](troubleshooting.md).

## <a name="see-also"></a>См. также раздел

- [Azure Kinect DK hardware specifications](hardware-specification.md) (Требования к оборудованию Azure Kinect DK)
- [Update Azure Kinect DK firmware](update-device-firmware.md) (Обновление встроенного ПО Azure Kinect DK)
- Дополнительные сведения о [средстве просмотра Azure Kinect](azure-kinect-viewer.md)

## <a name="next-steps"></a>Дальнейшие действия

После того как Azure Kinect DK подготовлен и работает, вы также сможете узнать, как
> [!div class="nextstepaction"]
> [Выполнять потоковую передачу данных датчиков в файл](record-sensor-streams-file.md)
