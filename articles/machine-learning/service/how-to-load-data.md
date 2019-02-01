---
title: 'Загрузка: пакет SDK Python для подготовки данных'
titleSuffix: Azure Machine Learning service
description: Узнайте, как загружать данные с помощью пакета SDK для подготовки данных машинного обучения Azure. Можно загрузить различные типы входных данных, указать типы и параметры файлов данных или воспользоваться функцией интеллектуального чтения SDK, чтобы автоматически обнаружить тип файла.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: cforbe
author: cforbe
manager: cgronlun
ms.reviewer: jmartens
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 08dcb75fabc109a8869151402d3a448333beb556
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247533"
---
# <a name="load-and-read-data-with-azure-machine-learning"></a>Загрузка и чтение данных с помощью службы "Машинное обучение Azure"

В этой статье рассмотрены разные методы загрузки данных с помощью [пакета SDK службы "Машинное обучение Azure" для подготовки данных](https://aka.ms/data-prep-sdk). Этот пакет SDK поддерживает несколько функций приема данных, включая:

* загрузка из файлов многих типов с автоматическим обнаружением параметров для синтаксического анализа (кодировка, разделитель, заголовки);
* преобразование типов на основе зависимостей во время загрузки файла;
* поддержка подключений к MS SQL Server и хранилищу Azure Data Lake.

## <a name="load-data-automatically"></a>Автоматическая загрузка данных

Используйте функцию `auto_read_file()`, чтобы автоматически загружать данные без указания типа файла. Тип файла и аргументы для их чтения определяются автоматически.

```python
import azureml.dataprep as dprep

dataflow = dprep.auto_read_file(path='./data/any-file.txt')
```

Эта функция используется для автоматического обнаружения типа файла, кодирования и других видов анализа аргументов из единой удобной точки входа. Функция также автоматически выполняет следующие действия, которые часто выполняются во время загрузки данных с разделителями:

* определение и настройка разделителя;
* пропуск пустых записей в начале файла;
* определение и настройка строки заголовка.

Кроме того, если вам заранее известен тип файла и вы хотите четко контролировать способ его обработки, следуйте указаниям из этой статьи, чтобы узнать о специализированных функциях предлагаемой SDK.

## <a name="load-text-line-data"></a>Загрузка строковых текстовых данных

Чтобы поместить в поток данных простые текстовые данные, используйте `read_lines()` без дополнительных параметров.

```python
dataflow = dprep.read_lines(path='./data/text_lines.txt')
dataflow.head(5)
```

||график;|
|----|-----|
|0|Дата\|\| Минимальная температура\|\| Максимальная температура|
|1|2015-07-1 \|\| -4,1 \|\| 10,0|
|2|2015-07-2 \|\| -0,8 \|\| 10,8|
|3|2015-07-3 \|\| -7,0 \|\| 10,5|
|4.|2015-07-4 \|\| -5,5 \|\| 9,3|

После приема данных выполните следующий код, чтобы преобразовать объект потока данных в таблицу данных Pandas.

```python
pandas_df = dataflow.to_pandas_dataframe()
```

## <a name="load-csv-data"></a>Загрузка данных в формате CSV

При чтении файлов с разделителями среда выполнения может вычислить параметры для анализа (например, найти разделители и кодировка, выбрать заголовки и т. д.). Выполните представленный ниже фрагмент кода, чтобы попытаться прочитать файл, указав только его местоположение.

```python
# SAS expires June 16th, 2019
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv?st=2018-06-15T23%3A01%3A42Z&se=2019-06-16T23%3A01%3A00Z&sp=r&sv=2017-04-17&sr=b&sig=ugQQCmeC2eBamm6ynM7wnI%2BI3TTDTM6z9RPKj4a%2FU6g%3D')
dataflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0||stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|1|АЛАБАМА|1|101710|Округ Хейл|10171002158| |
|2|АЛАБАМА|1|101710|Округ Хейл|10171002162| |
|3|АЛАБАМА|1|101710|Округ Хейл|10171002156| |
|4.|АЛАБАМА|1|101710|Округ Хейл|10171000588|2|

Чтобы исключить строки во время загрузки, определите параметр `skip_rows`. Этот параметр пропускает загрузку из CSV-файле всех строк ниже указанной (по индексу строк, начиная с единицы).

```python
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1)
dataflow.head(5)
```

| |stnam|fipst|leaid|leanm10|ncessch|MAM_MTH00numvalid_1011|
|-----|-------|---------| -------|------|-----|------|-----|
|0|АЛАБАМА|1|101710|Округ Хейл|10171002158|29|
|1|АЛАБАМА|1|101710|Округ Хейл|10171002162|40 |
|2|АЛАБАМА|1|101710|Округ Хейл|10171002156| 43|
|3|АЛАБАМА|1|101710|Округ Хейл|10171000588|2|
|4.|АЛАБАМА|1|101710|Округ Хейл|10171000589|23 |

Выполните следующий код, чтобы отобразить типы данных для столбцов.

```python
dataflow.head(1).dtypes

