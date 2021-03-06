---
title: Импорт и экспорт данных с помощью проектов с предварительной версией Azure Notebook
description: Узнайте, как перенести данные в проект предварительной версии записных книжек Azure из внешних источников и как экспортировать данные из проекта.
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: e1d4a52ab7f4ad2ca3438af4bc87bec0b79f34d1
ms.sourcegitcommit: f788bc6bc524516f186386376ca6651ce80f334d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/03/2020
ms.locfileid: "75646982"
---
# <a name="work-with-data-files-in-azure-notebooks-preview-projects"></a>Работа с файлами данных в проектах предварительной версии Azure Notebook

Данные – источник множества записных книжек Jupyter, особенно записных книжек, используемый для обработки и анализа данных. С помощью Записных книжек Azure можно легко совершать импорт из разных источников в проект, а затем использовать эти данные из записных книжек. Также в записных книжках можно создавать данные, что хранятся в проекте, который затем можно загрузить для использования в другом месте.

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Меню **Данные** в пределах запущенной записной книжки также предоставляют команды **Отправить** и **Загрузить**, которые работают с файлами в проекте, также как и временные файлы для текущего сеанса записной книжки.

Можно также использовать код в записной книжке для прямого доступа к различным источникам данных, включая файлы в проекте. С помощью команд в ячейке кода можно получить доступ к произвольным данным. Поскольку такие данные хранятся в переменных в сеансе записной книжки, если вы не используете код для специальной генерации файлов проекта, они не будут сохранены в проекте.

