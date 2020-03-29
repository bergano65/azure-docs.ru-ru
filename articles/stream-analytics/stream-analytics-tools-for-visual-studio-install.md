---
title: Настройка инструментов Azure Stream Analytics для Visual Studio
description: В этой статье описаны требования к установке и способы настройки инструментов azure Stream Analytics для Visual Studio.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/22/2018
ms.openlocfilehash: fa3990d2239da71fb27f4c9a06699f758fcfe7a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75354361"
---
# <a name="install-azure-stream-analytics-tools-for-visual-studio"></a>Установка инструментов Azure Stream Analytics для Visual Studio

Visual Studio 2019 и Visual Studio 2017 поддерживают Azure Data Lake и Stream Analytics Tools. В этой статье показано, как устанавливать и удалять инструменты.

Для получения дополнительной информации [Quickstart: Create an Azure Stream Analytics job by using Visual Studio](stream-analytics-quick-create-vs.md)об использовании инструментов см.

## <a name="install"></a>Установка

Визуальные Studio Enterprise (Ultimate/Premium), Профессиональные и Сообщества издания поддерживают инструменты. Экспресс-издание и Visual Studio для Mac не поддерживают их.

Мы рекомендуем Visual Studio 2019.

### <a name="install-for-visual-studio-2019-and-2017"></a>Установка для визуальной студии 2019 и 2017<a name="recommended-visual-studio-2019-and-2017"></a>

Инструменты Azure Data Lake и Stream Analytics Tools являются частью **разработки Azure** и рабочих нагрузок **для хранения и обработки данных.** Включите одну из этих двух рабочих нагрузок во время установки. Если Visual Studio уже установлена, выберите **Инструменты** > **Получить инструменты и функции,** чтобы добавить рабочие нагрузки.

Скачать [Visual Studio 2019 (Предварительный просмотр 2 или выше) или Visual Studio 2017 (15.3 или выше)](https://www.visualstudio.com/) и следуйте инструкциям для установки.

Выберите рабочую нагрузку **на хранение и обработку данных** в показано:

![Выбранная рабочая нагрузка "Хранение и обработка данных"](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-01.png)

Выберите рабочую нагрузку **разработки Azure** в показано:

![Выбранная рабочая нагрузка "Разработка для Azure"](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-for-vs-2019-install-02.png)

После добавления рабочей нагрузки обновите инструменты. Эта процедура относится к Visual Studio 2019:

1. Выберите **расширения** > **Управления расширениями.**

1. В **области расширения управления**выберите **обновления** и выберите **инструменты анализа данных Azure и Stream Analytics.**

1. Выберите **обновление** для установки последнего расширения.

![Расширения и обновления Visual Studio](./media/stream-analytics-tools-for-visual-studio-install/stream-analytics-tools-vs2019-extensions-updates.png)

### <a name="install-for-visual-studio-2015-and-2013"></a>Установка для визуальной студии 2015 и 2013<a name="visual-studio-2015-2013"></a>

Визуальные Studio Enterprise (Ultimate/Premium), Профессиональные и Сообщества издания поддерживают инструменты. Экспресс-издание их не поддерживает.

* Установите Visual Studio 2015 или Visual Studio 2013 с обновлением 4.
* Установите пакет SDK Microsoft Azure для .NET версии 2.7.1 или более поздней версии с помощью [установщика веб-платформы](https://www.microsoft.com/web/downloads/platform.aspx).
* Установите [Microsoft Azure Data Lake и инструменты анализа потоков для визуальной студии.](https://www.microsoft.com/en-us/download/details.aspx?id=49504)

## <a name="update"></a>Обновление<a name="visual-studio-2019-and-2017"></a><a name="visual-studio-2015-and-2013"></a>

Для Visual Studio 2019 и Visual Studio 2017, новая версия напоминание появляется в виде уведомления Visual Studio.

Для Visual Studio 2015 и Visual Studio 2013 инструменты автоматически проверяют новые версии. Следуйте инструкциям по установке последней версии.

## <a name="uninstall"></a>Удаление

Можно удалить инструменты Azure Data Lake и Stream Analytics Tools. Для Visual Studio 2019 или Visual Studio 2017 выберите **Инструменты** > **Получить инструменты и особенности**. В **изменении**, невыбрать **Озеро данных Azure и поток Analytics Tools**. Он отображается либо под рабочей нагрузкой **для хранения и обработки данных,** либо с рабочей нагрузкой **разработки Azure.**

Чтобы удалить из Visual Studio 2015 или Visual Studio 2013, перейдите к**программам и функциям** **панели управления** > . Удалите **Microsoft Azure Data Lake и инструменты анализа потоков для визуальной студии.**