stnam                     object
fipst                     object
leaid                     object
leanm10                   object
ncessch                   object
schnam10                  object
MAM_MTH00numvalid_1011    object
dtype: object
```

По умолчанию SDK службы "Машинное обучение Azure" для подготовки данных не меняет тип данных. Сейчас мы считываем данные из текстового файла, поэтому пакет SDK считает все значения строковыми. Но в этом примере числовые столбцы нужно анализировать как числа. Задайте для параметра `inference_arguments` значение `InferenceArguments.current_culture()`, чтобы автоматически определять и преобразовывать типы столбцов во время чтения файлов.

```
dataflow = dprep.read_csv(path='https://dpreptestfiles.blob.core.windows.net/testfiles/read_csv_duplicate_headers.csv',
                          skip_rows=1,
                          inference_arguments=dprep.InferenceArguments.current_culture())
dataflow.head(1).dtypes

stnam                      object
fipst                     float64
leaid                     float64
leanm10                    object
ncessch                   float64
schnam10                   object
ALL_MTH00numvalid_1011    float64
dtype: object
```

Несколько столбцов правильно определяются как числовые и им присваивается тип данных `float64`.

## <a name="use-excel-data"></a>Использование данных Excel

Пакет SDK содержит функцию `read_excel()` для загрузки файлов Excel. По умолчанию эта функция загружает первый лист из книги. Чтобы выбрать для загрузки определенный лист, определите параметр `sheet_name` со именем листа в строковом формате.

```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2')
dataflow.head(5)
```

||Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8|
|------|------|------|-----|------|-----|-------|----|-----|
|0|Нет|Нет|Нет|Нет|Нет|Нет|Нет|Нет|Нет|
|1|Нет|Нет|Нет|Нет|Нет|Нет|Нет|Нет|Нет|
|2|Нет|Нет|Нет|Нет|Нет|Нет|Нет|Нет|Нет|
|3|RANK;|Название|Студия|Весь мир|Эта страна / %|Column1|Другие страны / %|Column2|Год ^|
|4.|1|Аватар|Фокс|2788|760,5|0,273|2027,5|0,727|2009^|5|

Эти выходные данные показывают, что данные на втором листе содержат три пустых строки перед заголовками. Функция `read_excel()` принимает необязательные параметры для пропуска строк и использование заголовков. Запустите следующий код, который пропускает первые три строки и использует четвертую для получения заголовков.

```python
dataflow = dprep.read_excel(path='./data/excel.xlsx', sheet_name='Sheet2', use_column_headers=True, skip_rows=3)
```

||RANK;|Название|Студия|Весь мир|Эта страна / %|Column1|Другие страны / %|Column2|Год ^|
|------|------|------|-----|------|-----|-------|----|-----|-----|
|0|1|Аватар|Фокс|2788|760,5|0,273|2027,5|0,727|2009^|
|1|2|Титаник|Par.|2186,8|658,7|0,301|1528,1|0,699|1997^|
|2|3|Мстители (Marvel)|BV|1518,6|623,4|0,41|895,2|0,59|2012|
|3|4.|Гарри Поттер и Дары Смерти. Часть 2|WB|1341,5|381|0,284|960,5|0,716|2011|
|4.|5|Холодное сердце|BV|1274,2|400,7|0,314|873,5|0,686|2013|

## <a name="load-fixed-width-data-files"></a>Использование файлов данных с фиксированной шириной

Чтобы загрузить файлы с фиксированной шириной, необходимо указать список символьных смещений. Предполагается, что первый столбец всегда начинается со смещения 0.

```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt', offsets=[7, 13, 43, 46, 52, 58, 65, 73])
dataflow.head(5)
```

||010000|99999|BOGUS NORWAY|НЕТ|NO_1|ENRS|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010003|99999|BOGUS NORWAY|НЕТ|НЕТ|ENSO||||
|1|010010|99999|JAN MAYEN|НЕТ|JN|ENJA|+70933|-008667|+00090|
|2|010013|99999|ROST|НЕТ|НЕТ|||||
|3|010014|99999|SOERSTOKKEN|НЕТ|НЕТ|ENSO|+59783|+005350|+00500|
|4.|010015|99999|BRINGELAND|НЕТ|НЕТ|ENBL|+61383|+005867|+03270|

Чтобы избежать обнаружения заголовков и правильно получить данные, передайте значение `PromoteHeadersMode.NONE` в параметре `header`.

```python
dataflow = dprep.read_fwf('./data/fixed_width_file.txt',
                          offsets=[7, 13, 43, 46, 52, 58, 65, 73],
                          header=dprep.PromoteHeadersMode.NONE)
