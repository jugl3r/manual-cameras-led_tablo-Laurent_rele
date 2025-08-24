# Руководство по активации устройств

## 📋 Общее описание процесса

Данное руководство описывает процедуру первичного обнаружения, активации и настройки устройств в сети после их физического монтажа.

### Участники процесса:
1.  **Монтажная группа:** Осуществляет установку оборудования, подключение к сети и электропитанию.
2.  **Системный администратор:** Выполняет первичную сетевую настройку и активацию устройств согласно данному мануалу.

### Последовательность работ:
1.  Монтажник устанавливает устройства и подключает их в сеть.
2.  Монтажник сообщает системному администратору о готовности оборудования к настройке.
3.  Системный администратор обнаруживает устройства.
4.  Системный администратор назначает устройствам IP-адреса и активирует их, задавая пароль.
5.  Системный администратор проверяет доступность веб-интерфейса и вносит данные в реестр.

---

## 🖥️ 1. Настройка камер (Установка и настройка SADP на Linux) 

Официальная утилита SADP (Search Active Devices Protocol) от Hikvision предназначена для ОС Windows. Для её запуска в Linux-среде требуется эмулятор Wine.

### 1. Установка Wine

Перед установкой SADP необходимо установить Wine и его зависимости.

**Для систем на основе Debian/Ubuntu:**

```bash
# Добавляем поддержку 32-битной архитектуры
sudo dpkg --add-architecture i386

# Обновляем списки пакетов
sudo apt update && sudo apt upgrade -y

# Устанавливаем Wine и необходимые компоненты
sudo apt install -y wine64 wine32 winetricks

# Проверяем версию установленного Wine
wine --version
```

