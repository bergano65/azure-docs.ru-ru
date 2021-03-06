---
title: Обработку строк ошибок с сопоставлением потоков данных в фабрике данных Azure
description: Узнайте, как выполнять обработку ошибок усечения SQL в фабрике данных Azure с помощью сопоставления потоков данных.
services: data-factory
author: kromerm
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/28/2019
ms.author: makromer
ms.openlocfilehash: 3fe3403ad06d82ba5ccd33d2718bf0e5eff64490
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73166542"
---
# <a name="handle-sql-truncation-error-rows-in-data-factory-mapping-data-flows"></a>Обработку строк ошибок усечения SQL в потоках данных сопоставления фабрики данных

Распространенным сценарием фабрики данных при использовании сопоставления потоков данных является написание преобразованных данных в базе данных SQL Azure. В этом сценарии распространенным условием ошибки, которое необходимо предотвратить, является усечение столбца. Выполните следующие действия, чтобы вести журнал столбцов, которые не помещаются в целевой строковый столбец, что позволяет потоку данных продолжить работу в этих сценариях.

## <a name="scenario"></a>Сценарий

1. У нас есть Целевая таблица базы данных SQL Azure, которая содержит ```nvarchar(5)``` столбец с именем Name.

2. Внутри нашего потока данных мы хотим сопоставлять названия фильмов из нашего приемника с этим целевым столбцом "Name".

    ![Поток данных фильмов 1](media/data-flow/error4.png)
    
3. Проблема заключается в том, что заголовок фильма не умещается в столбце приемника, в котором может содержаться только 5 символов. При выполнении этого потока данных появится сообщение об ошибке следующего вида: ```"Job failed due to reason: DF-SYS-01 at Sink 'WriteToDatabase': java.sql.BatchUpdateException: String or binary data would be truncated. java.sql.BatchUpdateException: String or binary data would be truncated."```

## <a name="how-to-design-around-this-condition"></a>Как создать обход этого условия

1. В этом сценарии максимальная длина столбца "Name" составляет пять символов. Итак, добавим преобразование «Условное разбиение», которое позволит нам записывать строки с «заголовками», длина которых превышает пять символов, одновременно позволяя остальным строкам, которые могут поместиться в это пространство, записываться в базу данных.

    ![Условное разбиение](media/data-flow/error1.png)

2. Это преобразование "Условное разбиение" определяет максимальную длину "Title", которая должна быть пятью. Любая строка, которая меньше или равна 5, перейдет в поток ```GoodRows```. Любая строка, превышающая пять, перейдет в поток ```BadRows```.

3. Теперь нужно регистрировать строки, на которые произошел сбой. Добавьте преобразование приемника в поток ```BadRows``` для ведения журнала. Здесь мы будем «автоmap» все поля, чтобы мы зарегистрировали полную запись транзакции. Это файловый выход CSV-файла, разделенный текстом, в один файл в хранилище больших двоичных объектов. Мы назовем файл журнала «бадровс. csv».

    ![Неправильные строки](media/data-flow/error3.png)
    
4. Завершенный поток данных показан ниже. Теперь можно разделить строки ошибок, чтобы избежать ошибок усечения SQL и поместить эти записи в файл журнала. В то же время успешные строки могут продолжать записывать в нашу целевую базу данных.

    ![завершение потока данных](media/data-flow/error2.png)

## <a name="next-steps"></a>Дальнейшие действия

* Создайте оставшуюся часть логики потока данных с помощью [преобразования потоков данных](concepts-data-flow-overview.md)сопоставления.
