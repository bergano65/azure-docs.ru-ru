---
title: Просмотр данных и модели в Windows
titleSuffix: Azure Data Science Virtual Machine
description: Выполнение задач по исследованию и моделированию данных на виртуальной машине Windows для обработки и анализа данных.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 75a685dc90db9133ee9bc5d52d046246270ea32a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73497616"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>10 задач, которые можно выполнить на виртуальной машине Windows для обработки и анализа данных

Виртуальная машина Windows для обработки и анализа данных (DSVM) — это мощная среда разработки для обработки и анализа данных, в которой можно выполнять задачи по исследованию и моделированию данных. Среда уже создана и объединена с несколькими популярными средствами анализа данных, которые упрощают начало работы с анализом для локальных, облачных и гибридных развертываний. 

DSVM тесно работает со службами Azure. Он может считывать и обрабатывать данные, которые уже хранятся в Azure, в хранилище данных SQL Azure, Azure Data Lake, службе хранилища Azure или Azure Cosmos DB. Он также может использовать другие средства аналитики, такие как Машинное обучение Azure и фабрика данных Azure.

В этой статье вы узнаете, как использовать DSVM для выполнения задач обработки и анализа данных и взаимодействия с другими службами Azure. Вот некоторые задачи, которые можно выполнить в DSVM:

- Изучите данные и разрабатывайте модели локально в DSVM с помощью Microsoft Machine Learning Server и Python.
- Используйте записную книжку Jupyter для экспериментирования с данными в браузере с помощью Python 2, Python 3 и Microsoft R. (Microsoft R — это готовая к использованию Корпоративная версия R, предназначенная для повышения производительности.)
- Развертывайте модели, созданные с помощью R и Python, на Машинное обучение Azure поэтому клиентские приложения могут получать доступ к моделям с помощью простого интерфейса веб-службы.
- Администрирование ресурсов Azure с помощью портал Azure или PowerShell.
- Расширьте пространство в хранилище и делитесь большими наборами данных и кодом во всей группе, создав файловый ресурс Azure в качестве подключаемого диска в DSVM.
- Поделитесь кодом с командой с помощью GitHub. Доступ к репозиторию с помощью предварительно установленных клиентов Git: Git Bash и GUI Git.
- Доступ к службам данных и аналитикам Azure, таким как хранилище BLOB-объектов Azure, Azure Data Lake, Azure HDInsight (Hadoop), Azure Cosmos DB, хранилище данных SQL Azure и база данных SQL Azure.
- Создание отчетов и панели мониторинга с помощью экземпляра Power BI Desktop, предварительно установленного на DSVM, и развертывание их в облаке.
- Динамическое масштабирование DSVM в соответствии с потребностями проекта.
- Установите на виртуальной машине дополнительные средства.   

