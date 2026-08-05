# Ansible Role: Установка платформы 1С Преприятие

Роль Ansible для установки и базовой настройки сервера  
**«1С:Предприятие»** на Linux-системах.

Роль предназначена для использования во внутренних инфраструктурах
и ориентирована на **офлайн-установку** из локальных пакетов поставщика.

---

## 1. Назначение

Роль обеспечивает:
- установку серверных компонентов платформы «1С:Предприятие»;
- установку системных зависимостей;
- регистрацию и запуск сервиса 1С через `systemd`;
- контроль поддерживаемых операционных систем (fail-fast).

Роль **не выполняет** настройку прикладных решений, СУБД и лицензирования.
Внимание!!! Перед запуском на выполнение роли, в паке files должны уже находится необходимые пакеты deb/rpm, полученные
у 1С либо лицензионного дистрибьютера.

---

## 2. Поддерживаемые операционные системы

| Семейство ОС | Дистрибутивы |
|-------------|--------------|
| Debian | Debian, Ubuntu |
| Debian-based | Astra Linux (CE / SE)* |
| RedHat | RHEL, CentOS |
| RedHat-based | AlmaLinux, Rocky Linux |
| Altlinux | ALT Linux (пакеты ставятся через `apt-rpm`) |

\* Astra Linux определяется Ansible как `ansible_os_family: Debian`,
поэтому пойдёт по общей Debian-ветке роли; отдельно не тестировалось.

> При запуске на неподдерживаемой ОС роль завершает выполнение
> с диагностическим сообщением (список поддерживаемых семейств
> задаётся переменной `onec_supported_os_families`).

---

## 3. Требования

- Ansible версии **2.12** и выше
- Архитектура: **x86_64**
- Наличие `systemd`
- SSH-доступ с правами `sudo`
- Установочные пакеты 1С (DEB или RPM)
- Для ALT Linux: коллекция `community.general` (модуль `apt_rpm`) —
  установи через `ansible-galaxy collection install community.general`

---

## 4. Подготовка установочных пакетов

### 4.1 Debian / Ubuntu / Astra Linux

Перед запуском роли положите пакеты **в подпапку `files/deb/`**:

```
files/deb/1c-enterprise-8.3.24.1234-common_8-3-24-1234_amd64.deb
files/deb/1c-enterprise-8.3.24.1234-crs_8-3-24-1234_amd64.deb
files/deb/1c-enterprise-8.3.24.1234-server_8-3-24-1234_amd64.deb
files/deb/1c-enterprise-8.3.24.1234-ws_8-3-24-1234_amd64.deb
```

### 4.2 RHEL / CentOS / AlmaLinux / Rocky Linux

Аналогично — пакеты **в подпапку `files/rpm/`**:

```
files/rpm/1c-enterprise-8.3.24.1234-common-8.3.24.1234.x86_64.rpm
files/rpm/1c-enterprise-8.3.24.1234-crs-8.3.24.1234.x86_64.rpm
files/rpm/1c-enterprise-8.3.24.1234-server-8.3.24.1234.x86_64.rpm
files/rpm/1c-enterprise-8.3.24.1234-ws-8.3.24.1234.x86_64.rpm
```

Точные имена файлов зависят от `onec_version` и `onec_arch` — они
должны совпадать с именами, которые формируют `vars/Debian.yml` /
`vars/RedHat.yml`. Несовпадение имени приведёт к ошибке на шаге
копирования пакетов.

### 4.3 ALT Linux

Тоже используется подпапка `files/rpm/`, но точные имена пакетов
для ALT могут отличаться от RHEL-сборок — сверься с реальными
именами файлов из дистрибутива 1С для ALT и при необходимости
поправь список в `vars/Altlinux.yml`.

---

## 5. Переменные роли

```yaml
onec_version: "8.3.ХХ.ХХХХ"  # ОБЯЗАТЕЛЬНАЯ переменная, версия которая у вас имеется
onec_arch: x86_64
onec_service_instance: srv1cv8@default
onec_install_dir: /tmp/1c-install
onec_supported_os_families:   # можно переопределить список поддерживаемых семейств
  - Debian
  - RedHat
```

Роль явно падает на старте (assert), если `onec_version` не задан.

## 6. Установка и запуск через Ansible Galaxy

### 6.1 Установка роли

Роль может быть установлена из Ansible Galaxy с управляющей машины:

```bash
ansible-galaxy role install halif.onec_server
```

Либо через `requirements.yml`:

```yaml
roles:
  - name: halif.onec_server
```


