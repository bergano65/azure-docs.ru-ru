---
title: 'Загрузка: пакет SDK Python для подготовки данных'
titleSuffix: Azure Machine Learning service
description: Узнайте, как загружать данные с помощью пакета SDK для подготовки данных машинного обучения Azure. Можно загрузить различные типы входных данных, указать типы и параметры файлов данных или воспользоваться функцией интеллектуального чтения SDK, чтобы автоматически обнаружить тип файла.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: sihhu
author: MayMSFT
manager: cgronlun
ms.reviewer: jmartens
ms.date: 02/22/2019
ms.custom: seodec18
ms.openlocfilehash: 7dc07ba7f1d62b49232b1cd892070804099fab8c
ms.sourcegitcommit: 4b9c06dad94dfb3a103feb2ee0da5a6202c910cc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/02/2019
ms.locfileid: "65024003"
---
# <a name="load-and-read-data-with-the-azure-machine-learning-data-prep-sdk"></a>Загрузить и считывание данных с помощью пакета SDK Azure Machine Learning данных подготовки
В этой статье вы узнаете, различных методов загрузки данных с помощью пакета SDK Azure Machine Learning данных подготовки. Справочная документация для пакета SDK см. в разделе [Обзор](https://aka.ms/data-prep-sdk). Этот пакет SDK поддерживает несколько функций приема данных, включая:

* загрузка из файлов многих типов с автоматическим обнаружением параметров для синтаксического анализа (кодировка, разделитель, заголовки);
* преобразование типов на основе зависимостей во время загрузки файла;
* поддержка подключений к MS SQL Server и хранилищу Azure Data Lake.

> [!Important]
> Если вы создаете новое решение, попробуйте [наборы данных обучения машины Azure](how-to-explore-prepare-data.md) (Предварительная версия) для просмотра данных и подготовки. Наборы данных — следующая версия пакета SDK, предлагая расширенными функциональными возможностями по управлению наборов данных в решения искусственного Интеллекта подготовки данных.

В следующей таблице показаны несколько функций, используемых для загрузки данных из распространенных типов файлов.

| Тип файла | Функция | Ссылка на |
|-------|-------|-------|
|Любой|`auto_read_file()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#auto-read-file-path--filepath--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|Text|`read_lines()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-lines-path--filepath--header--azureml-dataprep-api-engineapi-typedefinitions-promoteheadersmode----promoteheadersmode-none--0---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---skip-rows--int---0--skip-mode--azureml-dataprep-api-engineapi-typedefinitions-skipmode----skipmode-none--0---comment--str---none--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|CSV|`read_csv()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-csv-path--filepath--separator--str--------header--azureml-dataprep-api-engineapi-typedefinitions-promoteheadersmode----promoteheadersmode-constantgrouped--3---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---quoting--bool---false--inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--skip-mode--azureml-dataprep-api-engineapi-typedefinitions-skipmode----skipmode-none--0---comment--str---none--include-path--bool---false--archive-options--azureml-dataprep-api--archiveoption-archiveoptions---none-----azureml-dataprep-api-dataflow-dataflow)|
|Excel|`read_excel()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-excel-path--filepath--sheet-name--str---none--use-column-headers--bool---false--inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|Фиксированной ширины|`read_fwf()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-fwf-path--filepath--offsets--typing-list-int---header--azureml-dataprep-api-engineapi-typedefinitions-promoteheadersmode----promoteheadersmode-constantgrouped--3---encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---inference-arguments--azureml-dataprep-api-builders-inferencearguments---none--skip-rows--int---0--skip-mode--azureml-dataprep-api-engineapi-typedefinitions-skipmode----skipmode-none--0---include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|
|JSON|`read_json()`|[reference](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#read-json-path--filepath--encoding--azureml-dataprep-api-engineapi-typedefinitions-fileencoding----fileencoding-utf8--0---flatten-nested-arrays--bool---false--include-path--bool---false-----azureml-dataprep-api-dataflow-dataflow)|

## <a name="load-data-automatically"></a>Автоматическая загрузка данных

Используйте функцию `auto_read_file()`, чтобы автоматически загружать данные без указания типа файла. Тип файла и аргументы для их чтения определяются автоматически.

```python
import azureml.dataprep as dprep

dflow = dprep.auto_read_file(path='./data/any-file.txt')
```

Эта функция используется для автоматического обнаружения типа файла, кодирования и других видов анализа аргументов из единой удобной точки входа. Функция также автоматически выполняет следующие действия, которые часто выполняются во время загрузки данных с разделителями:

* определение и настройка разделителя;
* пропуск пустых записей в начале файла;
* определение и настройка строки заголовка.

Кроме того Если вы знаете файле введите заранее и явным образом контролировать способ ее анализе, используются функции относящихся к файлу.

## <a name="load-text-line-data"></a>Загрузка строковых текстовых данных

Чтобы поместить в поток данных простые текстовые данные, используйте `read_lines()` без дополнительных параметров.

```python
dflow = dprep.read_lines(path='./data/text_lines.txt')
dflow.head(5)
```

||график;|
|----|-----|
|0|Дата\|\| Минимальная температура\|\| Максимальная температура|
|1|2015-07-1 \|\| -4,1 \|\| 10,0|
|2|2015-07-2 \|\| -0,8 \|\| 10,8|


После приема данных выполните следующий код, чтобы преобразовать объект потока данных в таблицу данных Pandas.

```python
pandas_df = dflow.to_pandas_dataframe()
```

## <a name="load-csv-data"></a>Загрузка данных в формате CSV

При чтении файлов с разделителями среда выполнения может вычислить параметры для анализа (например, найти разделители и кодировка, выбрать заголовки и т. д.). Выполните представленный ниже фрагмент кода, чтобы попытаться прочитать файл, указав только его местоположение.

```python
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|АЛАБАМА|1|101710|Округ Хейл|10171002158| |
|2|АЛАБАМА|1|101710|Округ Хейл|10171002162| |


Чтобы исключить строки во время загрузки, определите параметр `skip_rows`. Этот параметр пропускает загрузку из CSV-файле всех строк ниже указанной (по индексу строк, начиная с единицы).

```python
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|
|0|АЛАБАМА|1|101710|Округ Хейл|10171002158|29|
|1|АЛАБАМА|1|101710|Округ Хейл|10171002162|40 |

Выполните следующий код, чтобы отобразить типы данных для столбцов.

```python
dflow.dtypes
```
Выходные данные:

    stnam                     object
    fipst                     object
    leaid                     object
    leanm10                   object
    ncessch                   object
    schnam10                  object
    MAM_MTH00numvalid_1011    object
    dtype: object

По умолчанию SDK службы "Машинное обучение Azure" для подготовки данных не меняет тип данных. Сейчас мы считываем данные из текстового файла, поэтому пакет SDK считает все значения строковыми. Но в этом примере числовые столбцы нужно анализировать как числа. Задайте для параметра `inference_arguments` значение `InferenceArguments.current_culture()`, чтобы автоматически определять и преобразовывать типы столбцов во время чтения файлов.

```
dflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dflow.dtypes
```
Выходные данные:

    stnam                      object
    fipst                     float64
    leaid                     float64
    leanm10                    object
    ncessch                   float64
    schnam10                   object
    ALL_MTH00numvalid_1011    float64
    dtype: object


Несколько столбцов правильно определяются как числовые и им присваивается тип данных `float64`.

## <a name="use-excel-data"></a>Использование данных Excel

Пакет SDK содержит функцию `read_excel()` для загрузки файлов Excel. По умолчанию эта функция загружает первый лист из книги. Чтобы выбрать для загрузки определенный лист, определите параметр `sheet_name` со именем листа в строковом формате.

```python
dflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dflow.head(5)
```

| |Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8| | |
|-|-------|-------|-------|-------|-------|-------|-------|-------|-|-|
|0|Нет|Нет|Нет|Нет|Нет|Нет|Нет|Нет|Нет| |
|1|Нет|Нет|Нет|Нет|Нет|Нет|Нет|Нет|Нет| |
|2|Нет|Нет|Нет|Нет|Нет|Нет|Нет|Нет|Нет| |
|3|RANK;|Название|Студия|Весь мир|Эта страна / %|Column1|Другие страны / %|Column2|Год ^| |
|4.|1|Аватар|Фокс|2788|760,5|0,273|2027,5|0,727|2009^|5|

Эти выходные данные показывают, что данные на втором листе содержат три пустых строки перед заголовками. Функция `read_excel()` принимает необязательные параметры для пропуска строк и использование заголовков. Запустите следующий код, который пропускает первые три строки и использует четвертую для получения заголовков.

```python
dflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_column_headers=True, skip_rows=3)
```

||RANK;|Название|Студия|Весь мир|Эта страна / %|Column1|Другие страны / %|Column2|Год ^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Аватар|Фокс|2788|760,5|0,273|2027,5|0,727|2009^|
|1|2|Титаник|Par.|2186,8|658,7|0,301|1528,1|0,699|1997^|

## <a name="load-fixed-width-data-files"></a>Использование файлов данных с фиксированной шириной

Для загрузки файлов с фиксированной шириной, укажите список смещения знаков. Предполагается, что первый столбец всегда начинается со смещения 0.

```python
dflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dflow.head(5)
```

||010000|99999|BOGUS NORWAY|НЕТ|NO_1|ENRS|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|BOGUS NORWAY|НЕТ|НЕТ|ENSO||||
|1|010010|99999|JAN MAYEN|НЕТ|JN|ENJA|+70933|-008667|+00090|


Чтобы избежать обнаружения заголовков и правильно получить данные, передайте значение `PromoteHeadersMode.NONE` в параметре `header`.

```python
dflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)
```

||Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|BOGUS NORWAY|НЕТ|NO_1|ENRS|Column7|Column8|Column9|
|1|010003|99999|BOGUS NORWAY|НЕТ|НЕТ|ENSO||||


## <a name="load-sql-data"></a>Загрузка данных SQL

Этот пакет SDK умеет загружать данные из источника SQL. В настоящее время поддерживается только Microsoft SQL Server. Чтобы считать данные из SQL server, создайте [ `MSSQLDataSource` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep.mssqldatasource?view=azure-dataprep-py) объект, который содержит параметры подключения. Параметр пароля `MSSQLDataSource` принимает [ `Secret` ](https://docs.microsoft.com/python/api/azureml-dataprep/azureml.dataprep?view=azure-dataprep-py#register-secret-value--str--id--str---none-----azureml-dataprep-api-engineapi-typedefinitions-secret) объекта. Объект секрета можно создать двумя способами.

* Зарегистрируйте секрет и его значение в подсистеме выполнения.
* Чтобы создать секрет только с полем `id` (если значение секрета уже зарегистрировано в среде выполнения), выполнив команду `dprep.create_secret("[SECRET-ID]")`.

```python
secret = dprep.register_secret(value="[SECRET-PASSWORD]", id="[SECRET-ID]")