**Для систем на основе RHEL/CentOS/Fedora:**
Рекомендуется следовать актуальным инструкциям на [официальном Wiki WineHQ.](#https://gitlab.winehq.org/wine/wine/-/wikis/Download)

### 2. Установка SADP

После успешной установки Wine необходимо установить сам SADP

Wine поддерживает не все актуальные версии SADP, последняя актуальнная версия, поддерживая Wine находится в репозитории

Предварительно перенесите файл "SADP.exe" (находится в репозитории, если не работает, связаться с @jugl3r в телеграмме для получения корректного файла) на сервер, в котором будет производиться настройка устройств 

**Для систем на основе Debian/Ubuntu:**

```bash
# Устанавливаем SADP с помощью Wine
wine SADP.exe

# Следуйте инструкциям установщика, менять место установки не рекомендуется

```
**Для систем на основе RHEL/CentOS/Fedora:**
```bash
# Устанавливаем SADP с помощью Wine
wine SADP.exe

# Следуйте инструкциям установщика, менять место установки не рекомендуется

```
### 3. Запуск SADP

Дальнейшией шаги требуется выполнять в графической среде Linux

После успешной установки SADP необходимо в терминале запустить SADP для первичной установки камер

```bash
# Перейти в директорию с исполняемым файлом (если не меняли путь при установке, путь должен быть актуальным)
cd ~/.wine/drive_c/Program\ Files\ \(x86\)/SADP/SADP/

# Запустить файл SADPTool.exe
wine SADPTool.exe

```

После чего откроется первоначальное окно SADP

Первоначальное окно SADP:
![alt text](https://github.com/jugl3r/manual-cameras-led_tablo-Laurent_rele/raw/main/camera/images/SADP_first_screen.png)

При отсутствии камер в приложении передать информацию монтажнику, что-бы проверил подключение камер в сеть

Новые активированные камеры будут появляться в пометкой "INACTIVE" в таблице "STATUS", их и следует настроить



1. Слева от неактивированной камеры нажать на квадрат, что-бы конфигурировать именно эту камеру

![alt text](https://github.com/jugl3r/manual-cameras-led_tablo-Laurent_rele/raw/main/images/camera/first_settings.png)

2. В правой части появится окно с настройками камеры, нажать на 'ENABLE DHCP", что-бы камера получила актуальные сетевые настройки от роутера

![alt text](https://github.com/jugl3r/manual-cameras-led_tablo-Laurent_rele/raw/main/images/camera/dhcp_configure_camera.png)

3. Необхожимо придумать админский пароль для камеры, для этого в самом конце есть поле Administator Password, который отвечает за этот параметр, впишите в это поле желаемый вами пароль

![alt text](https://github.com/jugl3r/manual-cameras-led_tablo-Laurent_rele/raw/main/images/camera/admin_password.png)

4. Сохраните новые настройки камеры нажатием на кнопку "Modify"


5. Зайдите на роутер и зарезервируйте адрес за камерой

6. После того, как камера получила сетевые настройки, нужно нажать на её новый IP-адрес в SADP, после чего откроется браузер с этой камерой, введите логин "admin" и ранее вписанный вами пароль и проверьте, что камера функционирует и показывает изображение

WEB-страница камеры:
![alt text](https://github.com/jugl3r/manual-cameras-led_tablo-Laurent_rele/raw/main/images/camera/camera_web.png)
![alt text](https://github.com/jugl3r/manual-cameras-led_tablo-Laurent_rele/raw/main/images/camera/camera_check_functional.png)

7. Для того, что-бы в ПО отображались снапшоты при всех событиях, нужно поменять конфигурацию RTSP

Перейдите в пункт "Configuration" -> "Network Service" -> "RTSP" и переключите пункт "Authentication mode" на "digest/basic"

![alt text](https://github.com/jugl3r/manual-cameras-led_tablo-Laurent_rele/raw/main/images/camera/snapshot_configuration.png)

8. Для того, что бы работали rtsp-потоки, необходимо настроить конфиругацию rtsp в Docker, в репозитории есть каталог go2rtc, это и есть вся конфигурация rtsp

Необходимо в файле по пути go2rtc/go2rtc/go2rtc.yaml заменить IP-адрес и учётные записи на актуальныеЖ
```bash

streams:
  enter:
    - rtsp://admin:<password>@192.168.1.100:554/ISAPI/Streaming/Channels/102
  exit:
    - rtsp://admin:<password>@192.168.1.101:554/ISAPI/Streaming/Channels/102

```
Так-же необходимо заменить в конце файла учётные данные от камер на актуальные
```bash

username: "admin"  # default "", Basic auth for WebUI
password: "<password>" # default "", Basic auth for WebUI

```


Если у вас есть белый IP-адрес, то замените соотвествующую строку на ваш IP-адрес:
```bash

webrtc:
  candidates:
    - 1.1.1.1:8555 # if you have static public IP-address
```

Теперь запустите docker-compose.yml
```bash

docker-compose up -d --build

```

Проверьте, что контейнер с go2rtc запустился и работает корректно

```bash

docker ps -a | grep go2rtc

```
Теперь камеры готовы для передачи и работы с rtsp-потоками

## 🖥️ 2. Настройка реле Laurent

При подключении устройства Laurent в сеть ему выдаётся его стандартный ip-адрес 192.168.0.101, в случае, если подсеть Laurent не сходится с вашей подсетью можно выполнить следующее:

```bash
# Выдаем "второй" адрес сетевому интерфейсу на сервере
sudo ip addr add 192.168.0.10/24 dev <название интерфейса>

```
1. перейти в веб-интерфейс Laurent по адресу 192.168.0.101, при переходе будет запрошен логин и пароль: admin / Laurent
2. Перейти в пункт "Общие сетевые настройки", найти "Сетевые настройки" и поставить напротив значения "DHCP" значение "ON",
предварительно запомнив МАC-адрес, что-бы дальше было легче его найти
![alt text](https://github.com/jugl3r/manual-cameras-led_tablo-Laurent_rele/raw/main/images/laurent/laurent_first_settings.png)
![alt text](https://github.com/jugl3r/manual-cameras-led_tablo-Laurent_rele/raw/main/images/laurent/laurent_DHCP_enable.png)

3. Перезагрузить реле с помощью команды "Программная перезагрузка"
![alt text](https://github.com/jugl3r/manual-cameras-led_tablo-Laurent_rele/raw/main/images/laurent/Laurent_reboot.png)

4. Зарезервировать новый IP-адрес за реле в роутере
5. Проверить веб-интерфейс по новому IP-адресу

После следует проверить работоспособность реле на команды открытия и закрытия шлагбаума, для этого необходимо:
1. Перейти в веб-интерфейс реле по IP-адресу
2. Перейти в пункт "Электромагнитное реле"
![alt text](https://github.com/jugl3r/manual-cameras-led_tablo-Laurent_rele/raw/main/images/laurent/Laurent_rele.png)
3. Проверить по кнопкам "RELE-{1-4}" наличие реакции на открытие/закрытие
![alt text](https://github.com/jugl3r/manual-cameras-led_tablo-Laurent_rele/raw/main/images/laurent/Laurent_rele_check.png)
4. В случае каких-либо проблем с открытием/закрытием описать и передать проблему монтажнику для решения проблемы

## 🖥️ 3. Настройка Табло

**Рекомендуется использовать приложение "USR-TCP232-T24-V5.1.1.20.exe" для удобного мониторинга появляения устойств в сети**

Данная утилита поможет быстро и легко обнаружить табло в сети,для этого нужно в терминале выполнить:

```bash
# Запускаем утилиту
wine USR-TCP232-T24-V5.1.1.20.exe
```
После чего нажать на кнопку "SEARCH IN LAN", после в таблице будет отображаться новые табло
При отсутствии табло в приложении передать информацию монтажнику, что-бы проверил подключение табло в сеть
![alt text](https://github.com/jugl3r/manual-cameras-led_tablo-Laurent_rele/raw/main/images/Led-tablo/Led_tablo_check.png)

При подключении табло в сеть ему выдаётся его стандартный ip-адрес 192.168.1.7, в случае, если подсеть табло не сходится с вашей подсетью можно выполнить следующее:

```bash
# Выдаем "второй" адрес сетевому интерфейсу на сервере
sudo ip addr add 192.168.1.10/24 dev <название интерфейса>

```
1. перейти в веб-интерфейс табло по IP-адресу 192.168.1.7, при переходе будет запрошен логин и пароль: admin / admin
2. Перейти в пункт "Local IP config", найти пункт "IP-TYPE" и поставить напротив значения "DHCP", предварительно запомнив МАC-адрес, что-бы дальше было легче его найти
![alt text](https://github.com/jugl3r/manual-cameras-led_tablo-Laurent_rele/raw/main/images/Led-tablo/Led_tablo_DHCP.png)
3. Перезагрузить табло с помощью команды "Restart module" в последнем пункте "Restart"
![alt text](https://github.com/jugl3r/manual-cameras-led_tablo-Laurent_rele/raw/main/images/Led-tablo/Led_tablo_reboot.png)
4. Зарезервировать новый IP-адрес за реле в роутере
5. Проверить веб-интерфейс по новому IP-адресу
