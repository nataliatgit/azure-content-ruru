В следующей таблице перечислены ограничения, связанные с разными уровнями служб (S1, S2, F1). Сведения о стоимости каждой *единицы* на каждом уровне см. в разделе [Цены на центр IoT](https://azure.microsoft.com/pricing/details/iot-hub/).

| Ресурс | Стандартный S1 | Стандартный S2 | Бесплатный F1 |
| -------- | ----------- | ----------- | ------- |
| Сообщений в день | 400 000 | 6 000 000 | 8000 |
| Максимальное число единиц | 200 | 200 | 1 |

> [AZURE.NOTE] Если предполагается использование более 200 единиц с центром уровня S1 и S2, обратитесь в службу технической поддержки Майкрософт.

В следующей таблице перечислены ограничения, которые применяются к ресурсам центра IoT:

| Ресурс | Ограничение |
| -------- | ----- |
| Максимальное число центров IoT на подписку Azure | 10 |
| Максимальное число удостоверений устройств,<br/> возвращаемых в одном вызове | 1000 |
| Максимальный срок хранения сообщения центра IoT | 7 дней |
| Максимальный размер сообщения, отправляемого с устройства в облако | 256 KB |
| Максимальный размер пакета, отправляемого с устройства в облако | 256 KB |
| Максимальное число сообщений в пакете, отправляемом с устройства в облако | 500 |
| Максимальный размер сообщения, отправляемого из облака на устройство | 64 КБ |
| Максимальный срок жизни для сообщений, отправляемых из облака на устройство | 2 дня |
| Максимальное число доставок для сообщений, <br/> отправляемых из облака на устройство | 100 |
| Максимальное число доставок для сообщений обратной связи <br/> в ответ на сообщение, отправляемое из облака на устройство | 100 |
| Максимальный срок жизни для сообщений обратной связи <br/> в ответ на сообщение, отправляемое из облака на устройство | 2 дня |

Служба центра IoT регулирует запросы при превышении следующих квот:

| Регулирование | Значение концентратора |
| -------- | ------------- |
| Операции с реестром удостоверений <br/> (создание, извлечение, перечисление, обновление и удаление), импорт и экспорт отдельных элементов или пакетов <br/> | 100/мин/единица, до 5000/мин |
| Подключение устройств | 120/с/единица (для S2), 12/с/единица (для S1). Минимум 100/с |
| Передачи с устройства в облако | 120/с/ед (для S2), 12/с/ед (для S1) <br/> Минимум 100/с |
| Передачи из облака на устройство | 100/мин/единица |
| Получение из облака на устройство | 1000/мин/единица |

<!---HONumber=AcomDC_0204_2016-->