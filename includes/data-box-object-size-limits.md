---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: eb12adf8f8523686b1d8deda2776eb203a76e954
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "66244558"
---
Ниже приведены размеры объектов Azure, которые могут быть записаны. Убедитесь, что все передаваемые файлы соответствуют этим ограничениям.

| Тип объекта Azure | Ограничение по умолчанию                                             |
|-------------------|-----------------------------------------------------------|
| Блочный BLOB-объект        | ~4,75 ТиБ                                                 |
| Страничный BLOB-объект         | 8 ТиБ <br> Каждый файл, отправляемый в формате страничного BLOB-объекта, должен быть выровнен по размеру 512 байт (целое кратное). В противном случае произойдет сбой передачи. <br> VHD и VHDX выровнены по размеру 512 байт. |
| Файлы Azure        | 1 ТиБ                                                      |
| Управляемые диски     | 4 ТиБ <br> Для получения дополнительной информации о размере и ограничениях см. <li>[Цели масштабируемости стандартных SSD](../articles/virtual-machines/windows/disks-types.md#standard-ssd)</li><li>[Цели масштабируемости премиальных SSD](../articles/virtual-machines/windows/disks-types.md#standard-hdd)</li><li>[Цели масштабируемости стандартных HDD](../articles/virtual-machines/windows/disks-types.md#premium-ssd)</li><li>[Ценообразование и выставление счетов управляемых дисков](../articles/virtual-machines/windows/disks-types.md#billing)</li>  