> [!NOTE]
> Дополнительные платы за использование относятся ко многим службам хранения данных и аналитики, перечисленным в этой статье. Дополнительные сведения см. на странице [цен на Azure](https://azure.microsoft.com/pricing/) .
> 
> 

## <a name="prerequisites"></a>Технические условия

* Вам понадобится подписка Azure. Вы можете [воспользоваться бесплатной пробной версией](https://azure.microsoft.com/free/).
* Инструкции по подготовке виртуальной машины для обработки и анализа данных на портал Azure доступны при [создании виртуальной машины](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Изучите данные и разрабатывайте модели с помощью Microsoft Machine Learning Server
Используя такие языки, как R и Python, анализ данных можно выполнять непосредственно в DSVM.

Для R можно использовать интегрированную среду разработки, например RStudio, которую можно найти в меню "Пуск" или на рабочем столе. Или можно использовать Инструменты R для Visual Studio. Корпорация Майкрософт предоставила дополнительные библиотеки на основе CRAN R с открытым исходным кодом, чтобы обеспечить масштабируемую аналитику и возможность анализировать данные, превышающие размер памяти, допустимый при параллельном анализе фрагментированных данных. 

Для Python можно использовать такую интегрированную среду разработки, как Visual Studio Community Edition, в которой предустановлено расширение Python Tools for Visual Studio (PTVS). По умолчанию в PTVS настраивается только Python 3,6, Корневая среда Conda. Чтобы включить Anaconda Python 2,7, выполните следующие действия.

1. Создайте пользовательские среды для каждой версии, перейдя в **меню инструменты** > **средства Python** > **среды Python**, а затем выбрав **+ Custom** в Visual Studio Community Edition.
1. Введите описание и задайте путь к префиксу окружения в виде **c:\anaconda\envs\python2** для anaconda Python 2,7.
1. Выберите **Автоматическое обнаружение** > **Применить** , чтобы сохранить окружение.

Дополнительные сведения о создании окружений Python см. в [документации по PTVS](https://aka.ms/ptvsdocs) .

Теперь вы настроили создание нового проекта Python. Перейдите в **файл** > **новый** > **проект** > **Python** и выберите тип приложения Python, которое вы собираете. Можно задать среду Python для текущего проекта до нужной версии (Python 2,7 или 3,6), щелкнув правой кнопкой мыши **среду Python** и выбрав пункт **Добавить или удалить окружения Python**. Дополнительные сведения о работе с PTVS можно найти в документации по [продукту](https://aka.ms/ptvsdocs).

## <a name="use-jupyter-notebooks"></a>Использование записных книжек Jupyter
Jupyter Notebook предоставляет интегрированную среду разработки для просмотра и моделирования данных на основе браузера. Вы можете использовать Python 2, Python 3 или R (с открытым исходным кодом и Microsoft R Server) в записной книжке Jupyter.

Чтобы запустить Jupyter Notebook, выберите значок **Jupyter Notebook** в меню " **Пуск** " или на рабочем столе. В командной строке DSVM можно также выполнить команду ```jupyter notebook``` из каталога, в котором находятся существующие записные книжки или где вы хотите создать новые записные книжки.  

После запуска Jupyter вы увидите каталог, содержащий несколько примеров записных книжек, которые предварительно упакованы в DSVM. Теперь вы можете:

* Выберите записную книжку, чтобы просмотреть код.
* Выполните каждую ячейку, нажав клавиши SHIFT + ВВОД.
* Запустите всю записную книжку, выбрав **ячейку** > **выполнить**.
* Создайте новую записную книжку, щелкнув значок Jupyter (в левом верхнем углу), нажав кнопку **создать** справа, а затем выбрав язык записной книжки (также известный как ядра).   

> [!NOTE]
> В настоящее время поддерживаются ядра Python 2,7, Python 3,6, R, Julia и PySpark в Jupyter. Ядро R поддерживает программирование на языке R и Microsoft R с открытым исходным кодом.   
> 
> 

Когда Вы находитесь в записной книжке, вы можете исследовать данные, построить модель и протестировать модель с помощью выбора библиотек.

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>Обучение и развертывание моделей с помощью Машинное обучение Azure
После создания и проверки модели следующим шагом обычно является развертывание ее в рабочей среде. Этот шаг позволяет клиентским приложениям вызывать прогнозы модели в режиме реального времени или на основе пакетного режима. Службе машинного обучения Azure доступен механизм ввода в эксплуатацию модели, созданной на языке R или Python.

При эксплуатациюии модели в Машинное обучение Azure предоставляется веб-служба. Он позволяет клиентам выполнять вызовы RESTFUL, которые передают входные параметры и получают прогнозы из модели в виде выходных данных.

### <a name="build-and-operationalize-python-models"></a>Создание и эксплуатацию моделей Python
Ниже приведен фрагмент кода, разработанный в записной книжке Python Jupyter, который создает простую модель с помощью библиотеки Scikit-учиться.

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

Метод, который используется для развертывания моделей Python в Машинное обучение Azure переносит Прогноз модели в функцию и добавляет его к атрибутам, предоставленным предустановленной библиотекой Машинное обучение Azure Python. Атрибуты обозначают идентификатор рабочей области Машинное обучение Azure, ключ API, а также входные и возвращаемые параметры.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

Теперь клиент может выполнять вызовы веб-службы. Удобные оболочки создают запросы REST API. Ниже приведен пример кода для использования веб-службы.

```python
# Consume through web service URL and keys
from azureml import services
@services.service(url, api_key)
@services.types(sep_l = float, sep_w = float, pet_l=float, pet_w=float)
@services.returns(float)
def IrisPredictor(sep_l, sep_w, pet_l, pet_w):
pass

IrisPredictor(3,2,3,4)
```

> [!NOTE]
> В настоящее время библиотека Машинное обучение Azure поддерживается только в Python 2,7.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Создание и эксплуатацию моделей R
Вы можете развернуть модели R, построенные на виртуальной машине для обработки и анализа данных, или в других местах, Машинное обучение Azure так же, как это делается для Python. Для этого выполните следующие действия:

1. Создайте файл Settings. JSON, чтобы указать идентификатор рабочей области и маркер проверки подлинности. 
2. Напишите оболочку для функции Predict модели.
3. Вызовите ```publishWebService``` в библиотеке Машинное обучение Azure, чтобы передать обертку функции.  

Используйте следующую процедуру и фрагменты кода для настройки, сборки, публикации и использования модели в качестве веб-службы в Машинное обучение Azure.

#### <a name="set-up"></a>Настройка

Создайте файл Settings. JSON в каталоге с именем ```.azureml``` в домашнем каталоге. Введите параметры из рабочей области Машинное обучение Azure.

Ниже приведена структура файла Settings. JSON.

```json
{"workspace":{
"id"                  : "ENTER YOUR AZUREML WORKSPACE ID",
"authorization_token" : "ENTER YOUR AZUREML AUTH TOKEN"
}}
```

#### <a name="build-a-model-in-r-and-publish-it-in-azure-machine-learning"></a>Создание модели на языке R и ее публикация в службе машинного обучения Azure

```r
library(AzureML)
ws <- workspace(config="~/.azureml/settings.json")

if(!require("lme4")) install.packages("lme4")
library(lme4)
set.seed(1)
train <- sleepstudy[sample(nrow(sleepstudy), 120),]
m <- lm(Reaction ~ Days + Subject, data = train)

# Define a prediction function to publish based on the model:
sleepyPredict <- function(newdata){
        predict(m, newdata=newdata)
}

ep <- publishWebService(ws, fun = sleepyPredict, name="sleepy lm", inputSchema = sleepstudy, data.frame=TRUE)
```

#### <a name="consume-the-model-deployed-in-azure-machine-learning"></a>Использование модели, развернутой в службе машинного обучения Azure
Чтобы использовать модель из клиентского приложения, используйте библиотеку Машинное обучение Azure для поиска опубликованной веб-службы по имени. Чтобы определить конечную точку, используйте вызов API `services`. Затем нужно просто вызвать функцию `consume` и передать блок данных для прогноза.

Используйте следующий код для использования модели, опубликованной в качестве веб-службы Машинное обучение Azure:

```r
library(AzureML)
library(lme4)
ws <- workspace(config="~/.azureml/settings.json")

s <-  services(ws, name = "sleepy lm")
s <- tail(s, 1) # use the last published function, in case of duplicate function names

ep <- endpoints(ws, s)

# Try this out, and compare with raw data
ans = consume(ep, sleepstudy)$ans
```

Дополнительные сведения о [пакетах R см. в машинное обучение Studio](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning).

## <a name="manage-azure-resources"></a>Управление ресурсами Azure
DSVM не только позволяет создавать аналитические решения локально на виртуальной машине. Она также позволяет получить доступ к службам на облачной платформе Azure. Azure предоставляет несколько вычислений, хранения, аналитики данных и других служб, которые можно администрировать и получать к ним доступ из DSVM.

Для администрирования подписки Azure и облачных ресурсов можно использовать два варианта:
+ Используйте браузер и перейдите к [портал Azure](https://portal.azure.com).

+ Используйте скрипты PowerShell. Запустите Azure PowerShell с помощью ярлыка на рабочем столе или из меню " **Пуск** ". Полные сведения см. в [документации по Microsoft Azure PowerShell](../../powershell-azure-resource-manager.md) . 

## <a name="extend-storage-by-using-shared-file-systems"></a>Расширение хранилища с помощью общих файловых систем
Специалисты по обработке и анализу данных могут использовать большие наборы данных, код или другие ресурсы вместе с участниками группы. DSVM имеет около 45 ГБ свободного места. Чтобы расширить хранилище, можно использовать файлы Azure и либо подключить его к одному или нескольким экземплярам DSVM, либо получить доступ к нему через REST API. Можно также использовать [портал Azure](../../virtual-machines/windows/attach-managed-disk-portal.md) или использовать [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) для добавления дополнительных выделенных дисков данных. 

> [!NOTE]
> Максимальный объем пространства в общей папке службы файлов Azure составляет 5 ТБ. Максимальный размер каждого файла составляет 1 ТБ. 

Этот скрипт можно использовать в Azure PowerShell для создания общей папки службы файлов Azure:

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you want.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure Files share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the file share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

Теперь, когда вы создали общую папку службы файлов Azure, ее можно подключить к любой виртуальной машине в Azure. Рекомендуется разместить виртуальную машину в том же центре обработки данных Azure, что и учетная запись хранения, чтобы избежать расходов на задержку и передачу данных. Ниже приведены Azure PowerShell команды для подключения диска к DSVM:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Теперь к этому диску можно обращаться как к обычному диску на виртуальной машине.

## <a name="share-code-in-github"></a>Совместное использование кода в GitHub
GitHub — это репозиторий кода, в котором можно найти примеры кода и источники для различных средств с помощью технологий, совместно используемых сообществом разработчиков. Git выступает в качестве технологии для отслеживания и хранения версий файлов кода. GitHub также является платформой, где можно создать собственный репозиторий для хранения общего кода и документации команды, реализовать управление версиями и контролировать доступ к коду для просмотра и Contribute. 

Дополнительные сведения об использовании Git см. на [страницах справки GitHub](https://help.github.com/). GitHub можно использовать как один из способов совместной работы с командой, использования кода, разработанного сообществом, и обратного участия в сообществе.

DSVM загружается с помощью клиентских средств из командной строки и графического пользовательского интерфейса для доступа к репозиторию GitHub. Программа командной строки, которая работает с Git и GitHub, называется git bash. Visual Studio устанавливается на DSVM и имеет расширения Git. Значки для этих инструментов можно найти в меню **Пуск** и на рабочем столе.

Для скачивания кода из репозитория GitHub используйте команду ```git clone```. Например, чтобы скачать репозиторий обработки и анализа данных, опубликованный корпорацией Майкрософт в текущем каталоге, можно выполнить следующую команду в Git Bash:

    git clone https://github.com/Azure/DataScienceVM.git

В Visual Studio можно выполнить ту же операцию клонирования. На следующем снимке экрана показано, как получить доступ к средствам Git и GitHub в Visual Studio:

![Снимок экрана Visual Studio с отображением соединения с GitHub](./media/vm-do-ten-things/VSGit.PNG)

Дополнительные сведения об использовании Git для работы с репозиторием GitHub можно найти в ресурсах, доступных в github.com. Вы также найдете полезную информацию в этой [памятке](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) .

## <a name="access-azure-data-and-analytics-services"></a>Доступ к данным и службам аналитики Azure
### <a name="azure-blob-storage"></a>Хранилище больших двоичных объектов Azure
Хранилище BLOB-объектов Azure — это надежная и экономичная служба облачного хранилища для больших и малых данных. В этом разделе описывается, как можно переместить данные в хранилище BLOB-объектов и получить доступ к данным, хранящимся в большом двоичном объекте Azure.

#### <a name="prerequisites"></a>Технические условия

* Создайте учетную запись хранилища BLOB-объектов Azure из [портал Azure](https://portal.azure.com).

   ![Снимок экрана процесса создания учетной записи хранения в портал Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Убедитесь, что средство командной строки AzCopy предварительно установлено: ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. Каталог, содержащий azcopy. exe, уже находится в переменной среды PATH, поэтому при запуске этого средства можно избежать ввода полного пути к команде. Дополнительные сведения о средстве AzCopy см. в [документации по AzCopy](../../storage/common/storage-use-azcopy.md).
* Запустите инструмент Azure Storage Explorer. Его можно загрузить на [веб-странице Обозреватель службы хранилища](https://storageexplorer.com/). 

   ![Снимок экрана Обозреватель службы хранилища Azure доступ к учетной записи хранения](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Перемещение данных из виртуальной машины в большой двоичный объект Azure: AzCopy

Чтобы переместить данные между локальными файлами и хранилищем BLOB-объектов, можно использовать AzCopy в командной строке или в PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Замените **C:\MyFolder** на путь, по которому хранится файл, **mystorageaccount** с именем учетной записи хранилища BLOB-объектов, **MyContainer** с именем контейнера и **ключом учетной записи хранения** с ключом доступа к хранилищу BLOB-объектов. Учетные данные учетной записи хранения можно найти в [портал Azure](https://portal.azure.com).

Выполните команду AzCopy в PowerShell или из командной строки. Ниже приведен пример использования команды AzCopy:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

После выполнения команды AzCopy для копирования в большой двоичный объект Azure файл появится в Обозреватель службы хранилища Azure.

![Снимок экрана учетной записи хранения с отправленным CSV-файлом](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Перемещение данных из виртуальной машины в большой двоичный объект Azure: Обозреватель службы хранилища Azure

Вы также можете передать данные из локального файла на виртуальной машине с помощью Обозреватель службы хранилища Azure:

* Чтобы отправить данные в контейнер, выберите целевой контейнер и нажмите кнопку Upload ( **Отправить** ).![снимок экрана кнопки "Отправить" в Обозреватель службы хранилища Azure](./media/vm-do-ten-things/storage-accounts.png)
* Нажмите кнопку с многоточием ( **...** ) справа от поля **файлы** , выберите один или несколько файлов для отправки из файловой системы и нажмите кнопку **Отправить** , чтобы начать отправку файлов. Снимок экрана диалогового окна "Передача файлов"![](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>Чтение данных из модуля чтения BLOB-объектов Azure: Машинное обучение

В Машинное обучение Azure Studio можно использовать модуль импорт данных для чтения данных из большого двоичного объекта.

![Снимок экрана модуля "Импорт данных" в студии машинного обучения](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Чтение данных из большого двоичного объекта Azure: ODBC Python

Библиотеку BlobService можно использовать для чтения данных непосредственно из большого двоичного объекта в записной книжке Jupyter или в программе Python.

Сначала импортируйте необходимые пакеты:

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
```

Затем подсоедините учетные данные учетной записи хранилища BLOB-объектов и прочтите данные из большого двоичного объекта:

```python
CONTAINERNAME = 'xxx'
STORAGEACCOUNTNAME = 'xxxx'
STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
localfilename = 'trip_data_1.csv'
LOCALDIRECTORY = os.getcwd()
LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

#download from blob
t1 = time.time()
blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
t2 = time.time()
print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

#unzip downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

Данные считываются как кадр данных:

![Снимок экрана первых 10 строк данных](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Озеро данных Azure
Azure Data Lake Storage — это геомасштабируемый репозиторий для рабочих нагрузок анализа больших данных и совместимый с Hadoop распределенная файловая система (HDFS). Оно работает с Hadoop, Spark и Azure Data Lake Analytics. В этом разделе вы узнаете, как перемещать данные в Azure Data Lake Storage и выполнять аналитику с помощью Azure Data Lake Analytics.

#### <a name="prerequisites"></a>Технические условия

* Создайте экземпляр Azure Data Lake Analytics в [портал Azure](https://portal.azure.com).

   ![Снимок экрана создания Data Lake Analytics экземпляра из портал Azure](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* [Средства Azure Data Lake и Stream Analytics для подключаемого модуля Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) уже установлены в выпуске Visual Studio Community на виртуальной машине. После запуска Visual Studio и входа в подписку Azure вы увидите учетную запись и хранилище аналитики данных Azure на левой панели Visual Studio.

   ![Снимок экрана подключаемого модуля для средств Data Lake в Visual Studio](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>Перемещение данных из виртуальной машины в Data Lake: обозреватель Azure Data Lake

Azure Data Lake Explorer можно использовать для [передачи данных из локальных файлов виртуальной машины в Data Lake Storage](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal).

Вы также можете создать конвейер данных для эксплуатацию перемещения данных в Azure Data Lake с помощью [фабрики данных Azure](https://azure.microsoft.com/services/data-factory/). В [этой статье](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) описываются шаги по созданию конвейеров данных.

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>Чтение данных из большого двоичного объекта Azure в Data Lake: U-SQL

Если данные хранятся в хранилище BLOB-объектов Azure, вы можете напрямую считывать данные из большого двоичного объекта Azure в запросе U-SQL. Прежде чем приступить к составлению запроса U-SQL, убедитесь, что учетная запись хранилища BLOB-объектов связана с вашим экземпляром Azure Data Lake. Перейдите в портал Azure, найдите панель мониторинга Azure Data Lake Analytics, выберите **Добавить источник данных**, выберите тип хранилища **хранилища Azure**и подключите имя и ключ учетной записи хранения Azure. Затем можно сослаться на данные, хранящиеся в учетной записи хранения.

![Снимок экрана: диалоговое окно "Добавление источника данных"](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

В Visual Studio можно считывать данные из хранилища BLOB-объектов, управлять данными, конструировать функции и передавать полученные данные в Azure Data Lake или хранилище BLOB-объектов Azure. При ссылке на данные в хранилище BLOB-объектов используйте **wasb://** . При ссылке на данные в Azure Data Lake используйте **swbhdfs://** .

В Visual Studio можно использовать следующие запросы U-SQL:

```usql
@a =
    EXTRACT medallion string,
            hack_license string,
            vendor_id string,
            rate_code string,
            store_and_fwd_flag string,
            pickup_datetime string,
            dropoff_datetime string,
            passenger_count int,
            trip_time_in_secs double,
            trip_distance double,
            pickup_longitude string,
            pickup_latitude string,
            dropoff_longitude string,
            dropoff_latitude string

    FROM "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Input Data File Name>"
    USING Extractors.Csv();

@b =
    SELECT vendor_id,
    COUNT(medallion) AS cnt_medallion,
    SUM(passenger_count) AS cnt_passenger,
    AVG(trip_distance) AS avg_trip_dist,
    MIN(trip_distance) AS min_trip_dist,
    MAX(trip_distance) AS max_trip_dist,
    AVG(trip_time_in_secs) AS avg_trip_time
    FROM @a
    GROUP BY vendor_id;

OUTPUT @b   
TO "swebhdfs://<Azure Data Lake Storage Account Name>.azuredatalakestore.net/<Folder Name>/<Output Data File Name>"
USING Outputters.Csv();

OUTPUT @b   
TO "wasb://<Container name>@<Azure Blob Storage Account Name>.blob.core.windows.net/<Output Data File Name>"
USING Outputters.Csv();
```

После отправки запроса на сервер на схеме отображается состояние задания.

![Снимок экрана: схема состояния задания](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>Запрос данных в Data Lake: U-SQL

После приема набора данных в Azure Data Lake можно использовать [язык U-SQL](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) для запроса и просмотра данных. Язык U-SQL подобен T-SQL, но объединяет некоторые функции из C# , чтобы пользователи могли создавать пользовательские модули и определяемые пользователем функции. Можно использовать сценарии из предыдущего шага.

После отправки запроса на сервер tripdata_summary. В обозревателе Azure Data Lake появится CSV-файл. Вы можете просмотреть данные, щелкнув файл правой кнопкой мыши.

![Снимок экрана CSV-файла в Data Lake Explorer](./media/vm-do-ten-things/USQL_create_summary.png)

Отобразятся сведения о файле:

![Снимок экрана сводных данных файла](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>Кластеры HDInsight Hadoop
Azure HDInsight — это управляемая служба Apache Hadoop, Spark, HBase и в облаке. Вы можете легко работать с кластерами Azure HDInsight из виртуальной машины для обработки и анализа данных.

#### <a name="prerequisites"></a>Технические условия

* Создайте учетную запись хранилища BLOB-объектов Azure из [портал Azure](https://portal.azure.com). Она используется для хранения данных кластеров HDInsight.

   ![Снимок экрана создания учетной записи хранения из портал Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Настройка Azure HDInsight Hadoop кластеров из [портал Azure](../team-data-science-process/customize-hadoop-cluster.md).
  
   Свяжите учетную запись хранения, созданную с помощью кластера HDInsight, при его создании. Эта учетная запись хранения используется для доступа к данным, которые можно обработать в пределах кластера.

   ![Параметры для связывания учетной записи хранения, созданной с помощью кластера HDInsight](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Включите удаленный рабочий стол доступ к головному узлу кластера после его создания. Запомните указанные здесь учетные данные для удаленного доступа, так как они понадобятся вам в следующей процедуре.

   ![Кнопка удаленный рабочий стол для включения удаленного доступа к кластеру HDInsight](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Создайте рабочую область машинного обучения Azure. Машинное обучение эксперименты хранятся в этой Машинное обучение рабочей области. Выберите выделенные параметры на портале, как показано на следующем снимке экрана:

   ![Создание рабочей области машинного обучения Azure](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Введите параметры для рабочей области.

   ![Ввод параметров рабочей области машинного обучения](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Отправка данных с помощью записной книжки IPython Notebook. Импортируйте необходимые пакеты, подключаемые учетные данные, создайте базу данных в учетной записи хранения, а затем загрузите данные в кластеры HDI.

```python
# Import required packages
import pyodbc
import time as time
import json
import os
import urllib
import urllib2
import warnings
import re
import pandas as pd
import matplotlib.pyplot as plt
from azure.storage.blob import BlobService
warnings.filterwarnings("ignore", category=UserWarning, module='urllib2')


# Create the connection to Hive by using ODBC
SERVER_NAME = 'xxx.azurehdinsight.net'
DATABASE_NAME = 'nyctaxidb'
USERID = 'xxx'
PASSWORD = 'xxxx'
DB_DRIVER = 'Microsoft Hive ODBC Driver'
driver = 'DRIVER={' + DB_DRIVER + '}'
server = 'Host=' + SERVER_NAME + ';Port=443'
database = 'Schema=' + DATABASE_NAME
hiveserv = 'HiveServerType=2'
auth = 'AuthMech=6'
uid = 'UID=' + USERID
pwd = 'PWD=' + PASSWORD
CONNECTION_STRING = ';'.join(
    [driver, server, database, hiveserv, auth, uid, pwd])
connection = pyodbc.connect(CONNECTION_STRING, autocommit=True)
cursor = connection.cursor()


# Create the Hive database and tables
queryString = "create database if not exists nyctaxidb;"
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.trip
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    rate_code string,
                    store_and_fwd_flag string,
                    pickup_datetime string,
                    dropoff_datetime string,
                    passenger_count int,
                    trip_time_in_secs double,
                    trip_distance double,
                    pickup_longitude double,
                    pickup_latitude double,
                    dropoff_longitude double,
                    dropoff_latitude double)  
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)

queryString = """
                create external table if not exists nyctaxidb.fare
                (
                    medallion string,
                    hack_license string,
                    vendor_id string,
                    pickup_datetime string,
                    payment_type string,
                    fare_amount double,
                    surcharge double,
                    mta_tax double,
                    tip_amount double,
                    tolls_amount double,
                    total_amount double)
                PARTITIONED BY (month int)
                ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\\n'
                STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
            """
cursor.execute(queryString)


# Upload data from Blob storage to an HDI cluster
for i in range(1, 13):
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxitripraw2/trip_data_%d.csv' INTO TABLE nyctaxidb2.trip PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
    queryString = "LOAD DATA INPATH 'wasb:///nyctaxifareraw2/trip_fare_%d.csv' INTO TABLE nyctaxidb2.fare PARTITION (month=%d);" % (
        i, i)
    cursor.execute(queryString)
```

Кроме того, вы можете выполнить [это пошаговое руководство](../team-data-science-process/hive-walkthrough.md) , чтобы загрузить данные Нью такси в кластер HDi. Ниже перечислены основные действия.
  
* Используйте AzCopy для загрузки ZIP-файла CSV из общедоступного большого двоичного объекта в локальную папку.
* Используйте AzCopy для отправки распакованного CSV из локальной папки в кластер HDI.
* Войдите на головной узел кластера Hadoop и подготовьтесь к анализу исследовательских данных.

После загрузки данных в кластер HDI можно проверить данные в Обозреватель службы хранилища Azure. База данных nyctaxidb создана в кластере HDI.

#### <a name="data-exploration-hive-queries-in-python"></a>Исследование данных: запросы Hive в Python

Так как данные находятся в кластере Hadoop, вы можете использовать пакет pyodbc для подключения к кластерам Hadoop и запросов к базам данных с помощью Hive для изучения и проектирования компонентов. Можно просмотреть существующие таблицы, созданные на этапе предварительных требований.

```python
queryString = """
    show tables in nyctaxidb2;
    """
pd.read_sql(queryString, connection)
```

![Просмотр имеющихся таблиц](./media/vm-do-ten-things/Python_View_Existing_Tables_Hive_v3.PNG)

Давайте взглянем в таблице trip на количество записей по каждому месяцу и посмотрим данные по частоте поездок с чаевыми и без.

```python
queryString = """
    select month, count(*) from nyctaxidb.trip group by month;
    """
results = pd.read_sql(queryString,connection)

%matplotlib inline

results.columns = ['month', 'trip_count']
df = results.copy()
df.index = df['month']
df['trip_count'].plot(kind='bar')
```

![Диаграмма количества записей за каждый месяц](./media/vm-do-ten-things/Exploration_Number_Records_by_Month_v3.PNG)

```python
queryString = """
    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;
    """
results = pd.read_sql(queryString, connection)

results.columns = ['tipped', 'trip_count']
df = results.copy()
df.index = df['tipped']
df['trip_count'].plot(kind='bar')
```

![Диаграмма частотности чаевых](./media/vm-do-ten-things/Exploration_Frequency_tip_or_not_v3.PNG)

Можно также вычислить расстояние между расположением подбора и расположением отсчета, а затем сравнить его с расстоянием поездок.

```python
queryString = """
                select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
                    3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
                    *radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
                    *cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
                    /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
                    +cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*
                    pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance
                    from nyctaxidb.trip
                    where month=1
                        and pickup_longitude between -90 and -30
                        and pickup_latitude between 30 and 90
                        and dropoff_longitude between -90 and -30
                        and dropoff_latitude between 30 and 90;
            """
results = pd.read_sql(queryString, connection)
results.head(5)
```

![Верхние строки таблицы раскладки и раскрывающегося списка](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![Отображение расстояния и отбрасывания на расстояние поездок](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Теперь давайте подготавливаем downsampled (1 процент) набора данных для моделирования. Эти данные можно использовать в модуле модуля чтения Машинное обучение.

```python
queryString = """
create  table if not exists nyctaxi_downsampled_dataset_testNEW (
medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\\n'
stored as textfile;
"""
cursor.execute(queryString)
```

Теперь вставьте содержимое объединения в предыдущую внутреннюю таблицу.

```python
queryString = """
insert overwrite table nyctaxi_downsampled_dataset_testNEW
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class
from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
3959*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
radians(180)/180/2),2)-cos(pickup_latitude*radians(180)/180)
*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*radians(180)/180/2),2)
+cos(pickup_latitude*radians(180)/180)*cos(dropoff_latitude*radians(180)/180)*pow(sin((dropoff_longitude-pickup_longitude)*radians(180)/180/2),2))) as direct_distance,
rand() as sample_key

from trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
"""
cursor.execute(queryString)
```

Через некоторое время можно увидеть, что данные были загружены в кластеры Hadoop:

```python
queryString = """
    select * from nyctaxi_downsampled_dataset limit 10;
    """
cursor.execute(queryString)
pd.read_sql(queryString, connection)
```

![Верхние строки данных из таблицы](./media/vm-do-ten-things/DownSample_Data_For_Modeling_v2.PNG)

#### <a name="read-data-from-hdi-by-using-azure-machine-learning-studio-classic-reader-module"></a>Чтение данных из HDI с помощью Машинное обучение Azure Studio (классическая модель): модуль чтения

Модуль чтения можно также использовать в Машинное обучение Azure Studio (классическая модель) для доступа к базе данных в кластере Hadoop. Подключите учетные данные кластеров HDI и учетной записи хранения Azure, чтобы включить создание моделей машинного обучения с помощью базы данных в кластерах HDI.

![Свойства модуля "Читатель"](./media/vm-do-ten-things/AML_Reader_Hive.PNG)

Затем можно просмотреть оцененный набор данных:

![Просмотр оцененного набора данных](./media/vm-do-ten-things/AML_Model_Results.PNG)

### <a name="azure-sql-data-warehouse-and-databases"></a>Хранилище данных SQL Azure и базы данных
Хранилище данных SQL Azure — это хранилище эластичных данных как услуга с SQL Server корпоративного класса.

Вы можете подготавливать хранилище данных SQL Azure, следуя инструкциям в [этой статье](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md). После подготовки хранилища данных SQL вы можете использовать [это пошаговое руководство](../team-data-science-process/sqldw-walkthrough.md) для передачи, просмотра и моделирования данных с помощью данных в хранилище данных SQL.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB — это база данных NoSQL в облаке. Его можно использовать для работы с документами, такими как JSON, а также для хранения и запроса документов.

Используйте следующие шаги, чтобы получить доступ к Azure Cosmos DB из DSVM:

1. Пакет SDK для Azure Cosmos DB Python уже установлен на DSVM. Чтобы обновить его, запустите ```pip install pydocumentdb --upgrade``` из командной строки.
2. Создайте учетную запись Azure Cosmos DB и базу данных из [портал Azure](https://portal.azure.com).
3. Скачайте средство переноса данных Azure Cosmos DB из [центра загрузки Майкрософт](https://www.microsoft.com/downloads/details.aspx?FamilyID=cda7703a-2774-4c07-adcc-ad02ddc1a44d) и извлеките его в нужный каталог.
4. Импортируйте данные JSON (о вулканах Data), хранящиеся в [общедоступном большом двоичном объекте](https://cahandson.blob.core.windows.net/samples/volcano.json) , в Azure Cosmos DB со следующими параметрами команды для средства миграции. (Используйте dtui. exe из каталога, в котором установлен инструмент переноса данных Azure Cosmos DB.) Введите исходное и целевое расположение с этими параметрами:
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

После импорта данных можно перейти в Jupyter и открыть записную книжку под названием *DocumentDBSample*. Он содержит код Python для доступа к Azure Cosmos DB и выполнения некоторых базовых запросов. Дополнительные сведения о Azure Cosmos DB можно получить на [странице документации](https://docs.microsoft.com/azure/cosmos-db/)по службе.

## <a name="use-power-bi-reports-and-dashboards"></a>Использование Power BI отчетов и панелей мониторинга 
Вы можете визуализировать файл JSON О вулканах из предыдущего Azure Cosmos DB примера в Power BI Desktop, чтобы получить наглядную информацию о данных. Подробные указания см. в статье о [Power BI](../../cosmos-db/powerbi-visualize.md). Ниже приведены основные действия.

1. Откройте Power BI Desktop и выберите **Получение данных**. Укажите URL-адрес как: https://cahandson.blob.core.windows.net/samples/volcano.json.
2. Вы должны увидеть записи JSON, импортированные в виде списка. Преобразуйте список в таблицу, чтобы Power BI может работать с ним.
4. Разверните столбцы, щелкнув значок развернуть (стрелка).
5. Обратите внимание, что расположение является полем **записи** . Разверните запись и выберите только координаты. **Координата** — это столбец списка.
6. Добавьте новый столбец, чтобы преобразовать столбец координат списка в **LatLong** столбец с разделителями-запятыми. Объедините два элемента в поле списка координат, используя формулу ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```.
7. Преобразуйте столбец **повышения** в тип Decimal и выберите кнопки **Закрыть** и **Применить** .

Вместо предыдущих шагов можно вставить следующий код. Он включает в себя действия, используемые в Расширенный редактор в Power BI для записи преобразований данных на языке запросов.

```pqfl
let
    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
in
    #"Changed Type"
```

Теперь ваши данные находятся в модели данных Power BI. Экземпляр Power BI Desktop должен выглядеть следующим образом:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Вы можете приступить к созданию отчетов и визуализаций с помощью модели данных. Для создания отчета можно выполнить действия, описанные в [этой Power BI статье](../../cosmos-db/powerbi-visualize.md#build-the-reports) .

## <a name="scale-the-dsvm-dynamically"></a>Динамическое масштабирование DSVM 
Вы можете масштабировать DSVM в соответствии с потребностями проекта. Если вы не хотите использовать виртуальную машину в вечером или в выходные дни, можно завершить работу виртуальной машины из [портал Azure](https://portal.azure.com).

> [!NOTE]
> Если для операционной системы на виртуальной машине используется только кнопка завершения работы, взимается плата за использование вычислений.  
> 
> 

Может потребоваться выполнить какой-либо крупномасштабный анализ и потребовать больше ресурсов ЦП, памяти или дискового пространства. Если это так, можно выбрать размеры виртуальных машин в терминах ядер ЦП, экземпляров на основе GPU для глубокого обучения, объема памяти и типов дисков (включая твердотельные накопители), которые соответствуют потребностям вычислений и бюджета. Полный список виртуальных машин, а также их почасовые расчетные цены доступны на странице [цен на виртуальные машины Azure](https://azure.microsoft.com/pricing/details/virtual-machines/) .

Аналогичным образом производительность обработки виртуальных машин может уменьшиться. (Например: крупная Рабочая нагрузка была перемещена в кластер Hadoop или Spark.) Затем можно масштабировать кластер из [портал Azure](https://portal.azure.com) и перейти к параметрам экземпляра виртуальной машины. 

## <a name="add-more-tools"></a>Добавить дополнительные инструменты
Средства, встроенные в DSVM, могут решать многие распространенные потребности в аналитике данных. Это экономит время, так как вам не нужно устанавливать и настраивать среды по одному. Это также экономит деньги, так как вы платите только за используемые ресурсы.

Вы можете использовать другие данные и службы аналитики Azure, профилированные в этой статье, для улучшения среды аналитики. В некоторых случаях могут потребоваться дополнительные средства, в том числе некоторые специализированные средства для партнеров. У вас есть полный административный доступ к виртуальной машине для установки новых необходимых средств. Вы также можете установить дополнительные пакеты на Python и R, которые не предустановлены. Для Python можно использовать либо ```conda```, либо ```pip```. Для R можно использовать ```install.packages()``` в консоли R либо использовать интегрированную среду разработки и выбрать **пакеты** > **установки пакетов**.

## <a name="deep-learning"></a>Глубокое обучение

В дополнение к примерам на основе платформы можно получить набор подробных руководств, которые были проверены на DSVM. Эти пошаговые руководства помогут вам быстро приступить к разработке приложений для глубокого обучения в таких доменах, как изображение и текст/понимание языка.   


- [Выполнение нейронных сетей в разных платформах](https://github.com/ilkarman/DeepLearningFrameworks). в этом пошаговом руководстве показано, как перенести код из одной платформы в другую. В нем также показано, как сравнивать модели и производительность среды выполнения между различными платформами. 

- [Руководство по созданию комплексного решения по обнаружению продуктов на изображениях.](https://github.com/Azure/cortana-intelligence-product-detection-from-images) Обнаружение изображений — это метод, с помощью которого можно находить и классифицировать объекты на изображении. Эта технология может привести к огромным преимуществам во многих реальных бизнес-доменах. Например, розничные продавцы могут использовать этот метод для определения того, какой продукт клиент взял с полки. Эти сведения, в свою очередь, помогают управлять запасами продукции. 

- [Глубокое обучение для аудио](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): в этом руководстве показано, как обучить модель глубокого обучения для обнаружения событий аудио в [наборе данных городской звук](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html). В нем также приводятся общие сведения о работе с звуковыми данными.

- [Классификация текстовых документов](https://github.com/anargyri/lstm_han). в этом пошаговом руководстве показано, как создать и обучить две архитектуры нейронных сетей: сетевое внимание и сеть с длительной краткосрочной памятью (LSTM). Эти нейронные сети выполняют классификацию текстовых документов с помощью API Keras для глубокого обучения. Keras — это внешний интерфейс до трех наиболее популярных платформ для глубокого обучения: Microsoft Cognitive Toolkit, TensorFlow и Theano.

## <a name="summary"></a>Сводка
В этой статье описаны некоторые действия, которые можно выполнить на виртуальной машине Майкрософт для обработки и анализа данных. Существует множество вещей, которые можно сделать, чтобы сделать DSVM эффективной средой аналитики.

