---
title: Сброс Azure Kinect DK
description: В этой статье описывается, как сбросить устройство Azure Kinect DK к заводскому образу
author: Teresa-Motiv
ms.author: v-tea
ms.reviewer: jarrettr
ms.prod: kinect-dk
ms.date: 02/11/2020
ms.topic: how-to
keywords: kinect, сброс
ms.custom:
- CI 113492
- CSSTroubleshooting
audience: ITPro
manager: dcscontentpm
ms.localizationpriority: high
ms.openlocfilehash: f851579712a9549c88f1ca652aea4f31457da132
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201904"
---
# <a name="reset-azure-kinect-dk"></a>Сброс Azure Kinect DK

Вы можете столкнуться с ситуацией, когда необходимо сбросить Azure Kinect DK к заводскому образу (например, если обновление встроенного ПО установилось неправильно).

1. Выключите Azure Kinect DK. Для этого отсоедините кабель USB и кабель питания.
  ![Рисунок, на котором показано расположение винта, закрывающего кнопку сброса.](media/reset-azure-kinect-dk-diagram.png)
1. Чтобы найти кнопку сброса, снимите винт, расположенный в гнезде крепления штатива.
1. Снова подключите кабель питания.
1. Введите кончик выпрямленной скрепки в пустое резьбовое отверстие гнезда крепления штатива.
1. Скрепкой аккуратно нажмите и удерживайте кнопку сброса.
1. При нажатой кнопке сброса подключите USB-кабель.
1. Через 3 секунды цвет индикатора питания меняется на оранжевый. Как только цвет индикатора изменится, отпустите кнопку сброса.  
   
   Как только кнопка сброса отпущена, во время сброса устройства индикатор питания начнет мигать белым и оранжевым. 
1. Дождитесь, когда индикатор питания станет постоянно подсвечиваться белым.
1. Верните винт в гнезде крепления штатива на место (закрыв кнопку сброса).
1. С помощью средства просмотра Kinect для Azure проверьте, что встроенное ПО сброшено. Для этого запустите [средство просмотра Azure Kinect](azure-kinect-viewer.md), а затем выберите **Device firmware version info** (Сведения о версии встроенного ПО устройства), чтобы узнать версию встроенного ПО, установленного в Azure Kinect DK.

Следите за тем, чтобы на устройстве была установлена последняя версия встроенного ПО. Чтобы получить последнюю версию встроенного ПО, используйте средство обновления встроенного ПО Azure Kinect. Дополнительные сведения о проверке состояния встроенного ПО см. в разделе [Проверка версии встроенного ПО устройства](azure-kinect-firmware-tool.md#check-device-firmware-version).

## <a name="related-topics"></a>Связанные разделы

- [Об Azure Kinect DK](about-azure-kinect-dk.md)
- [Настройка Azure Kinect DK](set-up-azure-kinect-dk.md)
- [Спецификации оборудования для Azure Kinect DK: условия эксплуатации](hardware-specification.md#operating-environment)
- [Средство обновления встроенного ПО Azure Kinect](azure-kinect-firmware-tool.md)
- [Средство просмотра Azure Kinect](azure-kinect-viewer.md)
- [Синхронизация нескольких устройств Azure Kinect DK](multi-camera-sync.md)