```

||Column1|Column2|Column3|Column4|Column5|Column6|Column7|Column8|Column9|
|------|------|------|-----|------|-----|-------|----|-----|----|
|0|010000|99999|BOGUS NORWAY|НЕТ|NO_1|ENRS|Column7|Column8|Column9|
|1|010003|99999|BOGUS NORWAY|НЕТ|НЕТ|ENSO||||
|2|010010|99999|JAN MAYEN|НЕТ|JN|ENJA|+70933|-008667|+00090|
|3|010013|99999|ROST|НЕТ|НЕТ|||||
|4.|010014|99999|SOERSTOKKEN|НЕТ|НЕТ|ENSO|+59783|+005350|+00500|
|5|010015|99999|BRINGELAND|НЕТ|НЕТ|ENBL|+61383|+005867|+03270|

## <a name="load-sql-data"></a>Загрузка данных SQL

Этот пакет SDK умеет загружать данные из источника SQL. В настоящее время поддерживается только Microsoft SQL Server. Чтобы прочитать данные с SQL Server, создайте объект `MSSQLDataSource`, который содержит параметры подключения. Параметр пароля `MSSQLDataSource` принимает объект `Secret`. Объект секрета можно создать двумя способами.

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
dataflow = dprep.read_sql(ds, "SELECT top 100 * FROM [SalesLT].[Product]")
dataflow.head(5)
```

