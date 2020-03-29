---
title: Доступ к наборам данных с помощью клиентской библиотеки Python. Командный процесс обработки и анализа данных
description: Установка и использование клиентской библиотеки Python для безопасного доступа к данным Машинного обучения Azure и управления ими из локальной среды Python.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 93ec5e740ac6acf9420a9d980092ed772ac1618e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76720985"
---
# <a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Доступ к наборам данных через Python с помощью клиентской библиотеки Python для машинного обучения Azure
Предварительная версия клиентской библиотеки Python для машинного обучения Microsoft Azure может обеспечить безопасный доступ к наборам данных машинного обучения Azure из локальной среды Python. Она также позволяет создавать наборы данных и управлять ими в рабочей области.

В этой статье описано, как:

* установить клиентскую библиотеку Python для машинного обучения;
* обращаться к наборам данных и передавать их, включая указания по получению авторизации для доступа к наборам данных машинного обучения Azure из локальной среды Python;
* получать доступ к промежуточным наборам данных из экспериментов;
* использовать клиентскую библиотеку Python для перечисления наборов данных, доступа к метаданным, чтения содержимого набора данных, создания новых наборов данных и обновления существующих наборов данных

## <a name="prerequisites"></a><a name="prerequisites"></a>Предварительные требования
Клиентская библиотека Python была протестирована в следующих средах:

* Windows, Mac и Linux
* Python 2.7, 3.3 и 3.4

Зависит от следующих пакетов:

* requests
* python-dateutil
* pandas

