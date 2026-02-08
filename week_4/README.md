# Проект AD Lab - Блокировка USB-устройств через GPO

## Цель
Закрепить работу с Group Policy в Active Directory  
на практическом кейсе — **централизованная блокировка USB-накопителей**
на клиентских компьютерах домена.


## Используемая инфраструктура

**Домен:**
- corp.local

**Сервер:**
- DC01 — Windows Server 2022
  - Active Directory Domain Services (AD DS)
  - DNS Server

**Клиент:**
- CLIENT01 — Windows 10
  - Член домена corp.local

**OU:**
- OU_Computers
- OU_Users

**Виртуализация:**
- Oracle VirtualBox

 ## Теория (кратко)

### Что такое Group Policy (GPO)

Group Policy — механизм централизованного управления настройками:
- компьютеров (Computer Configuration)
- пользователей (User Configuration)

Политики применяются:
- к Site
- к Domain
- к OU


**Требование:**
- Запретить использование USB-накопителей
- Применять политику **к компьютерам**
- Управление должно быть централизованным через AD

---

## Шаг 1. Подготовка Active Directory

### Проверка структуры OU

Действия выполнены на DC01:

- Открыта оснастка **Active Directory Users and Computers**
- Проверено наличие организационных подразделений:
  - OU_Computers
  - OU_Users
- Компьютер CLIENT01 перемещён из контейнера `Computers`
  в OU `OU_Computers`

Результат:
- CLIENT01 находится в целевом OU
- Возможна привязка GPO на уровне компьютеров


В разделе **Group Policy Objects** создан новый объект групповой политики:

   - Имя GPO: `CORP-WS-BlockUSB`
   - GPO создан в домене `corp.local`

Результат:
- Объект GPO `CORP-WS-BlockUSB` отображается в списке
  Group Policy Objects
- Политика готова к настройке и привязке


## Шаг 2. Настройка групповой политики (GPO)

Действия выполнены на DC01:

- Открыт **Group Policy Management Editor**
- Отредактирован GPO `CORP-WS-BlockUSB`

Путь в редакторе GPO:

Computer Configuration  
→ Policies  
→ Administrative Templates  
→ System  
→ Removable Storage Access

### Настроенный параметр

- **All Removable Storage classes: Deny all access**
- Состояние: **Enabled**

Результат:
- Запрещён доступ ко всем съёмным USB-устройствам
- Политика применяется на уровне компьютера


## Шаг 3. Привязка GPO к OU

Действия выполнены на DC01:

- GPO `CORP-WS-BlockUSB` привязан к OU `OU_Computers`
- Компьютер CLIENT01 находится в данном OU
- Наследование групповых политик включено

Результат:
- Политика применяется ко всем компьютерам в OU_Computers


## Шаг 4. Обновление групповых политик

Действия выполнены на CLIENT01:

Выполнена команда:
```cmd
gpupdate /force```


## Итог

В рамках лабораторной работы:

- Развёрнута доменная инфраструктура Active Directory
- Создан и настроен объект групповой политики (GPO)
- Реализована централизованная блокировка USB-устройств
- Подтверждено корректное применение политики на клиенте домена