---
title: Настройка инструментов Azure Stream Analytics для Visual Studio
description: В этой статье приведены требования для установки и настройки средств Azure Stream Analytics для Visual Studio.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: 673f4935dce28b30c10e6abf4c7d22e00c1dd73a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60762241"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Установка инструментов Azure Stream Analytics для Visual Studio
Инструменты Azure Stream Analytics поддерживают Visual Studio 2017, Visual Studio 2015 и Visual Studio 2013. В этой статье показано, как устанавливать и удалять инструменты.

Дополнительные сведения об использовании инструментов см. в статье [Использование инструментов Azure Stream Analytics для Visual Studio](stream-analytics-quick-create-vs.md).

## <a name="install"></a>Установка
### <a name="recommended-visual-studio-2019-and-2017"></a>Рекомендация: Visual Studio 2019 и 2017
* Скачайте [Visual Studio 2019 (предварительная версия 2 или более поздняя) и Visual Studio 2017 (версия 15.3 или более поздняя)](https://www.visualstudio.com/). Поддерживаются выпуски Enterprise (Ultimate/Premium), Professional и Community. Выпуск Express не поддерживается. Visual Studio 2017 не поддерживается на компьютерах Mac. 
* Инструменты Stream Analytics являются частью рабочей нагрузки **разработки Azure** и **хранилища и обработки данных** в Visual Studio 2017. Включите одну из этих рабочих нагрузок как часть установки Visual Studio.

Включите рабочую нагрузку **Хранение и обработка данных**, как показано ниже.

![Выбранная рабочая нагрузка "Хранение и обработка данных"](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-01.png)

Включите рабочую нагрузку **Разработка для Azure**, как показано ниже.

![Выбранная рабочая нагрузка "Разработка для Azure"](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2017-install-02.png)

* В меню "Сервис" выберите **Расширения и обновления**. Найдите средства Azure Data Lake и Stream Analytics в установленных расширениях и щелкните **Обновить**, чтобы установить последнюю версию расширения. 

![Расширения и обновления Visual Studio](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-extensions-updates.png)

### <a name="visual-studio-2015-2013"></a>Visual Studio 2015, 2013
* Установите Visual Studio 2015 или Visual Studio 2013 с обновлением 4. Поддерживаются выпуски Enterprise (Ultimate/Premium), Professional и Community. Выпуск Express не поддерживается. 
* Установите пакет SDK Microsoft Azure для .NET версии 2.7.1 или более поздней версии с помощью [установщика веб-платформы](https://www.microsoft.com/web/downloads/platform.aspx).
* Установите [инструменты Azure Stream Analytics для Visual Studio](https://www.microsoft.com/en-us/download/details.aspx?id=49504).

## <a name="update"></a>Блокировка изменений

### <a name="visual-studio-2019-and-2017"></a>Visual Studio 2019 и 2017
Напоминание о новой версии отображается в уведомлении Visual Studio.

![Напоминание о новой версии Visual Studio](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-new-version-reminder-vs-tools.png)

### <a name="visual-studio-2015-and-2013"></a>Visual Studio 2015 и 2013
Установленные инструменты Stream Analytics для Visual Studio автоматически проверяют наличие новых версий. Следуйте инструкциям во всплывающем окне, чтобы установить последнюю версию. 


## <a name="uninstall"></a>Удаление

### <a name="visual-studio-2019-and-2017"></a>Visual Studio 2019 и 2017
Дважды щелкните установщик Visual Studio и выберите **Изменить**. Снимите флажок **Средства Azure Data Lake и Stream Analytics** для рабочей нагрузки **Хранение и обработка данных** или **Разработка для Azure**.

### <a name="visual-studio-2015-and-2013"></a>Visual Studio 2015 и 2013
Откройте панель управления и удалите **Средства Microsoft Azure Data Lake и Stream Analytics для Visual Studio**.