ds = dprep.MSSQLDataSource(server_name="[SERVER-NAME]",
                           database_name="[DATABASE-NAME]",
                           user_name="[DATABASE-USERNAME]",
                           password=secret)
```

Завершив создание объекта источника данных, из него можно считать данные, возвращаемые запросом.

```python
dflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dflow.head(5)
```

| |ProductID|ИМЯ|ProductNumber|Цвет|StandardCost|ListPrice|Размер|Вес|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|ThumbNailPhoto|ThumbnailPhotoFileName|rowguid|ModifiedDate| |
|-|---------|----|-------------|-----|------------|---------|----|------|-----------------|--------------|-------------|-----------|----------------|--------------|----------------------|-------|------------|-|
|0|680|HL Road Frame - Black, 58|FR-R92B-58|Черный|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|Нет|Нет|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000+00:00|
|1|706|HL Road Frame - Red, 58|FR-R92R-58|Красный|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|Нет|Нет|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000+00:00|
|2|707|Шлем Sport-100, красный|HL-U509-R|Красный|13.0863|34.99|Нет|Нет|35|33|2005-07-01 00:00:00+00:00|Нет|Нет|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000+00:00|


## <a name="use-azure-data-lake-storage"></a>Использование Azure Data Lake Storage

Существует два способа получения пакетом SDK необходимого токена OAuth для доступа к хранилищу Azure Data Lake Storage:

* Получить маркер доступа из текущего сеанса пользователя в интерфейсе командной строки Azure
* Использовать субъект-службу (SP) и сертификат как секрет

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>Использовать маркер доступа из недавнего сеанса интерфейса командной строки Azure

На локальном компьютере выполните следующую команду.

```azurecli
az login
az account show --query tenantId
dflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dflow.head(5) head
```

> [!NOTE]
> Если учетная запись пользователя является участником нескольких клиентов Azure, необходимо указать соответствующий клиент в форме имени хоста URL-адреса AAD.

### <a name="create-a-service-principal-with-the-azure-cli"></a>Создание субъекта-службы с помощью Azure CLI

Примените интерфейс командной строки Azure для создания субъекта-службы и соответствующего сертификата. Этот субъект-служба настроен в роли `reader`, а область его действия ограничена учетной записью dpreptestfiles в Azure Data Lake Storage.

```azurecli
az account set --subscription "Data Wrangling development"
az ad sp create-for-rbac -n "SP-ADLS-dpreptestfiles" --create-cert --role reader --scopes /subscriptions/35f16a99-532a-4a47-9e93-00305f6c40f2/resourceGroups/dpreptestfiles/providers/Microsoft.DataLakeStore/accounts/dpreptestfiles
```

Эта команда выдает `appId` и путь к файлу сертификата (обычно в домашней папке). Файл .crt содержит открытый сертификат и закрытый ключ в формате PEM.

```
openssl x509 -in adls-dpreptestfiles.crt -noout -fingerprint
```

Чтобы настроить ACL для файловой системы Azure Data Lake Storage, используйте objectId пользователя. В этом примере используется субъект-служба.

```azurecli
az ad sp show --id "8dd38f34-1fcb-4ff9-accd-7cd60b757174" --query objectId
```

Чтобы настроить доступ `Read` и `Execute` для файловой системы Azure Data Lake Storage, настройте ACL для папок и файлов по отдельности. Это связано с тем, что базовая модель HDFS ACL не поддерживает наследование.

```azurecli
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r-x" --path /
az dls fs access set-entry --account dpreptestfiles --acl-spec "user:e37b9b1f-6a5e-4bee-9def-402b956f4e6f:r--" --path /farmers-markets.csv
```

```
certThumbprint = 'C2:08:9D:9E:D1:74:FC:EB:E9:7E:63:96:37:1C:13:88:5E:B9:2C:84'
certificate = ''
with open('./data/adls-dpreptestfiles.crt', 'rt', encoding='utf-8') as crtFile:
    certificate = crtFile.read()

