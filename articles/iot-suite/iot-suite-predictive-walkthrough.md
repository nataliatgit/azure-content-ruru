<properties
 pageTitle="Пошаговое руководство по решению для диагностического обслуживания | Microsoft Azure"
 description="Пошаговое руководство по предварительно настроенному решению для диагностического обслуживания IoT Azure."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="aguilaaj"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="03/02/2016"
 ms.author="araguila"/>

# Пошаговое руководство по предварительно настроенному решению для диагностического обслуживания

## Введение

Предварительно настроенное решение для диагностического обслуживания в IoT Suite представляет собой комплексное решение для бизнес-сценария, предполагающего прогнозирование вероятной точки сбоя. Вы можете с упреждением использовать это предварительно настроенное решение в таких задачах, как оптимизация обслуживания. Решение объединяет ключевые службы Azure IoT Suite, включая рабочую область [машинного обучения Azure][lnk_machine_learning], с экспериментами для прогнозирования остаточного срока эксплуатации двигателей самолетов на основании общедоступного набора демонстрационных данных. Это решение предоставляет полную реализацию бизнес-сценария и может служить основой для планирования и реализации подобного решения IoT в соответствии с конкретными потребностями.

## Логическая архитектура

На следующей диаграмме показаны логические компоненты предварительно настроенного решения.

![][img-architecture]

Синие блоки представляют службы Azure, подготовленные в расположении, выбранном во время подготовки предварительно настроенного решения. Вы можете подготовить предварительно настроенное решение в одном из следующих регионов: восточная часть США, Северная Европа или Восточная Азия.

В определенных регионах, где подготавливается предварительно настроенное решение, некоторые ресурсы могут быть недоступны. Оранжевые блоки на схеме соответствуют службам Azure, подготовленным в доступном регионе (юго-центральный регион США, Западная Европа или Юго-Восточная Азия), ближайшем к выбранному региону.

Зеленый блок — это виртуальное устройство, представляющее двигатель самолета. Дополнительные сведения о виртуальных устройствах приведены ниже.

Серые блоки представляют компоненты, реализующие возможности *администрирования устройств*. В текущем выпуске предварительно настроенного решения для диагностического обслуживания эти ресурсы не подготавливаются. Дополнительные сведения об администрировании устройств см. в статье [Пошаговое руководство по работе с настроенным решением для удаленного мониторинга][lnk-remote-monitoring].

## Виртуальные устройства

В предварительно настроенном решении виртуальное устройство представляет двигатель самолета. В этом решении имеется два двигателя, установленных на одном самолете. Каждый двигатель выдает данные телеметрии четырех типов: датчика 9, датчика 11, датчика 14 и датчика 15. Эти датчики предоставляют данные, необходимые модели машинного обучения для расчета остаточного срока эксплуатации двигателя. Каждое виртуальное устройство отправляет в центр IoT следующие сообщения телеметрии.

*Счетчик циклов*. Цикл представляет собой выполненный рейс продолжительностью от 2 до 10 часов, в течение которых каждые полчаса регистрируются данные телеметрии.

*Телеметрия*. Имеется четыре датчика, представляющих атрибуты двигателя. Датчики имеют универсальные имена: датчик 9, датчик 11, датчик 14 и датчик 15. Эти четыре датчика представляют данные телеметрии, достаточные для определения остаточного срока эксплуатации из модели машинного обучения. Эта модель создается из общедоступного набора данных, который включает фактические данные датчиков двигателя. Дополнительные сведения о создании модели из исходного набора данных см. на странице [шаблона диагностического обслуживания из коллекции Cortana Analytics][lnk-cortana-analytics].

Виртуальные устройства могут обрабатывать следующие команды, отправляемые из центра IoT.

| Команда | Описание |
|---------|-------------|
| StartTelemetry | Управляет состоянием моделирования.<br/>Запускает отправку данных телеметрии с устройства. |
| StopTelemetry | Управляет состоянием моделирования.<br/>Останавливает отправку данных телеметрии с устройства. |

Центр IoT подтверждает получение команды от устройства.

## Задание Azure Stream Analytics