||ProductID|ИМЯ|ProductNumber|Цвет|StandardCost|ListPrice|Размер|Вес|ProductCategoryID|ProductModelID|SellStartDate|SellEndDate|DiscontinuedDate|ThumbNailPhoto|ThumbnailPhotoFileName|rowguid|ModifiedDate|
|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
|0|680|HL Road Frame - Black, 58|FR-R92B-58|Черный|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|Нет|Нет|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|43dd68d6-14a4-461f-9069-55309d90ea7e|2008-03-11 |0:01:36.827000+00:00|
|1|706|HL Road Frame - Red, 58|FR-R92R-58|Красный|1059.3100|1431.50|58|1016.04|18|6|2002-06-01 00:00:00+00:00|Нет|Нет|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|9540ff17-2712-4c90-a3d1-8ce5568b2462|2008-03-11 |10:01:36.827000+00:00|
|2|707|Шлем Sport-100, красный|HL-U509-R|Красный|13.0863|34.99|Нет|Нет|35|33|2005-07-01 00:00:00+00:00|Нет|Нет|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|2e1ef41a-c08a-4ff6-8ada-bde58b64a712|2008-03-11 |10:01:36.827000+00:00|
|3|708|Шлем Sport-100, черный|HL-U509|Черный|13.0863|34.99|Нет|Нет|35|33|2005-07-01 00:00:00+00:00|Нет|Нет|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|a25a44fb-c2de-4268-958f-110b8d7621e2|2008-03-11 |10:01:36.827000+00:00|
|4.|709|Носки для горного велосипеда, M|SO-B909-M|Белый|3.3963|9.50|M|Нет|27|18|2005-07-01 00:00:00+00:00|2006-06-30 00:00:00+00:00|Нет|b'GIF89aP\x001\x00\xf7\x00\x00\x00\x00\x00\x80...|no_image_available_small.gif|18f95f47-1540-4e02-8f1f-cc1bcb6828d0|2008-03-11 |10:01:36.827000+00:00|

## <a name="use-azure-data-lake-storage"></a>Использование Azure Data Lake Storage

Существует два способа получения пакетом SDK необходимого токена OAuth для доступа к хранилищу Azure Data Lake Storage:

* Получить маркер доступа из текущего сеанса пользователя в интерфейсе командной строки Azure
* Использовать субъект-службу (SP) и сертификат как секрет

### <a name="use-an-access-token-from-a-recent-azure-cli-session"></a>Использовать маркер доступа из недавнего сеанса интерфейса командной строки Azure

На локальном компьютере выполните следующую команду.

```azurecli
az login
az account show --query tenantId
dataflow = read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', tenant='microsoft.onmicrosoft.com')) head = dataflow.head(5) head
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

Используйте пакет `adal` (`pip install adal`), чтобы создать контекст аутентификации на клиенте MSFT и получить маркер доступа OAuth. Что касается ADLS, ресурс в запросе маркера должен предназначаться https://datalake.azure.net, отличающемуся от большинства других ресурсов Azure.

```python
import adal
from azureml.dataprep.api.datasources import DataLakeDataSource

ctx = adal.AuthenticationContext('https://login.microsoftonline.com/microsoft.onmicrosoft.com')
token = ctx.acquire_token_with_client_certificate('https://datalake.azure.net/', servicePrincipalAppId, certificate, certThumbprint)
dataflow = dprep.read_csv(path = DataLakeDataSource(path='adl://dpreptestfiles.azuredatalakestore.net/farmers-markets.csv', accessToken=token['accessToken']))
dataflow.to_pandas_dataframe().head()
```

||FMID|MarketName|Веб-сайт|street|city|Округ|
|----|------|-----|----|----|----|----|
|0|1012063|Caledonia Farmers Market Association, Дэнвилл|https://sites.google.com/site/caledoniafarmers... ||Дэнвилл|Каледония|
|1|1011871|Stearns Homestead Farmers' Market|http://Stearnshomestead.com |6975 Ridge Road|Парма|Кайахога|
|2|1011878|100 Mile Market|http://www.pfcmarkets.com |507 Harrison St|Каламазу|Каламазу|
|3|1009364|106 S. Main Street Farmers Market|http://thetownofsixmile.wordpress.com/ |106 S. Main Street|Сикс Майл|||
|4.|1010691|10th Steet Community Farmers Market|https://agrimissouri.com/... |10th Street and Poplar|Ламар|Бартон|
