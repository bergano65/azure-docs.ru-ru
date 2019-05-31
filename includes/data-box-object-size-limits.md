---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66244558"
---
Ниже приведены размеры объектов Azure, которые могут быть записаны. Убедитесь, что все передаваемые файлы соответствуют этим ограничениям.

| Тип объекта Azure | Ограничение по умолчанию                                             |
|-------------------|-----------------------------------------------------------|
| Блочный BLOB-объект        | ~4,75 ТиБ                                                 |
| Страничный BLOB-объект         | 8 ТиБ <br> Каждый файл, отправляемый в формате страничного BLOB-объекта, должен быть выровнен по размеру 512 байт (целое кратное). В противном случае произойдет сбой передачи. <br> VHD и VHDX выровнены по размеру 512 байт. |
| Файлы Azure        | 1 ТиБ                                                      |
| Управляемые диски     | 4 ТиБ <br> Дополнительные сведения о размере и ограничения см. в разделе: <li>[Целевые показатели масштабируемости для стандартных SSDs](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Целевые показатели масштабируемости для уровня "премиум" SSDs](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Целевые показатели масштабируемости для стандартных жестких дисков](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Цены и выставление счетов для управляемых дисков](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
