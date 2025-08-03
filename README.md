# itd-bitrix
FreePBX integration module for Bitrix24

Выполнить shell-скрипт, install.sh:
<code>
#!/bin/bash
fwconsole setting MODULE_REPO https://nnekipelov.github.io/itd-bitrix/modules-15-itd.xml
fwconsole ma refreshsignatures
</code>

```mermaid
flowchart TD
    A["Поступление звонка в Asterisk"] --> B{"DID в списке?"}
    B -- Нет --> X1["Игнорируем звонок"]
    B -- Да --> C["AGI: itd_bitrix_incoming.php"]
    C --> D["Создание объекта звонка CID, uniqueid, type=in"]
    D --> E["Запрос: Получение имени/фамилии из Битрикс"]
    E --> F["Установка CALLERID_name"]
    F --> G["Запрос: Регистрация звонка в Битрикс"]
    G --> H["БД: Сохранение звонка: Сохраняем bitrix_call_id, uniqueid, type"]
    H --> I["Дальнейшая маршрутизация: Приветствие / IVR / Очередь"]
    I --> J{"Звонок попал в очередь и был отвечен опретором?"}
    J -- Нет --> K1["Ожидаем завершения вызова"]
    J -- Да --> K["AGI: itd_bitrix_show.php ."]
    K --> L["БД: Получение bitrix_call_id"]
    L --> M["Получение USER_ID по номеру добавочного оператора из Битрикс"]
    M --> N["Запрос: Показ карточки звонка в Битрикс"]
    K1 --> Z["AGI: itd_bitrix_hangup.php"]
    N --> Z
    Z --> Z1["Поиск незавершённого звонка в БД"]
    Z1 --> Z2["Запрос: Завершение звонка в Битрикс."]
    Z2 --> Z3["Запрос: Привязка записи если включено"]
    Z3 --> Z4["Запрос: Скрытие карточки звонка"]
    Z4 --> Z5["БД: Пометка звонка как завершённого"]
    Z5 --> Z6["БД: Удаление старых звонков > 48 ч"]

    Z2@{ shape: rect}
     B:::Sky
     X1:::Ash
     C:::Peach
     E:::Rose
     F:::Pine
     G:::Rose
     J:::Sky
     K:::Peach
     N:::Rose
     Z:::Peach
     Z2:::Rose
     Z3:::Rose
     Z4:::Rose
    classDef Ash stroke-width:1px, stroke-dasharray:none, stroke:#999999, fill:#EEEEEE, color:#000000
    classDef Sky stroke-width:1px, stroke-dasharray:none, stroke:#374D7C, fill:#E2EBFF, color:#374D7C
    classDef Pine stroke-width:1px, stroke-dasharray:none, stroke:#254336, fill:#27654A, color:#FFFFFF
    classDef Peach stroke-width:1px, stroke-dasharray:none, stroke:#FBB35A, fill:#FFEFDB, color:#8F632D
    classDef Rose stroke-width:1px, stroke-dasharray:none, stroke:#FF5978, fill:#FFDFE5, color:#8E2236
```
