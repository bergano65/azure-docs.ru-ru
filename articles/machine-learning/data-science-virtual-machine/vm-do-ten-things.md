---
title: Изучение данных и моделей в Windows
titleSuffix: Azure Data Science Virtual Machine
description: Выполняйте задачи по исследованию и моделированию данных на виртуальной машине Windows Data Science.
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0f42f075f5d3be4486157334403bfa7d3f1aa80c
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682872"
---
# <a name="ten-things-you-can-do-on-the-windows-data-science-virtual-machine"></a>10 задач, которые можно выполнить на виртуальной машине Windows для обработки и анализа данных

Виртуальная машина Windows Data Science (DSVM) — это мощная среда разработки науки о данных, в которой можно выполнять задачи по исследованию и моделированию данных. Среда уже построена и в комплекте с несколькими популярными инструментами анализа данных, которые упрощает работу с анализом для развертывания на месте, в облаке или гибриде. 

DSVM работает в тесном контакте с службами Azure. Он может считывать и обрабатывать данные, которые уже хранятся в Azure, в хранилище данных Azure S'L, Azure Data Lake, Azure Storage или Azure Cosmos DB. Он также может воспользоваться другими инструментами аналитики, такими как Azure Machine Learning и Azure Data Factory.

В этой статье вы узнаете, как использовать DSVM для выполнения задач по науке данных и взаимодействия с другими службами Azure. Вот некоторые задачи, которые можно выполнить в DSVM:

- Изучите данные и разработайте модели локально на DSVM с помощью Microsoft Machine Learning Server и Python.
- Используйте ноутбук Jupyter для экспериментов с данными в браузере с помощью Python 2, Python 3 и Microsoft R. (Microsoft R — это готовая к эксплуатации версия R, разработанная для производительности).
- Развертывайте модели, построенные с помощью R и Python на Azure Machine Learning, чтобы клиентские приложения могли получить доступ к вашим моделям с помощью простого интерфейса веб-службы.
- Администрирование ресурсов Azure с помощью портала Azure или PowerShell.
- Расширьте пространство для хранения данных и поделитесь крупномасштабными наборами данных/кодом всей группой, создав совместное использование файлов Azure в качестве монтажного диска на DSVM.
- Поделитесь кодом со своей командой с помощью GitHub. Получите доступ к репозиторию с помощью предустановленных клиентов Git: Git Bash и Git GUI.
- Доступ к службам данных и аналитики Azure, таким как хранилище данных Azure Blob, озеро данных Azure, Azure HDInsight (Хадуп), Azure Cosmos DB, Хранилище данных Azure S'L и база данных Azure S'L.
- Создавайте отчеты и панель мониторинга, используя предустановленную на DSVM экземпляр Power BI Desktop и развертывайте их в облаке.
- Динамическимасштабируйте DSVM, чтобы удовлетворить потребности вашего проекта.
- Установите дополнительные инструменты на виртуальную машину.   