**Задание: телеметрия**. Работает на входящем потоке телеметрии устройства, используя две инструкции. Первая выбирает все данные телеметрии, поступающие от устройств, и отправляет эти данные в хранилище BLOB-объектов, откуда они будут визуализированы в веб-приложении. Вторая инструкция вычисляет средние значения датчика за скользящее двухминутное окно и отправляет эти данные через концентратор событий в **обработчик событий**.

## Обработчик событий

**Обработчик событий** принимает средние значения датчика за завершенный цикл и передает эти значения в API-интерфейс, который предоставляет обученную модель машинного обучения для вычисления остаточного срока эксплуатации двигателя.

## Машинное обучение Azure

Дополнительные сведения о создании модели из исходного набора данных см. на странице [шаблона диагностического обслуживания из коллекции Cortana Analytics][lnk-cortana-analytics].

## Пошаговое руководство

В этом разделе описываются компоненты решения, целевые варианты использования и приводятся примеры.

### Панель мониторинга диагностического обслуживания

Эта страница веб-приложения использует элементы управления PowerBI JavaScript (см. [репозиторий визуальных элементов PowerBI][lnk-powerbi]) для визуализации:

- выходных данных из задания Stream Analytics, поступающих в хранилище BLOB-объектов;
- значений остаточного срока эксплуатации и количества циклов для каждого двигателя самолета.

### Наблюдение за поведением облачного решения

Чтобы просмотреть подготовленные ресурсы, перейдите на портал Azure, а затем выберите группу ресурсов с выбранным вами именем решения.

![][img-resource-group]

При подготовке предварительно настроенного решения вы получите электронное сообщение со ссылкой на рабочую область машинного обучения. Также вы можете перейти к рабочей области машинного обучения со страницы [azureiotsuite.com][lnk-azureiotsuite] подготовленного решения, находящегося в состоянии **Готово**.

![][img-machine-learning]

На портале решения можно увидеть, что в этом примере есть четыре виртуальных устройства, представляющих два самолета с двумя двигателями в каждом. При этом на каждом двигателе установлено по четыре датчика. При первом переходе на портал решения моделирование будет остановлено.

![][img-simulation-stopped]

Щелкните **Запустить моделирование**, чтобы начать моделирование, в процессе которого вы увидите на панели мониторинга журнал показаний датчиков, значение остаточного срока эксплуатации, количество циклов и журнал значений остаточного срока эксплуатации.

![][img-simulation-running]

Когда значение остаточного срока эксплуатации станет меньше 160 (произвольное пороговое значение, выбранное для демонстрационных целей), на портале решения рядом с этим значением появится символ предупреждения, а цвет двигателя на рисунке изменится на желтый. Как можно заметить, значения остаточного срока эксплуатации имеют общую тенденцию к уменьшению, но при этом наблюдаются скачки вверх-вниз. Это происходит из-за различной продолжительности циклов и точности модели.

![][img-simulation-warning]

Полное моделирование, которое включает 148 циклов, занимает около 35 минут. Пороговое значение остаточного срока эксплуатации, равное 160, достигается примерно через 5 минут, а порог для обоих двигателей достигается примерно через 8 минут.

В процессе моделирования обрабатывается весь набор данных со 148 циклами. В результате можно получить окончательные значения остаточного срока эксплуатации и количества выполненных циклов.

Моделирование можно остановить в любой момент, но, если нажать кнопку **Запустить моделирование**, процесс перезапустится с самого начала.

## Дальнейшие действия

Возможно, после запуска предварительно настроенного решения для диагностического обслуживания вам понадобится внести в него изменения. Подробную информацию об этом см. в статье [Настройка предварительно настроенных решений][lnk-customize].

  
[img-architecture]: media/iot-suite-predictive-walkthrough/architecture.png
[img-resource-group]: media/iot-suite-predictive-walkthrough/resource-group.png
[img-machine-learning]: media/iot-suite-predictive-walkthrough/machine-learning.png
[img-simulation-stopped]: media/iot-suite-predictive-walkthrough/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-walkthrough/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-walkthrough/simulation-warning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-cortana-analytics]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md

<!---HONumber=AcomDC_0309_2016-->