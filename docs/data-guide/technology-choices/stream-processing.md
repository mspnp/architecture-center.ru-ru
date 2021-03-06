---
title: Выбор технологии потоковой обработки
description: ''
author: zoinerTejada
ms.date: 02/12/2018
ms.topic: guide
ms.service: architecture-center
ms.subservice: cloud-fundamentals
ms.openlocfilehash: 342e44d960682c72901a7482caaf328514eb73d8
ms.sourcegitcommit: c053e6edb429299a0ad9b327888d596c48859d4a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58246305"
---
# <a name="choosing-a-stream-processing-technology-in-azure"></a>Выбор технологии потоковой обработки в Azure

В этой статье сравниваются варианты технологий потоковой обработки в реальном времени в Azure.

При потоковой обработке в реальном времени система получает сообщения из очередей или файлового хранилища, затем обрабатывает их и пересылает результат в другую очередь сообщений, файловое хранилище или базу данных. Процесс обработки может включать такие задачи, как запрашивание, фильтрация и агрегирование сообщений. Системы потоковой обработки должны иметь возможность получать неограниченное число потоков данных и выводить результаты с минимальной задержкой. Дополнительные сведения см. в статье [об обработке в реальном времени](../big-data/real-time-processing.md).

<!-- markdownlint-disable MD026 -->

## <a name="what-are-your-options-when-choosing-a-technology-for-real-time-processing"></a>Варианты при выборе технологии для обработки в реальном времени

<!-- markdownlint-enable MD026 -->

Все следующие хранилища данных в Azure будут соответствовать основным требованиям к обработке в реальном времени:

- [Azure Stream Analytics](/azure/stream-analytics/)
- [HDInsight с потоковой передачей Spark](/azure/hdinsight/spark/apache-spark-streaming-overview);
- [Apache Spark в Azure Databricks](/azure/azure-databricks/)
- [HDInsight со Storm](/azure/hdinsight/storm/apache-storm-overview);
- [Функции Azure](/azure/azure-functions/functions-overview)
- [веб-задания службы приложений Azure](/azure/app-service/web-sites-create-web-jobs)

## <a name="key-selection-criteria"></a>Основные критерии выбора

Для сценариев обработки в реальном времени сначала выберите службу, которая соответствует вашим требованиям, ответив на следующие вопросы:

- Какой подход к созданию логики потоковой обработки вы предпочитаете использовать: декларативный или императивный?

- Нужна ли встроенная поддержка для обработки темпоральной информации или обработки методом окна?

- Вы получаете данные в форматах помимо Avro, JSON или CSV? Если да, выберите варианты с поддержкой всех форматов с помощью пользовательского кода.

- Требуется ли возможность увеличения скорости обработки свыше 1 ГБ/с? Если да, выберите варианты с поддержкой масштабирования размера кластера.

## <a name="capability-matrix"></a>Матрица возможностей

В следующих таблицах перечислены основные различия в возможностях.

### <a name="general-capabilities"></a>Общие возможности

| | Azure Stream Analytics | HDInsight с потоковой передачей Spark | Apache Spark в Azure Databricks | HDInsight со Storm | Функции Azure | Веб-задания службы приложений Azure |
| --- | --- | --- | --- | --- | --- | --- |
| Программируемость | Язык запросов Stream Analytics, JavaScript | Scala, Python, Java | Scala, Python, Java, R | Java, C# | C#, F#, Node.js | C#, Node.js, PHP, Java, Python |
| Парадигма программирования | Декларативный подход | Сочетание декларативного и принудительного подхода | Сочетание декларативного и принудительного подхода | Императивная | Императивная | Императивная |
| Модель ценообразования | [Единицы потоковой передачи](https://azure.microsoft.com/pricing/details/stream-analytics/) | На час работы кластера | [Единицы Databricks](https://azure.microsoft.com/pricing/details/databricks/) | На час работы кластера | За выполнение функции и использование ресурсов | За час согласно плану службы приложений |  

### <a name="integration-capabilities"></a>Возможности интеграции

| | Azure Stream Analytics | HDInsight с потоковой передачей Spark | Apache Spark в Azure Databricks | HDInsight со Storm | Функции Azure | Веб-задания службы приложений Azure |
| --- | --- | --- | --- | --- | --- | --- |
| Входные данные | Центры событий Azure, Центр Интернета вещей, хранилище больших двоичных объектов Azure  | Центры событий, Центр Интернета вещей, Kafka, HDFS, BLOB-объекты хранилища, Azure Data Lake Storage  | Центры событий, Центр Интернета вещей, Kafka, HDFS, BLOB-объекты хранилища, Azure Data Lake Storage  | Центры событий, Центр Интернета вещей, BLOB-объекты хранилища, Azure Data Lake Store  | [Поддерживаемые привязки](/azure/azure-functions/functions-triggers-bindings#supported-bindings) | Служебная шина, очереди хранилища, BLOB-объекты хранилища, Центры событий, веб-перехватчики, Cosmos DB, служба файлов |
| Приемники |  Хранилище Azure Data Lake, База данных SQL Azure, большие двоичные объекты службы хранилища, Центры событий, Power BI, Хранилище таблиц, очереди и разделы служебной шины, Cosmos DB, Функции Azure  | HDFS, Kafka, BLOB-объекты хранилища, HDFS, Azure Data Lake Store, Cosmos DB | HDFS, Kafka, BLOB-объекты хранилища, HDFS, Azure Data Lake Store, Cosmos DB | Центры событий, служебная шина, Kafka | [Поддерживаемые привязки](/azure/azure-functions/functions-triggers-bindings#supported-bindings) | Служебная шина, очереди хранилища, BLOB-объекты хранилища, Центры событий, веб-перехватчики, Cosmos DB, служба файлов |

### <a name="processing-capabilities"></a>Возможности обработки

| | Azure Stream Analytics | HDInsight с потоковой передачей Spark | Apache Spark в Azure Databricks | HDInsight со Storm | Функции Azure | Веб-задания службы приложений Azure |
| --- | --- | --- | --- | --- | --- | --- |
| Встроенная поддержка обработки темпоральной информации или обработки методом окна | Yes | Да | Да | Да | Нет  | Нет  |
| Форматы входных данных | Avro, JSON или CSV, данные в кодировке UTF-8 | Любой формат с использованием пользовательского кода | Любой формат с использованием пользовательского кода | Любой формат с использованием пользовательского кода | Любой формат с использованием пользовательского кода | Любой формат с использованием пользовательского кода |
| Масштабируемость | [Секции запросов](/azure/stream-analytics/stream-analytics-parallelization) | Ограничивается размером кластера | Ограничивается конфигурацией масштабирования кластера Databricks | Ограничивается размером кластера | До 200 экземпляров приложений-функций, обрабатываемых одновременно | Ограничивается емкостью, предоставляемой согласно плану службы приложений |
| Поддержка обработки событий, наступивших с задержкой, и неупорядоченных событий | Yes | Да | Да | Да | Нет  | Нет  |

См. также:

- [Выбор технологии приема сообщений в реальном времени в Azure](./real-time-ingestion.md)
- [Обработка в режиме реального времени](../big-data/real-time-processing.md)