> [!NOTE]
> Дополнительные сборы за использование применяются ко многим службам хранения данных и аналитики, перечисленным в этой статье. Для получения подробной информации смотрите страницу [ценообразования Azure.](https://azure.microsoft.com/pricing/)
> 
> 

## <a name="prerequisites"></a>Предварительные требования

* Вам понадобится подписка Azure. Вы можете [воспользоваться бесплатной пробной версией](https://azure.microsoft.com/free/).
* Инструкции по подготовке виртуальной машины Data Science на портале Azure доступны при [создании виртуальной машины.](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016)


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Изучите данные и разработайте модели с помощью microsoft Machine Learning Server
Используя такие языки, как R и Python, анализ данных можно выполнять непосредственно в DSVM.

Для R можно использовать IDE, как RStudio, который можно найти в меню «Старт» или на рабочем столе. Или вы можете использовать R Инструменты для визуальной студии. Корпорация Майкрософт предоставила дополнительные библиотеки поверх открытого исходного кода CRAN R, чтобы обеспечить масштабируемую аналитику и возможность анализировать данные больше, чем размер памяти, разрешенный в параллельном анализе. 

Для Python можно использовать такую интегрированную среду разработки, как Visual Studio Community Edition, в которой предустановлено расширение Python Tools for Visual Studio (PTVS). По умолчанию на PTVS настраивается только Python 3.6, корневая среда Conda. Для обеспечения того, чтобы Anaconda Python 2.7, сделать следующие шаги:

1. Создавайте пользовательские среды для каждой версии, перейдя в**Среды** **Python Tools** > **Tools,** > а затем выбрав **пользовательские** в Visual Studio Community Edition.
1. Дайте описание и установите путь префикссреды среды как **c: 'anaconda'envs-python2** для Anaconda Python 2.7.
1. Выберите **автоматическое обнаружение** > **применяются** для сохранения окружающей среды.

Более подробную информацию о том, как создавать среды Python, можно узнать в [документации PTVS.](https://aka.ms/ptvsdocs)

Теперь вы настроены для создания нового проекта Python. Перейдите в **File** > **New** > **Project** > **Python** и выберите тип создаваемого приложения Python. Среду Python можно настроить для текущего проекта на нужную версию (Python 2.7 или 3.6), нажав на **среду Python,** а затем выбрав **среды Добавить/Удалить Python.** Более подробную информацию о работе с PTVS вы можете найти в [документации по продукту.](https://aka.ms/ptvsdocs)

## <a name="use-jupyter-notebooks"></a>Использование записных книжек Jupyter
Ноутбук Jupyter предоставляет браузерный IDE для исследования и моделирования данных. Вы можете использовать Python 2, Python 3 или R (как с открытым исходным кодом, так и Microsoft R Server) в блокноте Jupyter.

Чтобы запустить записную книжку Jupyter, выберите значок **Jupyter Notebook** в меню **«Пуск»** или на рабочем столе. В запросе команды DSVM вы также ```jupyter notebook``` можете запустить команду из каталога, где у вас есть существующие ноутбуки или где вы хотите создать новые ноутбуки.  

После запуска Jupyter вы должны увидеть каталог, содержащий несколько примеров тетрадей, которые предварительно упакованы в DSVM. Теперь вы можете:

* Выберите блокнот, чтобы увидеть код.
* Выполнить каждую ячейку, выбрав Shift-Enter.
* Выполнить весь ноутбук, выбрав **Cell** > **Run.**
* Создайте новый блокнот, выбрав значок Jupyter (вверху в левом углу), выбрав **новую** кнопку справа, а затем выбрав язык ноутбука (также известный как ядра).   

> [!NOTE]
> В настоящее время поддерживаются ядра Python 2.7, Python 3.6, R, Julia и PySpark в Jupyter. Ядро R поддерживает программирование как в с открытым исходным кодом R, так и в Microsoft R.   
> 
> 

Когда вы находитесь в блокноте, вы можете изучить ваши данные, построить модель и протестировать модель, используя ваш выбор библиотек.

## <a name="train-and-deploy-models-by-using-azure-machine-learning"></a>Обучение и развертывание моделей с помощью машинного обучения Azure
После того как вы создали и проверили модель, следующим шагом, как правило, является развертывание ее в рабочей. Этот шаг позволяет вашим клиентским приложениям вызывать прогнозы модели в режиме реального времени или на основе пакетного режима. Службе машинного обучения Azure доступен механизм ввода в эксплуатацию модели, созданной на языке R или Python.

При эксплуатации модели в Azure Machine Learning подвергается веб-службе. Это позволяет клиентам совершать вызовы REST, которые проходят в входных параметрах и получать прогнозы от модели в качестве выходов.

### <a name="build-and-operationalize-python-models"></a>Создание и эксплуатация моделей Python
Вот фрагмент кода, разработанный в блокноте Python Jupyter, который создает простую модель с помощью библиотеки Scikit-обучения:

```python
# IRIS classification
from sklearn import datasets
from sklearn import svm
clf = svm.SVC()
iris = datasets.load_iris()
X, y = iris.data, iris.target
clf.fit(X, y)
```

Метод, используемый для развертывания моделей Python в Azure Machine Learning, обертывает предсказание модели в функцию и украшает ее атрибутами, предоставляемыми предварительно установленной библиотекой Azure Machine Learning Python. Атрибуты обозначают идентификатор рабочего пространства Azure Machine Learning, ключ API, а также параметры ввода и возврата.  

```python
from azureml import services
@services.publish(workspaceid, auth_token)
@services.types(sep_l=float, sep_w=float, pet_l=float, pet_w=float)
@services.returns(int)  # 0, or 1, or 2
def predictIris(sep_l, sep_w, pet_l, pet_w):
    inputArray = [sep_l, sep_w, pet_l, pet_w]


return clf.predict(inputArray)
```

Теперь клиент может выполнять вызовы веб-службы. Удобные обертки построить запросы REST API. Вот пример кода для потребления веб-службы:

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
> В настоящее время библиотека машинного обучения Azure поддерживается только на Python 2.7.   
> 
> 

### <a name="build-and-operationalize-r-models"></a>Создание и эксплуатация моделей R
Вы можете развернуть R-модели, построенные на виртуальной машине Data Science или в другом месте, на Azure Machine Learning таким образом, чтобы это было похоже на то, как это делается для Python. Ниже приводятся шаги:

1. Создайте файл settings.json для предоставления идентификатора рабочего пространства и токена проверки подлинности. 
2. Напишите обертку для функции прогнозирования модели.
3. Позвоните ```publishWebService``` в библиотеку машинного обучения Azure, чтобы пройти в обертку функции.  

Используйте следующие фрагменты процедури и кода для настройки, создания, публикации и использования модели в качестве веб-службы в Azure Machine Learning.

#### <a name="set-up"></a>Настройка

Создайте файл settings.json под ```.azureml``` каталогом, который называется под домашним каталогом. Введите параметры из рабочего пространства Azure Machine Learning.

Вот структура файла settings.json:

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
Чтобы использовать модель из клиентского приложения, воспользуйтесь библиотекой машинного обучения Azure, чтобы найти опубликованный веб-сервис по имени. Используйте `services` вызов API для определения конечных точек. Затем нужно просто вызвать функцию `consume` и передать блок данных для прогноза.

Используйте следующий код для использования модели, опубликованной в виде веб-сервиса Azure Machine Learning:

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

Более подробную информацию о пакетах R можно увидеть [в студии машинного обучения.](/azure/machine-learning/studio-module-reference/r-packages-supported-by-azure-machine-learning)

## <a name="manage-azure-resources"></a>Управление ресурсами Azure
DSVM не только позволяет создавать аналитические решения локально на виртуальной машине. Он также позволяет получить доступ к службам на облачной платформе Azure. Azure предоставляет несколько вычислений, хранения, анализа данных и других служб, которые можно администрировать и получить доступ с вашего DSVM.

Для администрирования подписки Azure и облачных ресурсов у вас есть два варианта:
+ Воспользуйтесь браузером и перейдите на [портал Azure](https://portal.azure.com).

+ Используйте скрипты PowerShell. Выполнить Azure PowerShell из ярлыка на рабочем столе или из меню **«Пуск».** Для получения подробной информации можно ознакомиться с [документацией Microsoft Azure PowerShell.](../../powershell-azure-resource-manager.md) 

## <a name="extend-storage-by-using-shared-file-systems"></a>Расширяйте хранение с помощью общих файловых систем
Специалисты по обработке и анализу данных могут использовать большие наборы данных, код или другие ресурсы вместе с участниками группы. DSVM имеет около 45 ГБ пространства. Чтобы расширить хранилище, можно использовать файлы Azure и либо установить его на одном или нескольких экземплярах DSVM, либо получить к нему доступ через REST API. Вы также можете использовать [портал Azure](../../virtual-machines/windows/attach-managed-disk-portal.md) или использовать [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) для добавления дополнительных выделенных дисков данных. 

> [!NOTE]
> Максимальное пространство на доле Файлов Azure составляет 5 ТБ. Предельный размер для каждого файла составляет 1 ТБ. 

Этот скрипт можно использовать в Azure PowerShell для создания общего использования файлов Azure:

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

Теперь, когда вы создали общий вариант Azure Files, вы можете смонтировать его в любой виртуальной машине Azure. Мы рекомендуем поместить VM в тот же центр обработки данных Azure, что и учетную запись хранения, чтобы избежать задержки и платы за передачу данных. Вот команды Azure PowerShell для установки диска на DSVM:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

Теперь к этому диску можно обращаться как к обычному диску на виртуальной машине.

## <a name="share-code-in-github"></a>Общий код в GitHub
GitHub — это репозиторий кода, где можно найти образцы кода и источники для различных инструментов с помощью технологий, которыми делится сообщество разработчиков. Git выступает в качестве технологии для отслеживания и хранения версий файлов кода. GitHub также является платформой, где вы можете создать свой собственный репозиторий для хранения общего кода и документации вашей команды, реализации управления версиями и управления, который имеет доступ к просмотру и вносить код. 

Дополнительные сведения об использовании Git см. на [страницах справки GitHub](https://help.github.com/). Вы можете использовать GitHub как один из способов совместной работы с командой, использовать код, разработанный сообществом, и вносить код обратно в сообщество.

DSVM поставляется с клиентскими инструментами на командной строке и на GUI для доступа к репозиторию GitHub. Инструмент командной строки, работающий с Git и GitHub, называется Git Bash. Visual Studio установлена на DSVM и имеет расширения Git. Иконки для этих инструментов можно найти в меню **«Пуск»** и на рабочем столе.

Для скачивания кода из репозитория GitHub используйте команду ```git clone```. Например, чтобы загрузить репозиторий науки о данных, опубликованный корпорацией Майкрософт, в текущий каталог можно запустить следующую команду в Git Bash:

    git clone https://github.com/Azure/DataScienceVM.git

В Visual Studio можно выполнить ту же операцию клонирования. На следующем скриншоте показано, как получить доступ к инструментам Git и GitHub в Visual Studio:

![Снимок экрана Visual Studio с отображением соединения с GitHub](./media/vm-do-ten-things/VSGit.PNG)

Более подробную информацию об использовании Git можно найти в репозитории GitHub из ресурсов, доступных на github.com. Вы также найдете полезную информацию в этой [памятке](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) .

## <a name="access-azure-data-and-analytics-services"></a>Доступ к службам данных и аналитики Azure
### <a name="azure-blob-storage"></a>Хранилище BLOB-объектов Azure
Хранение Azure Blob — это надежный и экономичный облачный сервис хранения данных больших и малых. В этом разделе описывается, как можно перемещать данные в хранилище Blob и доступ к данным, хранящимся в blob Azure.

#### <a name="prerequisites"></a>Предварительные требования

* Создайте учетную запись хранения Azure Blob на [портале Azure.](https://portal.azure.com)

   ![Скриншот процесса создания учетной записи хранилища на портале Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Подтвердите, что инструмент командной строки AzCopy предварительно установлен: ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe```. Каталог, содержащий azcopy.exe, уже находится в переменной среды PATH, так что вы можете избежать ввода полного пути команды при запуске этого инструмента. Для получения дополнительной информации об [AzCopy documentation](../../storage/common/storage-use-azcopy.md)инструменте AzCopy см.
* Запустите инструмент Azure Storage Explorer. Вы можете скачать его с [веб-страницы Storage Explorer](https://storageexplorer.com/). 

   ![Скриншот доступа исследователя хранения Azure к учетной записи хранилища](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Перемещение данных с VM в blob Azure: AzCopy

Для перемещения данных между локальными файлами и хранилищем Blob можно использовать AzCopy на командной строке или в PowerShell:

    AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt

Замените **C: 'myfolder** с пути, по которому хранится ваш файл, **mystorageaccount** с вашим именем учетной записи хранилища Blob, **mycontainer** с именем контейнера и **ключом учетной записи хранилища** с ключом доступа к хранению Blob. Учетные данные учетной записи хранилища можно найти на [портале Azure.](https://portal.azure.com)

Выполнить команду AzCopy в PowerShell или из запроса команды. Вот несколько примеров использования команды AzCopy:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

После запуска команды AzCopy для копирования в blob Azure ваш файл будет отображаться в Azure Storage Explorer.

![Скриншот учетной записи хранилища, отображающий загруженный файл CSV](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Перемещение данных с VM в blob Azure: Azure Storage Explorer

Вы также можете загрузить данные из локального файла в VM с помощью Azure Storage Explorer:

* Чтобы загрузить данные в контейнер, выберите целевой контейнер и выберите кнопку **Загрузка.** ![Скриншот кнопки загрузки в Azure Storage Explorer](./media/vm-do-ten-things/storage-accounts.png)
* Выберите эллипсис **(...)** справа от окна **файлов,** выберите один или несколько файлов для загрузки из файловой системы и выберите **Upload,** чтобы начать загрузку файлов. ![Скриншот диалогового окна файлов Upload](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-machine-learning-reader-module"></a>Читайте данные с капли Azure: модуль чтения машинного обучения

В студии машинного обучения Azure можно использовать модуль импортных данных для чтения данных с капли.

![Снимок экрана модуля "Импорт данных" в студии машинного обучения](./media/vm-do-ten-things/AML_ReaderBlob_Module_v3.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Читайте данные из капли Azure: Python ODBC

Вы можете использовать библиотеку BlobService для чтения данных непосредственно из капли в блокноте Jupyter или в программе Python.

Во-первых, импортируйте необходимые пакеты:

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

Затем подключите учетные данные учетной записи хранилища Blob и считайте данные из капли:

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

Данные считываемыеся как рамка данных:

![Снимок экрана первых 10 строк данных](./media/vm-do-ten-things/IPNB_data_readin.PNG)

### <a name="azure-data-lake"></a>Azure Data Lake
Azure Data Lake Storage — это гипермасштабное хранилище для рабочих нагрузок аналитики больших данных и совместимо с системой распределенных файлов Hadoop (HDFS). Оно работает с Hadoop, Spark и Azure Data Lake Analytics. В этом разделе вы узнаете, как можно перемещать данные в хранилище озер Лазурных данных и запускать аналитику с помощью аналитики Azure Data Lake.

#### <a name="prerequisites"></a>Предварительные требования

* Создайте экземпляр анализа озер данных Azure на [портале Azure.](https://portal.azure.com)

   ![Скриншот создания экземпляра анализа озера данных с портала Azure](./media/vm-do-ten-things/Azure_Data_Lake_Create_v2.png)

* [Плагин Azure Data Lake и Stream Analytics Tools for Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504) уже установлен в Visual Studio Community Edition на виртуальной машине. После запуска Visual Studio и вххажий в подписку Azure следует увидеть учетную запись и хранение данных Azure Data Analytics в левой панели Visual Studio.

   ![Скриншот плагина для инструментов Data Lake в Visual Studio](./media/vm-do-ten-things/Azure_Data_Lake_PlugIn_v2.PNG)

#### <a name="move-data-from-a-vm-to-data-lake-azure-data-lake-explorer"></a>Перемещение данных из VM в озеро данных: Azure Data Lake Explorer

Для [загрузки данных из локальных файлов в виртуальной машине](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal)в Хранилище data Lake Можно использовать Azure Data Lake Explorer.

Можно также построить конвейер данных для оперативной работы движения данных в озеро данных Azure или из нее с помощью [Azure Data Factory.](https://azure.microsoft.com/services/data-factory/) [Эта статья](https://azure.microsoft.com/blog/creating-big-data-pipelines-using-azure-data-lake-and-azure-data-factory/) проведет вас через шаги по созданию конвейеров данных.

#### <a name="read-data-from-an-azure-blob-to-data-lake-u-sql"></a>Прочитайте данные с капли Azure на озеро данных: U-S'L

Если данные находятся в хранилище Azure Blob, вы можете непосредственно читать данные из blob Azure в запросе U-S'L. Перед составлением запроса U-S'L убедитесь, что ваша учетная запись хранения Blob связана с экземпляром Azure Data Lake. Перейдите на портал Azure, найдите панель мониторинга Azure Data Lake Analytics, выберите **«Добавить источник данных»,** выберите тип **хранения хранилища Azure Storage**и подключите имя и ключ учетной записи и ключа хранилища Azure. Затем можно ссылаться на данные, хранящиеся в учетной записи хранилища.

![Скриншот диалогового окна Добавить источник данных](./media/vm-do-ten-things/Link_Blob_to_ADLA_v2.PNG)

В Visual Studio вы можете читать данные из хранилища Blob, манипулировать данными, инженерными функциями и отправлять полученные данные в хранилище Azure Data Или Azure Blob. При соотношении данных в хранилище Blob используйте **wasb://**. При соотношении данных в Azure Data Lake используйте **swbhdfs://**.

Вы можете использовать следующие запросы U-S'L в Visual Studio:

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

После отправки запроса на сервер диаграмма показывает состояние вашей работы.

![Скриншот диаграммы состояния задания](./media/vm-do-ten-things/USQL_Job_Status.PNG)

#### <a name="query-data-in-data-lake-u-sql"></a>Запрос данных в озере данных: U-SQL

После того, как набор данных попадает в Azure Data Lake, можно использовать [язык U-S'L](../../data-lake-analytics/data-lake-analytics-u-sql-get-started.md) для запроса и изучения данных. Язык U-S'L похож на язык T-S'L, но сочетает в себе некоторые функции от C, так что пользователи могут писать индивидуальные модули и пользовательские функции. Можно использовать сценарии из предыдущего шага.

После отправки запроса на сервер tripdata_summary. CSV отображается в Azure Data Lake Explorer. Вы можете просмотреть данные, нажав правой кнопкой мыши файла.

![Скриншот файла CSV в Data Lake Explorer](./media/vm-do-ten-things/USQL_create_summary.png)

Отображается информация о файле:

![Снимок экрана сводных данных файла](./media/vm-do-ten-things/USQL_tripdata_summary.png)

### <a name="hdinsight-hadoop-clusters"></a>Кластеры HDInsight Hadoop
Azure HDInsight — это управляемый сервис Apache Hadoop, Spark, HBase и Storm в облаке. Вы можете легко работать с кластерами Azure HDInsight из виртуальной машины Data Science.

#### <a name="prerequisites"></a>Предварительные требования

* Создайте учетную запись хранения Azure Blob на [портале Azure.](https://portal.azure.com) Она используется для хранения данных кластеров HDInsight.

   ![Скриншот создания учетной записи хранилища с портала Azure](./media/vm-do-ten-things/Create_Azure_Blob.PNG)

* Настройте кластеры Azure HDInsight Hadoop с [портала Azure.](../team-data-science-process/customize-hadoop-cluster.md)
  
   Свяжите учетную запись хранилища, созданную с кластером HDInsight, когда она будет создана. Эта учетная запись хранения используется для доступа к данным, которые можно обработать в пределах кластера.

   ![Выбор ы на соссылку учетной записи хранилища, созданной кластером HDInsight](./media/vm-do-ten-things/Create_HDI_v4.PNG)

* Включить удаленный доступ к головному узлам кластера после его создания. Помните учетные данные удаленного доступа, указанные здесь, потому что они понадобятся вам в последующей процедуре.

   ![Кнопка удаленного рабочего стола для обеспечения удаленного доступа к кластеру HDInsight](./media/vm-do-ten-things/Create_HDI_dashboard_v3.PNG)

* Создайте рабочую область машинного обучения Azure. Ваши эксперименты по машинном обучению хранятся в этом рабочем пространстве машинного обучения. Выберите выделенные параметры на портале, как показано на следующем скриншоте:

   ![Создание рабочей области машинного обучения Azure](./media/vm-do-ten-things/Create_ML_Space.PNG)

* Введите параметры рабочего пространства.

   ![Ввод параметров рабочей области машинного обучения](./media/vm-do-ten-things/Create_ML_Space_step2_v2.PNG)

* Загружайте данные с помощью ноутбука IPython. Импорт необходимые пакеты, подключите учетные данные, создайте базу данных в учетной записи хранилища, а затем загружайте данные в кластеры ИПДУ.

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

Кроме того, вы можете следовать [этому пошаговому шагу,](../team-data-science-process/hive-walkthrough.md) чтобы загрузить данные NYC Taxi в кластер ИРЧП. Ниже перечислены основные действия.
  
* Используйте AzCopy для загрузки застегнутых csVs с общедоступной капли в местную папку.
* Используйте AzCopy для загрузки незастегнутых CSV из локальной папки в кластер ИПЧП.
* Войти в головной узлы кластера Hadoop и подготовиться к исследовательскому анализу данных.

После загрузки данных в кластер ИРЧП можно проверить данные в Azure Storage Explorer. В кластере ИРЧП создана база данных nyctaxidb.

#### <a name="data-exploration-hive-queries-in-python"></a>Просмотр данных: запросы Hive в Python

Поскольку данные в кластере Hadoop, вы можете использовать пакет pyodbc для подключения к кластерам Hadoop и базам данных запросов, используя Hive для исследования и проектирования функций. Можно просмотреть существующие таблицы, созданные на этапе предварительного предварительного просмотра.

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

Вы также можете вычислить расстояние между местоположением пикапа и местом высадки, а затем сравнить его с расстоянием поездки.

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

![Верхние ряды стола для пикапа и высадки](./media/vm-do-ten-things/Exploration_compute_pickup_dropoff_distance_v2.PNG)

```python
results.columns = ['pickup_longitude', 'pickup_latitude', 'dropoff_longitude',
                   'dropoff_latitude', 'trip_distance', 'trip_time_in_secs', 'direct_distance']
df = results.loc[results['trip_distance'] <= 100]  # remove outliers
df = df.loc[df['direct_distance'] <= 100]  # remove outliers
plt.scatter(df['direct_distance'], df['trip_distance'])
```

![Участок пикап / высадка расстояние до расстояния поездки](./media/vm-do-ten-things/Exploration_direct_distance_trip_distance_v2.PNG)

Теперь давайте подготовим набор данных для моделирования. Эти данные можно использовать в модуле чтения машинного обучения.

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

Теперь вставьте содержимое соединения в предыдущую внутреннюю таблицу.

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

### <a name="azure-sql-data-warehouse-and-databases"></a>Хранилище данных и базы данных Azure S'L
Хранилище данных Azure S'L — это упругий склад данных как сервис с опытом работы с сервером Корпоративного класса.

Вы можете предоставить свой хранилище данных Azure S'L, следуя инструкциям, приведенным в [этой статье.](../../sql-data-warehouse/sql-data-warehouse-get-started-provision.md) После предоставления хранилища данных S'L можно использовать [это пошаг-закладку](../team-data-science-process/sqldw-walkthrough.md) для загрузки, исследования и моделирования данных с помощью данных в хранилище данных S'L.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB — это база данных NoSQL в облаке. Вы можете использовать его для работы с такими документами, как JSON, а также для хранения и запроса документов.

Используйте следующие предварительные шаги для доступа к DB Azure Cosmos от DSVM:

1. Azure Cosmos DB Python SDK уже установлен на DSVM. Чтобы обновить его, выполнить ```pip install pydocumentdb --upgrade``` от запроса команды.
2. Создайте учетную запись Azure Cosmos DB и базу данных с [портала Azure.](https://portal.azure.com)
3. Загрузите инструмент миграции данных Azure Cosmos DB из [Центра загрузки Майкрософт](https://www.microsoft.com/download/details.aspx?id=53595) и извлеките в каталог по вашему выбору.
4. Импортные данные JSON (данные вулкана), хранящиеся в [общедоступной капли](https://dotnet.microsoft.com/) в Azure Cosmos DB со следующими параметрами команды для инструмента миграции. (Использовать dtui.exe из каталога, где вы установили инструмент миграции данных Azure Cosmos DB.) Введите исходное и целевое местоположение с помощью следующих параметров:
   
    `/s:JsonFile /s.Files:https://cahandson.blob.core.windows.net/samples/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

После импорта данных, вы можете перейти к Jupyter и открыть блокнот под названием *DocumentDBSample*. Он содержит код Python для доступа к Azure Cosmos DB и сделать некоторые основные запросы. Вы можете узнать больше о Azure Cosmos DB, посетив [страницу документации](https://docs.microsoft.com/azure/cosmos-db/)службы.

## <a name="use-power-bi-reports-and-dashboards"></a>Используйте отчеты Power BI и панели мониторинга 
Можно визуализировать файл Volcano JSON из предыдущего примера Azure Cosmos DB на power BI Desktop, чтобы получить визуальное представление о данных. Подробные указания см. в статье о [Power BI](../../cosmos-db/powerbi-visualize.md). Ниже приведены основные действия.

1. Откройте Power BI Desktop и выберите **Получение данных**. Укажите URL `https://cahandson.blob.core.windows.net/samples/volcano.json`как: .
2. Вы должны видеть записи JSON, импортируемые в виде списка. Преобразуйте список в таблицу, чтобы Power BI мог работать с ним.
4. Расширьте столбцы, выбрав значок расширения (стрелка).
5. Обратите внимание, что местоположение является полем **записи.** Разверните запись и выберите только координаты. **Координация** — это столбец списка.
6. Добавьте новую колонку для преобразования столбца координат списка в столбец **LatLong,** разделенный запятой. Конквалифицировать два элемента в поле ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})```списка координат с помощью формулы.
7. Преобразуйте столбец **Высота** в десятичную и выберите кнопки **«Закрыть** и **применить».**

Вместо предыдущих шагов можно вставить следующий код. Он спорит с шагами, используемыми в Advanced Editor в Power BI для записи преобразований данных на языке запросов.

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

Теперь ваши данные находятся в модели данных Power BI. Ваш экземпляр Power BI Desktop должен отображаться следующим образом:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

Вы можете начать создание отчетов и визуализаций с помощью модели данных. Вы можете выполнить шаги в [этой статье Power BI,](../../cosmos-db/powerbi-visualize.md#build-the-reports) чтобы создать отчет.

## <a name="scale-the-dsvm-dynamically"></a>Масштабируйте DSVM динамически 
Вы можете масштабировать DSVM вверх и вниз, чтобы удовлетворить потребности вашего проекта. Если вам не нужно использовать VM вечером или в выходные дни, вы можете отключить VM с [портала Azure.](https://portal.azure.com)

> [!NOTE]
> Вы берете плату за вычисления, если используете только кнопку выключения для операционной системы на VM.  
> 
> 

Возможно, потребуется обработка крупномасштабного анализа и требуется больше емкости процессора, памяти или диска. Если это так, вы можете найти выбор размеров VM с точки зрения ядер процессора, GPU на основе экземпляров для глубокого обучения, памяти и типов дисков (в том числе твердотельных дисков), которые отвечают вашим вычислительным и бюджетным потребностям. Полный список виртуальных технологий, а также их почасовые расчетные цены доступны на странице [ценообразования Azure Virtual Machines.](https://azure.microsoft.com/pricing/details/virtual-machines/)

Аналогичным образом, потребность в емкости обработки VM может уменьшиться. (Например: вы перенесли основную рабочую нагрузку в кластер Hadoop или Spark.) Затем можно масштабировать кластер с [портала Azure](https://portal.azure.com) и перейти к настройкам экземпляра VM. 

## <a name="add-more-tools"></a>Добавить больше инструментов
Инструменты, предварительно построенные в DSVM, могут удовлетворить многие общие потребности в анализе данных. Это экономит время, потому что вам не нужно устанавливать и настраивать среду по одному. Это также экономит ваши деньги, потому что вы платите только за ресурсы, которые вы используете.

Вы можете использовать другие службы данных и аналитики Azure, профилированные в этой статье, для улучшения среды аналитики. В некоторых случаях могут потребоваться дополнительные инструменты, в том числе некоторые собственные инструменты-партнеры. У вас есть полный административный доступ на виртуальную машину для установки новых инструментов, которые вам нужны. Вы также можете установить дополнительные пакеты на Python и R, которые не предустановлены. Для Python, вы ```conda``` можете ```pip```использовать либо или . Для R можно ```install.packages()``` использовать в r-консоли или использовать IDE и выбрать **пакеты** > **Установить пакеты.**

## <a name="deep-learning"></a>Глубокое обучение

В дополнение к образцов, основанных на фреймворках, вы можете получить набор комплексных пошагов, которые были проверены на DSVM. Эти пошаговые действия помогут вам начать разработку приложений для глубокого обучения в таких областях, как изображение и понимание текста/языка.   


- [Запуск нейронных сетей в различных средах](https://github.com/ilkarman/DeepLearningFrameworks): Это пошаговое пошаговое походе показывает, как перенести код из одной инфраструктуры в другую. В нем также показано, как сравнивать модели и производительность выполнения в рамках. 

- [Руководство по созданию комплексного решения по обнаружению продуктов на изображениях.](https://github.com/Azure/cortana-intelligence-product-detection-from-images) Обнаружение изображений — это метод, с помощью которого можно находить и классифицировать объекты на изображении. Эта технология имеет потенциал, чтобы принести огромные награды во многих реальных бизнес-областях. Например, розничные продавцы могут использовать этот метод для определения того, какой продукт клиент взял с полки. Эти сведения, в свою очередь, помогают управлять запасами продукции. 

- [Глубокое обучение для аудио](https://blogs.technet.microsoft.com/machinelearning/2018/01/30/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure/): Этот учебник показывает, как обучать глубокому обучению модели для обнаружения аудио событий на [городских звуков dataset](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html). В нем также содержится обзор того, как работать с аудиоданными.

- [Классификация текстовых документов](https://github.com/anargyri/lstm_han): Это пошаговое пошаговое сообщение демонстрирует, как построить и обучить две нейронные сети архитектуры: Иерархическая сеть внимания и долгосрочную краткосрочную память (LSTM) сети. Эти нейронные сети выполняют классификацию текстовых документов с помощью API Keras для глубокого обучения. Keras является передним концом трех самых популярных платформ глубокого обучения: Microsoft Cognitive Toolkit, TensorFlow и Theano.

## <a name="summary"></a>Сводка
В этой статье описаны некоторые из вещей, которые вы можете сделать на Microsoft Data Science Virtual Machine. Есть еще много вещей, которые вы можете сделать, чтобы сделать DSVM эффективной среде аналитики.