servicePrincipalAppId = "8dd38f34-1fcb-4ff9-accd-7cd60b757174"
```

### <a name="acquire-an-oauth-access-token"></a>Получение маркера доступа OAuth

Используйте пакет `adal` (`pip install adal`), чтобы создать контекст аутентификации на клиенте MSFT и получить маркер доступа OAuth. Сведения об использовании adls ресурс в запросе маркера должен быть для "https:\//datalake.azure.net", который отличается от большинства других ресурсов Azure.

```python
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext('https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate('https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dflow = dprep.read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dflow.to_pandas_dataframe().head()
```

||FMID|MarketName|Веб-сайт|street|city|Округ|
|----|------|-----|----|----|----|----|
|0|1012063|Caledonia Farmers Market Association, Дэнвилл|https://sites.google.com/site/caledoniafarmers... ||Дэнвилл|Каледония|
|1|1011871|Stearns Homestead Farmers' Market|http://Stearnshomestead.com |6975 Ridge Road|Парма|Кайахога|
|2|1011878|100 Mile Market|https://www.pfcmarkets.com |507 Harrison St|Каламазу|Каламазу|
|3|1009364|106 S. Main Street Farmers Market|http://thetownofsixmile.wordpress.com/ |106 S. Main Street|Сикс Майл|||
|4.|1010691|10th Steet Community Farmers Market|https://agrimissouri.com/... |10th Street and Poplar|Ламар|Бартон|

## <a name="next-steps"></a>Дальнейшие действия

* Пакет SDK см. в разделе [Обзор](https://aka.ms/data-prep-sdk) конструктивные шаблоны и примеры использования
* См. в Azure Machine Learning данных подготовки SDK [руководстве](tutorial-data-prep.md) пример решения в определенной ситуации
