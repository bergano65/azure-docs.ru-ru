---
title: Просмотр данных в хранилище BLOB-объектов Azure с помощью Pandas — Процесс обработки и анализа данных группы
description: Как исследовать данные, которые хранятся в контейнере BLOB-объектов Azure, с помощью пакета Python Pandas.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/09/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3c399491f0a2048fe924e9ed9600dd5ce3899ca2
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59798559"
---
# <a name="explore-data-in-azure-blob-storage-with-pandas"></a>Просмотр данных в хранилище BLOB-объектов Azure с помощью Pandas

В этой статье рассказывается, как исследовать данные, которые хранятся в контейнере BLOB-объектов Azure, с помощью пакета Python [Pandas](https://pandas.pydata.org/).

Эта задача является одним из этапов [процесса обработки и анализа данных группы](overview.md).

## <a name="prerequisites"></a>Технические условия
В этой статье предполагается, что вы:

* Создали учетную запись хранения Azure. Инструкции см. в разделе [Создание учетной записи хранения](../../storage/common/storage-quickstart-create-account.md).
* Сохранили данные в учетной записи хранилища больших двоичных объектов Azure. Инструкции можно найти в статье [Перемещение данных в службу хранилища Azure и обратно](../../storage/common/storage-moving-data.md)

## <a name="load-the-data-into-a-pandas-dataframe"></a>Загрузка данных в кадр данных Pandas
Для просмотра набора данных и управления им набор необходимо сначала скачать из источника больших двоичных объектов в локальный файл, который в последствии можно загрузить в кадр данных Pandas. Ниже приведен порядок выполнения данной процедуры.

1. Скачайте данные из большого двоичного объекта Azure с помощью службы BLOB-объектов. Для этого воспользуйтесь приведенным ниже примером кода Python. Замените переменные в этом коде своими значениями.

```python
from azure.storage.blob import BlockBlobService
import tables

STORAGEACCOUNTNAME= <storage_account_name>
STORAGEACCOUNTKEY= <storage_account_key>
LOCALFILENAME= <local_file_name>
CONTAINERNAME= <container_name>
BLOBNAME= <blob_name>

#download from blob
t1=time.time()
blob_service=BlockBlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILENAME)
t2=time.time()
print(("It takes %s seconds to download "+blobname) % (t2 - t1))
```

1. Прочитайте данные, которые содержит скачанный файл, в блоке данных Pandas.

```python
#LOCALFILE is the file path
dataframe_blobdata = pd.read_csv(LOCALFILE)
```

Теперь вы готовы просматривать эти данные и создавать функции на основе этого набора данных.

## <a name="blob-dataexploration"></a>Примеры просмотра данных с помощью Pandas
Вот несколько примеров того, как можно просматривать данные с помощью Pandas.

1. Проверьте **количество строк и столбцов**

```python
print 'the size of the data is: %d rows and  %d columns' % dataframe_blobdata.shape
```

1. **Проверьте** первые или последние несколько **строк** в следующем наборе данных:

```python
dataframe_blobdata.head(10)

dataframe_blobdata.tail(10)
```

1. Проверьте **тип данных** , импортированный в каждый столбец, с помощью приведенного ниже примера кода.

```python
for col in dataframe_blobdata.columns:
    print dataframe_blobdata[col].name, ':\t', dataframe_blobdata[col].dtype
```

1. Проверьте **основную статистику** , относящуюся к столбцам набора данных, так, как показано ниже.

```python
dataframe_blobdata.describe()
```

1. Проверьте количество записей в каждом столбце так, как показано ниже.

```python
dataframe_blobdata['<column_name>'].value_counts()
```

1. **Проверьте соотношение числа отсутствующих значений** к фактическому количеству записей в каждом столбце. Воспользуйтесь приведенным ниже примером кода.

```python
miss_num = dataframe_blobdata.shape[0] - dataframe_blobdata.count()
print miss_num
```

1. Если в том или ином столбце данных **отсутствуют значения** , вы можете заменить их так, как показано ниже.

```python
dataframe_blobdata_noNA = dataframe_blobdata.dropna()
dataframe_blobdata_noNA.shape
```

Другой способ заменить отсутствующие значения — воспользоваться функцией режима.

```python
dataframe_blobdata_mode = dataframe_blobdata.fillna({'<column_name>':dataframe_blobdata['<column_name>'].mode()[0]})
```

1. Создайте **гистограмму**, используя переменное количество ячеек, чтобы построить распределение переменной.

```python
dataframe_blobdata['<column_name>'].value_counts().plot(kind='bar')

np.log(dataframe_blobdata['<column_name>']+1).hist(bins=50)
```

1. Проверьте **корреляции** между переменными, используя диаграмму рассеяния или встроенную функцию корреляции.

```python
#relationship between column_a and column_b using scatter plot
plt.scatter(dataframe_blobdata['<column_a>'], dataframe_blobdata['<column_b>'])

#correlation between column_a and column_b
dataframe_blobdata[['<column_a>', '<column_b>']].corr()
```
