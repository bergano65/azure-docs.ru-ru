---
title: Руководство по Использование модели ONNX с Windows ML — Пользовательская служба визуального распознавания
titlesuffix: Azure Cognitive Services
description: Сведения о создании приложения Windows UWP, в котором используется модель ONNX, экспортированная из Azure Cognitive Services.
services: cognitive-services
author: larryfr
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: larryfr
ms.openlocfilehash: ceb45048d160603a3ee619e3ea0e69a0c188a9ad
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64725785"
---
# <a name="tutorial-use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>Руководство по Использование модели ONNX из Пользовательской службы визуального распознавания с Windows ML (предварительная версия)

Сведения об использовании модели ONNX, экспортированной из пользовательской службы визуального распознавания, с Windows ML (предварительная версия).

В этом документе содержатся сведения об использовании файла ONNX, экспортированного из пользовательской службы визуального распознавания с Windows ML. Приводится пример приложения Windows UWP. В пример включена обученная модель распознавания. Также приводятся действия по использованию в примере собственной модели.

> [!div class="checklist"]
> * Сведения о примере приложения
> * Получение примера кода
> * Выполнение примера
> * Использование своей модели

## <a name="prerequisites"></a>Предварительные требования

* Windows 10 сборки 17738 или более поздней версии.

* Windows SDK для сборки 17738 или более поздней версии.

* Visual Studio 2017 версии 15.7 или более поздней с включенной рабочей нагрузкой __Разработка приложений для универсальной платформы Windows__;

* включенным режимом разработчика. Дополнительные сведения см. в статье о [включении устройства для разработки](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

## <a name="about-the-example-app"></a>Сведения о примере приложения

Приложение является универсальным приложением Windows UWP. Оно позволяет выбрать сохраненное на компьютере изображение и отправить его в модель. Теги и оценки, возвращаемые моделью, отображаются рядом с изображением.

## <a name="get-the-example-code"></a>Получение примера кода

Пример приложения доступен по адресу: [https://github.com/Azure-Samples/cognitive-services-onnx12-customvision-sample/](https://github.com/Azure-Samples/cognitive-services-onnx12-customvision-sample/).

## <a name="run-the-example"></a>Выполнение примера

1. Чтобы запустить приложение из Visual Studio, нажмите клавишу `F5`. Вам может быть предложено включить режим разработчика. Дополнительные сведения см. в статье о [включении устройства для разработки](https://docs.microsoft.com/windows/uwp/get-started/enable-your-device-for-development).

1. При запуске приложения используйте кнопку, чтобы выбрать изображение для оценки.

## <a name="use-your-own-model"></a>Использование своей модели

Чтобы использовать собственную модель, выполните следующие действия.

1. [Создайте и обучите](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/getting-started-build-a-classifier) классификатор с помощью Пользовательской службы визуального распознавания. Чтобы экспортировать модель, выберите __компактную__ категорию, например **General (compact)** (Общее (компактная)). Чтобы экспортировать существующий классификатор, преобразуйте категорию в компактную, выбрав значок шестеренки в правом верхнем углу. В разделе __Settings__ (Параметры) выберите компактную модель, а затем сохраните и обучите свой проект.  

1. [Экспортируйте модель](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model), перейдя на вкладку Performance (Производительность). Выберите итерацию, обученную с использованием компактной области, чтобы отобразилась кнопка Export (Экспорт). Выберите *Export* (Экспорт), *ONNX*, *ONNX1.2* и *Export* (Экспорт). Когда файл будет готов, нажмите кнопку *Download* (Скачать).

1. Перенесите файл ONNX в папку __Assets__ (Ресурсы) своего проекта. 

1. В обозревателе решений щелкните правой кнопкой мыши папку Assets (Ресурсы) и выберите __Add Existing Item__ (Добавить существующий элемент). Выберите файл ONNX.

1. В обозревателе решений выберите файл ONNX в папке Assets (Ресурсы). Измените следующие свойства файла:

    * __Build Action__ -> __Content__ (Действие при сборке > Содержимое);
    * __Copy to Output Directory__ -> __Copy if newer__ (Копировать в выходной каталог > Копировать, если новее).

1. Замените переменную `_onnxFileNames` именем файла ONNX. Также измените `ClassLabel` количеством меток в модели.

1. Выполните сборку и запуск.

1. Нажмите кнопку, чтобы выбрать изображение для оценки.

## <a name="next-steps"></a>Дополнительная информация

Сведения о других способах экспорта и использования модели пользовательской службы визуального распознавания см. в следующих документах:

* [Экспорт модели](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/export-your-model)
* [Использование экспортированной модели Tensorflow в приложении Android](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [Использование экспортированной модели CoreML в приложении Swift iOS](https://go.microsoft.com/fwlink/?linkid=857726)
* [Использование экспортированной модели CoreML в приложении iOS с помощью Xamarin](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

Дополнительные сведения об использовании моделей ONNX с Windows ML см. в статье [Интеграция модели в приложение с использованием Windows ML](/windows/ai/windows-ml/integrate-model).
