---
title: Использование Гео ИИ
titleSuffix: Azure Data Science Virtual Machine
description: Сведения об использовании виртуальной машины обработки и анализа географических данных с помощью ИИ для анализа данных и построения моделей на основе геопространственных данных.
keywords: deep learning, AI, data science tools, data science virtual machine, geospatial analytics
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 03/05/2018
ms.openlocfilehash: 2d7532c51e2963c0dc9f8d02e7a0e32864e80f92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70278418"
---
# <a name="using-the-geo-artificial-intelligence-data-science-virtual-machine"></a>Использование виртуальной машины для обработки и анализа геоданных средствами ИИ

Виртуальная машина для обработки и анализа геоданных средствами искусственного интеллекта позволяет осуществлять выборку данных для анализа, обрабатывать данные и создавать модели для приложений ИИ, использующих геопространственную информацию. После того, как вы подготовите свой Geo AI Data Science VM и вопиете в ArcGIS Pro через свою учетную запись ArcGIS, вы можете начать взаимодействовать с настольным компьютером ArcGIS и ArcGIs онлайн. Вы также можете получить доступ к ArcGIS с интерфейсов Python и моста языка R, который предварительно настроен на Geo-Data Science VM. Чтобы создать богатые приложения и итоговый ими, объедините ВМ Geo-Data Science с платформами машинного обучения и глубокого обучения и другими доступными на нем программами для наукоемких данных.  


## <a name="configuration-details"></a>Сведения о конфигурации

Библиотека Python, [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm), которая используется для взаимодействия с ArcGIS, устанавливается в глобальной среде корневой конды Data Science VM, которая находится в ```c:\anaconda```.

- Если вы работаете с Python в ```activate``` командном запросе, запустите, чтобы активировать в среду root Conda Python.
- Если вы используете ноутбук IDE или Jupyter, вы можете выбрать среду или ядро, чтобы убедиться, что вы находитесь в правильной среде conda.

R мост к ArcGIS установлен как R-библиотека под названием [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) в главном автономном экземпляре Microsoft Machine Learning Server, расположенном в ```C:\Program Files\Microsoft\ML Server\R_SERVER```. Visual Studio, RStudio и Jupyter уже предварительно настроены для использования этой ```arcgisbinding``` среды R и будут иметь доступ к библиотеке R. 


## <a name="geo-ai-data-science-vm-samples"></a>Примеры виртуальной машины для обработки и анализа геоданных средствами ИИ

В дополнение к машинному обучению и глубокому обучению на основе образцов, основанных на базе Data Science VM, набор геопространственных образцов также предоставляется в рамках Geo AI Data Science VM. Эти образцы могут помочь вам начать разработку приложений ИИ с помощью геопространственных данных и программного обеспечения ArcGIS:


1. [Начало работы с геопространственной аналитики с Python](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/Python%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Вводный образец, показывающий, как работать с геопространственных данных через интерфейс Python к ArcGIS обеспечивается [arcpy](https://pro.arcgis.com/en/pro-app/arcpy/main/arcgis-pro-arcpy-reference.htm) библиотеки. Он также показывает, как объединить традиционное машинное обучение с геопространственными данными, а затем визуализировать результат на карте arcGIS.

2. [Начало работы с геопространственной аналитики с R](https://github.com/Azure/DataScienceVM/blob/master/Notebooks/ArcGIS/R%20walkthrough%20ArcGIS%20Data%20analysis%20and%20ML.ipynb): Вводный образец, который показывает, как работать с геопространственных данных с помощью интерфейса R для ArcGIS, который обеспечивается [arcgisbinding](https://github.com/R-ArcGIS/r-bridge) библиотеки. 

3. [Pixel-level land use classification](https://github.com/Azure/pixel_level_land_classification) (Классификация использования земли на уровне пикселей): учебное руководство по созданию модели глубокой нейронной сети, которая принимает аэрофотоснимок и возвращает метку покрытия земли. Примерами надкрытия земли являются *лесистые* и *водные.* Модель возвращает такую метку для каждого пикселя в изображении. 


## <a name="next-steps"></a>Дальнейшие действия

Дополнительные примеры использования виртуальной машины для обработки и анализа данных см. здесь:

* [Примеры](dsvm-samples-and-walkthroughs.md)