Мы рекомендуем использовать дистрибутив Python, например [Anaconda](http://continuum.io/downloads#all) или [Canopy](https://store.enthought.com/downloads/), который поставляется с Python, IPython и тремя устанавливаемыми пакетами, перечисленными выше. Хотя использование IPython не является обязательным, это отличная среда для интерактивного управления данными и их визуализации.

### <a name="how-to-install-the-azure-machine-learning-python-client-library"></a><a name="installation"></a>Как установить клиентскую библиотеку Python для Машинного обучения Azure
Установите клиентскую библиотеку Azure Machine Learning Python для выполнения задач, изложенных в этой теме. Эта библиотека доступна в [индексе пакета Python.](https://pypi.python.org/pypi/azureml) Чтобы установить ее в своей среде Python, выполните следующую команду в локальной среде Python:

    pip install azureml

Кроме того, вы можете скачать и установить из источников на [GitHub](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Если на компьютере установлено приложение git, вы можете использовать команду pip для установки непосредственно из репозитория git:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


## <a name="use-code-snippets-to-access-datasets"></a><a name="datasetAccess"></a>Используйте фрагменты кода для доступа к наборам данных
Клиентская библиотека Python обеспечивает программный доступ к существующим наборам данных от экспериментов, которые были выполнены.

Из веб-интерфейса Azure Machine Learning Studio (классический) можно создавать фрагменты кода, которые включают всю необходимую информацию для загрузки и десеризации наборов данных в виде объектов DataFrame панд на локальной машине.

### <a name="security-for-data-access"></a><a name="security"></a>Безопасность доступа к данным
Фрагменты кода, предоставленные Azure Machine Learning Studio (классический) для использования в клиентской библиотеке Python, включают идентификатор рабочего пространства и токен авторизации. Они предоставляют полный доступ к рабочей области, и их необходимо защитить, например, паролем.

По соображениям безопасности функциональность фрагмента кода доступна только пользователям с ролью **Владелец** для рабочей области. Ваша роль отображается в студии машинного обучения Azure (классическая) на странице **USERS** под **настройками.**

![Безопасность][security]

Если ваша роль не **Владелец**, то вы можете запросить приглашение в качестве владельца или обратиться к владельцу рабочей области за предоставлением фрагмента кода.

Чтобы получить токен авторизации, вы можете выбрать один из этих вариантов:

* Запросите маркер у владельца. Владельцы могут получить доступ к токетокам авторизации со страницы «Настройки» в студии машинного обучения Azure (классический). Выберите **Параметры** в левой области и щелкните **Authorization tokens** (Маркеры авторизации), чтобы просмотреть основной и дополнительный маркеры. Хотя в фрагменте кода могут использоваться как основные, так и дополнительные маркеры авторизации, владельцам рекомендуется предоставлять только дополнительные маркеры авторизации.

   ![Маркеры авторизации](./media/python-data-access/ml-python-access-settings-tokens.png)

* Попросите, чтобы вас повысили до роли владельца: текущий владелец рабочего пространства должен сначала удалить вас из рабочего пространства, а затем повторно пригласить вас к нему как к владельцу.

После того, как разработчики получили идентификатор рабочего пространства и маркер авторизации, они могут получить доступ к рабочему пространству с помощью фрагмента кода, независимо от их роли.

Для управления маркерами авторизации используется раздел **Параметры** страницы **Authorization tokens** (Маркеры авторизации). Их можно создать повторно, но эта процедура отменяет доступ для предыдущего маркера.

### <a name="access-datasets-from-a-local-python-application"></a><a name="accessingDatasets"></a>Доступ к наборам данных из локального приложения Python
1. В студии машинного обучения (классический) нажмите **DATASETS** в панели навигации слева.
2. Выберите набор данных, к которому хотите получить доступ. Вы можете выбрать любой из наборов данных в списке **My datasets** (Мои наборы данных) или **Примеры**.
3. На нижней панели инструментов щелкните **Generate Data Access Code**(Создать код доступа к данным). Эта кнопка отключена, если данные хранятся в формате, несовместимом с клиентской библиотекой Python.
   
    ![Наборы данных][datasets]
4. Выберите фрагмент кода в появившемся окне и скопируйте его в буфер обмена.
   
    ![Кнопка "Generate Data Access Code File" (Создать файл кода доступа к данным)][dataset-access-code]
5. Вставьте код в заметки своего локального приложения Python.
   
    ![Вставка кода в записную книжку][ipython-dataset]

## <a name="access-intermediate-datasets-from-machine-learning-experiments"></a><a name="accessingIntermediateDatasets"></a>Доступ к промежуточным наборам данных из экспериментов машинного обучения
После запуска эксперимента в Студии машинного обучения (классический) можно получить доступ к промежуточным наборам данных из выходных узлов модулей. Промежуточные наборы данных — это данные, создаваемые и используемые на промежуточных шагах после запуска инструмента моделирования.

Доступ к промежуточным наборам данных можно получить при условии, что формат данных совместим с клиентской библиотекой Python.

Поддерживаются следующие форматы (константы для `azureml.DataTypeIds` этих форматов находятся в классе):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

Определить формат можно, наведя указатель мыши на выходной узел модуля. Он отображается во всплывающей подсказке вместе с именем узла.

Некоторые модули, например модуль [Разделение][split], выводят данные в формат `Dataset`, который не поддерживается клиентской библиотекой Python.

![Формат набора данных][dataset-format]

В этом случае требуется модуль преобразования, например [Преобразовать в CSV][convert-to-csv], чтобы получить выходные данные в поддерживаемом формате.

![Формат GenericCSV][csv-format]

Далее показан пример, который создает эксперимент, выполняет его и обращается к промежуточному набору данных.

1. Создайте новый эксперимент.
2. Вставьте модуль набора данных **Adult Census Income Binary Classification** .
3. Вставьте модуль [Разделение][split] и подключите к его вводу выходные данные модуля набора данных.
4. Вставьте модуль [Преобразовать в CSV][convert-to-csv] и подключите к его вводу выходные данные модуля набора данных [Разделение][split].
5. Сохраните эксперимент, запустите его и ждите завершения задания.
6. Щелкните выходной узел в модуле [Преобразовать в CSV][convert-to-csv].
7. В появившемся контекстном меню щелкните пункт **Generate Data Access Code** (Создать код доступа к данным).
   
    ![Контекстное меню][experiment]
8. Выберите фрагмент кода в появившемся окне и скопируйте его в буфер обмена.
   
    ![Создание кода доступа из контекстного меню][intermediate-dataset-access-code]
9. Вставьте код в заметки.
   
    ![Вставка кода в записную книжку][ipython-intermediate-dataset]
10. Вы можете визуализировать данные с помощью matplotlib. Вот как выглядит гистограмма для столбца возраста:
    
    ![Гистограмма][ipython-histogram]

## <a name="use-the-machine-learning-python-client-library-to-access-read-create-and-manage-datasets"></a><a name="clientApis"></a>Использование клиентской библиотеки Python для машинного обучения для осуществления доступа, чтения, создания и управления наборами данных
### <a name="workspace"></a>Рабочая область
Рабочая область — это точка входа для клиентской библиотеки Python. Предоставьте `Workspace` классу идентификатор рабочего пространства и токен авторизации для создания экземпляра:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Перечисление наборов данных
Для перечисления всех наборов данных в заданной рабочей области:

    for ds in ws.datasets:
        print(ds.name)

Для перечисления только созданных пользователями наборов данных:

    for ds in ws.user_datasets:
        print(ds.name)

Для перечисления только примеров наборов данных:

    for ds in ws.example_datasets:
        print(ds.name)

Можно обратиться к набору данных по имени (с учетом регистра):

    ds = ws.datasets['my dataset name']

Или по индексу:

    ds = ws.datasets[0]


### <a name="metadata"></a>Метаданные
Кроме содержимого в наборах данных имеются метаданные. (Промежуточные наборы данных являются исключением из этого правила и не имеют метаданных.)

Некоторые значения метаданных назначаются пользователем во время создания:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

Другие значения назначаются Машинным обучением Azure:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Дополнительную информацию о доступных метаданных см. в описании класса `SourceDataset`.

### <a name="read-contents"></a>Чтение содержимого
Фрагменты кода, предоставленные Машинной студией обучения (классический), автоматически загружаются и десериализируют набор данных на объект DataFrame панд. Это выполняется методом `to_dataframe` :

    frame = ds.to_dataframe()

Если вы предпочитаете скачать необработанные данные и выполнить десериализацию вручную, это возможно. На данный момент это единственная возможность для таких форматов, как «ARFF», которые клиентская библиотека Python не может десериализовать.

Для считывания содержимого в виде текста:

    text_data = ds.read_as_text()

Для считывания содержимого в виде двоичного файла:

    binary_data = ds.read_as_binary()

Можно также просто открыть поток содержимого:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Создание нового набора данных
Клиентская библиотека Python позволяет передавать наборы данных из программы Python. Эти наборы данных затем станут доступными для использования в вашей рабочей области.

Для данных в формате Pandas DataFrame используйте следующий код:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Если данные уже сериализованы, можно использовать:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Клиентская библиотека Python может сериализовать объекты Pandas DataFrame в следующие форматы (константы для них находятся в классе `azureml.DataTypeIds`):

* PlainText
* GenericCSV
* GenericTSV
* GenericCSVNoHeader
* GenericTSVNoHeader

### <a name="update-an-existing-dataset"></a>Обновление существующего набора данных
Если вы попытаетесь передать новый набор данных с именем, которое совпадает с именем существующего набора данных, то произойдет ошибка конфликта.

Чтобы обновить существующий набор данных, сначала необходимо получить ссылку на существующий набор данных:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Затем используйте `update_from_dataframe` для сериализации и замены содержимого набора данных в Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Если вы хотите сериализовать данные в другой формат, укажите значение необязательного параметра `data_type_id`.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

При необходимости можно задать новое описание, указав значение для параметра `description` .

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Кроме того, при необходимости можно задать новое имя, указав значение для параметра `name` . Теперь вы сможете извлекать набор данных только с помощью нового имени. Следующий код обновляет данные, имя и описание.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

Параметры `data_type_id`, `name` и `description` являются необязательными, и по умолчанию используются их предыдущие значения. Параметр `dataframe` всегда является обязательным.

Если данные уже сериализованы, вместо `update_from_dataframe` используйте `update_from_raw_data`: Он работает точно так же, просто вместо `raw_data` передается `dataframe`.

<!-- Images -->
[security]:./media/python-data-access/security.png
[dataset-format]:./media/python-data-access/dataset-format.png
[csv-format]:./media/python-data-access/csv-format.png
[datasets]:./media/python-data-access/datasets.png
[dataset-access-code]:./media/python-data-access/dataset-access-code.png
[ipython-dataset]:./media/python-data-access/ipython-dataset.png
[experiment]:./media/python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/