Работать с кодом в данных лучше всего в запущенной записной книжке: с этой целью см. в разделе о получении данных в примере записных книжек в службе "Записные книжки Azure" [здесь](https://notebooks.azure.com/Microsoft/projects/samples/html/Getting%20to%20your%20Data%20in%20Azure%20Notebooks.ipynb).

Далее в этой статье содержатся сведения об операциях с файлами на уровне проекта.

## <a name="import-data"></a>Импорт данных

С помощью меню **Данные** или команды, например `curl`, из панели мониторинга проекта или в пределах запущенной записной книжки можно переместить файлы в проект.

### <a name="import-files-from-the-project-dashboard"></a>Импорт файлов из панели мониторинга проекта

1. В проекте перейдите к папке, с которой хотите импортировать файлы.

1. Выберите команду **Отправить**, а затем либо **С URL-адреса** или **С компьютера** и спроектируйте необходимую информацию для импорта данных.

   - **URL-адрес**: введите адрес источника в поле **URL-адрес файла** и имя файла для назначения записной книжке в проекте в поле **имя файла** . Затем выберите **+ Добавить файл**, чтобы добавить в список передачи URL-адрес. Повторите эту процедуру для всех других URL-адресов, а затем выберите **Готово**.

     ![Всплывающее окно отправки из URL-адреса](media/quickstarts/upload-from-url-popup.png)

   - **С компьютера**: перетащите файлы во всплывающее окно или выберите **выбрать файлы**, перейдите к и выберите файлы данных, которые необходимо импортировать. Можно удалять или выбирать любое количество файлов любого типа и формата, поскольку именно код в записной книжке открывает файл и анализирует его данные.

     ![Всплывающее окно отправки с компьютера](media/quickstarts/upload-from-computer-popup.png)

1. После импорта, файлы отображаются на панели мониторинга проекта и с помощью относительных путей для папки с файлом можно получить доступ к ним в рамке кода записной книжки.

### <a name="import-files-from-the-file-menu-in-a-notebook"></a>Импорт файлов из меню "Файл" в записной книжке

1. В пределах запущенной записной книжки, выберите команды **Файл** > **Отправить**.

    ![Команда меню "Отправка файла" в записной книжке](media/file-menu-upload.png)

1. В открывшемся диалоговом окне перейдите и выберите файлы, необходимые для отправки. Можно выбрать любое количество файлов любого типа. По окончании нажмите кнопку **Открыть**.

1. В появившемся всплывающем окне **Состояние отправки** из раскрывающегося списка выберите **Папка назначения**.

    - Папка сеанса ( *~/* ): отправляет файлы в текущий сеанс записной книжки, но не создает файлы в проекте. Папка сеанса является одноранговой в папке проекта, но не сохраняется после завершения сеанса. Чтобы получить доступ к файлам сеанса в код, добавьте префикс имени файла с относительным путем *../* .

        Папки сеанса можно использовать для экспериментирования и они позволяют избежать загромождения проекта файлами, которые могут понадобиться на долгое время или нет. Файлы, которые имеют одинаковые имена в проекте, также можно отправить в папку сеанса не вызывая конфликтов и без необходимости переименовать файлы. Например, в проекте у вас уже есть одна версия *data.csv*, но вы хотите поэкспериментировать с другой версии *data.csv*. Отправляя файл в папку сеанса, можно запустить записную книжку, используя данные в загруженном файле (обращаясь к нему в коде используя *../data.csv*) в отличии от данных в файле проекта.

    - Папка проекта ( */project*): передает файлы в проект, где с помощью относительных путей в коде к ним можно получить доступ. Отправка файла в эту папку тоже самое, что и отправка файла в панель мониторинга проекта. Файл сохраняется с проектом и доступен в последующих сеансах.

        При попытке отправить файл с тем же именем, что уже существует в проекте, отправка завершается сбоем. Чтобы перезаписать файл, отправьте новый файл из панели мониторинга проекта, что позволит перезаписать его.

1. Чтобы завершить процесс, выберите **Начать отправку**.

### <a name="create-or-import-files-using-commands"></a>Создание или импорт файлов с помощью команд

Для создания файлов в папках проекта и сеанса, используйте команды в терминале или в ячейке кода Python. Например, команды, среди которых `curl` и `wget` загружают файлы напрямую из Интернета.

Для загрузки файлов в окне терминала, выберите команду **Terminal** на панели мониторинга проекта, а затем введите подходящие команды.

```bash
curl https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv

wget https://raw.githubusercontent.com/petroleum101/figures/db46e7f48b8aab67a0dfe31696f6071fb7a84f1e/oil_price/oil_price.csv -o oil_price.csv
```

При использовании ячейки кода Python в записной книжке, добавьте командам префикс `!`.

Папка проекта является папкой по умолчанию, поэтому указывая имя конечного файла как *oil_price.csv* в проекте создается файл. Чтобы создать файл сеанса, добавьте префикс *../* к имени как в *../oil_price.csv*.

### <a name="create-files-in-code"></a>Создание файлов в коде

При использовании кода, который создает файл, например, функция `write_csv` pandas, имена пути всегда указываются относительно папки проекта. Благодаря использованию *../* создается файл сеанса, который отменяется при остановке и закрытии записной книжки.

## <a name="export-files"></a>Файлы экспорта

Можно экспортировать данные из панели мониторинга проекта либо из записной книжки.

## <a name="export-files-from-the-project-dashboard"></a>Экспорт файлов из панели мониторинга проекта

На панели мониторинга проекта щелкните правой кнопкой мыши файл и выберите **Загрузить**.

![Команда загрузки в контекстном меню элемента проекта](media/download-command.png)

Можно также выбрать файл и использовать команду **Загрузить** (сочетание клавиш: d) на панели мониторинга.

![Команда панели инструментов "Загрузить" на панели мониторинга проекта](media/download-command-toolbar.png)

## <a name="export-files-from-the-data-menu-in-a-notebook"></a>Экспорт файлов из меню "Данные" в записной книжке

1. Выберите команду меню **Файл** > **Загрузить**.

    ![Команда меню "Отправка данных" в записной книжке](media/file-menu-download.png)

1. Появится всплывающее окно, на котором показаны папки в сеансе; папка *project* содержит файлы проекта.

    ![Команда загрузки данных на всплывающем окне, где можно выбрать файлы и папки](media/file-menu-download-popup.png)

1. Установите флажки слева от файлов и папок, которые необходимо загрузить, а затем выберите **Загрузить выбранные**.

1. Записная книжка подготавливает один *ZIP-файл*, содержащий выбранные файлы, которые вы сохраните как обычно это делаете в браузере. Записная книжка создает *ZIP-файл* даже в том случае, если загружается один файл.

## <a name="next-steps"></a>Дальнейшие действия

- [Access cloud data in a notebook](access-data-resources-jupyter-notebooks.md) (Доступ к облачным данным в записной книжке)
