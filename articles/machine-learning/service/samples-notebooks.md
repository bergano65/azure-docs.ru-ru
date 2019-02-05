---
title: Примеры записных книжек Jupyter
titleSuffix: Azure Machine Learning service
description: Для изучения Службы машинного обучения Azure в Python найдите и используйте примеры записных книжек Jupyter.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: sample
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 5ba555ad31545e1ae1aa822ec58b0bd22ef486ac
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55295157"
---
# <a name="use-jupyter-notebooks-to-explore-azure-machine-learning-service"></a>Использование записных книжек Jupyter в Azure Machine Learning Workbench

Для вашего удобства разработан ряд записных книжек Jupyter Python, которые можно использовать для изучения Службы машинного обучения Azure. 

Благодаря документации на этом сайте, узнайте, как использовать службу и как настроить записные книжки по своему усмотрению. 

Используйте один из приведенных ниже путей, чтобы запустить сервер с этими примерами записных книжек.  Когда сервер будет запущен, найдите записные книжки для руководств в папке **tutorials** или ознакомьтесь с различными компонентами в папке **how-to-use-azureml**.


## <a name="try-azure-notebooks-free-jupyter-notebooks-in-the-cloud"></a>Поработайте со службой "Записные книжки Azure": к вашим услугам бесплатные записные книжки на основе Jupyter в облаке Azure

Начать работу с Записными книжками Azure очень просто. [Пакет SDK Машинного обучения Azure для Python](https://aka.ms/aml-sdk) уже установлен и настроен в [Записных книжках Azure](https://notebooks.azure.com/). Службы Azure автоматически управляют установкой и последующими обновлениями.
  
[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]


## <a name="use-a-data-science-virtual-machine-dsvm"></a>Использование Виртуальной машины для обработки и анализа данных (DSVM)

[Пакет SDK Машинного обучения Azure для Python](https://aka.ms/aml-sdk) и сервер записной книжки уже установлены и настроены в DSVM. 

Когда вы [создадите DSVM](how-to-configure-environment.md#dsvm), выполните следующие действия на этой виртуальной машине, чтобы запустить записные книжки.

[!INCLUDE [aml-dsvm-server](../../../includes/aml-dsvm-server.md)]


## <a name="use-your-own-jupyter-notebook-server"></a>Использование собственного сервера записных книжек Jupyter

Чтобы создать локальный сервер Jupyter Notebook на компьютере, выполните следующие действия.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

<a name="automated-ml-setup"></a>

## <a name="automated-machine-learning-setup"></a>Настройка автоматического машинного обучения 

_Эти шаги применимы только к записным книжкам в папке **how-to-use-azureml/automated-machine-learning**._

Выполнив следующие инструкции, с помощью любого из перечисленных выше параметров можно одновременно установить среду и создать рабочую область. 

1. Установите [Miniconda](https://conda.io/miniconda.html). Выберите версию 3.7 или более позднюю. Следуйте инструкциям по установке. 
   >[!NOTE]
   >Можно использовать существующую Conda версии 4.4.10 или более поздней. Для отображения версии используйте `conda -V`. Версию Conda можно обновить, выполнив следующую команду: `conda update conda`. Нет необходимости в установке Miniconda.

1. Скачайте примеры записных книжек из [GitHub](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/automated-machine-learning
) как ZIP-файл и извлеките его содержимое в локальный каталог. Записные книжки Автоматического машинного обучения находятся в папке `how-to-use-azureml/automated-machine-learning`.

1. Настройте новую среду Conda. 
   1. Откройте запрос Conda на локальном компьютере.
   
   1. Перейдите к извлеченным на локальный компьютер файлам.
   
   1. Откройте папку **automated-machine-learning**.
   
   1. Выполните `automl_setup.cmd` в запросе Conda для Windows или в файле `.sh` для вашей операционной системы. Это может занять около 10 минут.

      Сценарий установки:
      + создает новую среду Conda;
      + устанавливает необходимые пакеты;
      + настраивает мини-приложение;
      + создает записную книжку Jupyter.
      
   >[!NOTE]
   > Сценарий принимает имя среды Conda в качестве дополнительного параметра. Имя среды Conda по умолчанию — `azure_automl`. Точная команда зависит от операционной системы. Это полезно при создании среды или ее обновлении до новой версии. Например, вы можете использовать "automl_setup.cmd azure_automl_sandbox", чтобы создать имя среды "azure_automl_sandbox". 
      
1. После выполнения сценария вы увидите в браузере домашнюю страницу записной книжки Jupyter.

1. Перейдите к пути, где были сохранены записные книжки. 

1. Откройте папку "automated-machine-learning", а затем записную книжку **configuration.ipynb**. 

1. Заполните ячейки в записной книжке, чтобы зарегистрировать поставщик ресурсов Службы машинного обучения, и создайте рабочую область.

Теперь вы научились открывать и запускать записные книжки, сохраненные на локальном компьютере.


## <a name="next-steps"></a>Дополнительная информация

Изучите репозиторий записных книжек GitHub для Службы машинного обучения Azure [здесь](https://aka.ms/aml-notebooks)

Ниже представлен список следующих руководств:
+ [Руководство 1. Обучение модели классификации изображений с помощью службы машинного обучения Azure](tutorial-train-models-with-aml.md)

+ [Руководство 1. Подготовка данных для моделирования регрессии](tutorial-data-prep.md)
