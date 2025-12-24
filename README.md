NanoVNA - Очень компактный портативный анализатор векторных сетей
==========================================================
[release]: https://github.com/Rusik636/Ru-NanoVNA-D/releases

> **Примечание:** Это русская версия прошивки NanoVNA-D.  
> Оригинальная прошивка: [DiSlord/NanoVNA-D](https://github.com/DiSlord/NanoVNA-D)  
> Переводчик: [@Rusik636](https://github.com/Rusik636)

<div align="center">
<img src="/doc/nanovna-ru.jpg" width="480px">
</div>

# О проекте

**NanoVNA-H** и **NanoVNA-H4** — это очень компактные портативные анализаторы векторных сетей (VNA).
Это автономные портативные устройства с ЖК-дисплеем и аккумулятором.
Этот проект направлен на предоставление улучшенной прошивки для этого полезного инструмента для энтузиастов.

Этот репозиторий содержит исходный код улучшенной прошивки для NanoVNA-H и NanoVNA-H4.

Документация описывает процесс сборки и прошивки в системе MacOS или Linux (Debian или Ubuntu), другие системы Linux (или даже BSD) могут работать аналогично.

## Подготовка инструментов кросс-компиляции для ARM

**ОБНОВЛЕНИЕ**: Последние версии gcc работают для сборки NanoVNA, нет необходимости использовать старую версию.

### MacOSX

Установите инструменты кросс-компиляции и утилиту для обновления прошивки.

    brew tap px4/px4
    brew install gcc-arm-none-eabi-80
    brew install dfu-util

### Linux (Ubuntu)

Скачайте инструменты кросс-компиляции для ARM [отсюда](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads).

    wget https://developer.arm.com/-/media/Files/downloads/gnu-rm/8-2018q4/gcc-arm-none-eabi-8-2018-q4-major-linux.tar.bz2
    sudo tar xfj gcc-arm-none-eabi-8-2018-q4-major-linux.tar.bz2 -C /usr/local
    PATH=/usr/local/gcc-arm-none-eabi-8-2018-q4-major/bin:$PATH
    sudo apt install -y dfu-util

### Debian

    sudo apt install gcc-arm-none-eabi
    sudo apt install -y dfu-util

### Windows (MSYS2 MINGW64)

Для сборки прошивки на Windows рекомендуется использовать MSYS2 с окружением MINGW64.

#### 1. Установка MSYS2

1. Скачайте установщик MSYS2 с официального сайта: [https://www.msys2.org/](https://www.msys2.org/)
2. Запустите установщик и следуйте инструкциям (рекомендуется установка в `C:\msys64`)
3. После установки откройте **MSYS2 MSYS** из меню "Пуск"
4. Обновите систему пакетов:

   ```bash
   pacman -Syu
   ```

5. Закройте и снова откройте терминал MSYS2, затем повторите обновление:

   ```bash
   pacman -Syu
   ```

#### 2. Установка инструментов сборки

В терминале MSYS2 установите необходимые инструменты:

```bash
# Установка базовых инструментов разработки и компилятора
pacman -S --needed base-devel mingw-w64-x86_64-toolchain make git

# Установка dfu-util для прошивки устройства
pacman -S mingw-w64-x86_64-dfu-util
```

#### 3. Установка кросс-компилятора ARM

Скачайте и установите кросс-компилятор ARM:

1. Скачайте последнюю версию GCC ARM Embedded Toolchain с сайта ARM:
   [https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads](https://developer.arm.com/tools-and-software/open-source-software/developer-tools/gnu-toolchain/gnu-rm/downloads)

2. Распакуйте архив (например, `gcc-arm-none-eabi-XX.X-XXXX-XX-mingw-w64-i686-arm-none-eabi.tar.xz`) в удобное место, например `C:\msys64\opt\gcc-arm-none-eabi`

3. Добавьте путь к компилятору в переменную PATH. Откройте файл `~/.bashrc` в MSYS2:

   ```bash
   nano ~/.bashrc
   ```

4. Добавьте в конец файла:

   ```bash
   export PATH="/opt/gcc-arm-none-eabi/bin:$PATH"
   ```

5. Сохраните файл (Ctrl+O, Enter, Ctrl+X) и перезапустите терминал MSYS2

6. Проверьте установку:

   ```bash
   arm-none-eabi-gcc --version
   ```

#### 4. Установка драйверов STM32 DFU

Для прошивки NanoVNA через USB необходимо установить драйвер STM32 BOOTLOADER:

**Вариант 1: Использование Zadig (рекомендуется)**

1. Скачайте Zadig с официального сайта: [https://zadig.akeo.ie/](https://zadig.akeo.ie/)
2. Переведите NanoVNA в режим DFU (см. раздел "Прошивка устройства")
3. Подключите устройство к компьютеру
4. Запустите Zadig от имени администратора
5. В меню "Options" включите "List All Devices"
6. Выберите устройство "STM32 BOOTLOADER" или "STM Device in DFU Mode"
7. Убедитесь, что выбран драйвер "WinUSB" или "libusb-win32"
8. Нажмите "Install Driver" или "Replace Driver"

**Вариант 2: Установка драйвера от STMicroelectronics**

1. Скачайте STM32 DfuSe демонстрационную утилиту с сайта STMicroelectronics:
   [https://www.st.com/en/development-tools/stsw-stm32080.html](https://www.st.com/en/development-tools/stsw-stm32080.html)
   (драйвер входит в состав пакета)

2. Установите пакет, следуя инструкциям

3. Если драйвер не установился автоматически:
   - Откройте "Диспетчер устройств" (Win+X → Диспетчер устройств)
   - Найдите устройство "STM32 BOOTLOADER" или устройство с восклицательным знаком
   - Щелкните правой кнопкой мыши → "Обновить драйвер"
   - Выберите "Выполнить поиск драйверов на этом компьютере"
   - Укажите путь к папке с драйверами (обычно в `C:\Program Files (x86)\STMicroelectronics\Software\DfuSe\Drivers`)

#### 5. Сборка прошивки

1. Откройте **MSYS2 MinGW 64-bit** из меню "Пуск" (важно использовать именно MINGW64, а не MSYS)

2. Перейдите в директорию с исходным кодом:

   ```bash
   cd /d/Project/Ru-NanoVNA-D
   ```

   (замените путь на ваш реальный путь к проекту)

3. Инициализируйте подмодули (если еще не сделано):

   ```bash
   git submodule update --init --recursive
   ```

4. Для сборки прошивки NanoVNA-H:

   ```bash
   export TARGET=F072
   make clean
   make
   ```

5. Для сборки прошивки NanoVNA-H4:

   ```bash
   export TARGET=F303
   make clean
   make
   ```

#### 6. Прошивка устройства

После успешной сборки прошейте прошивку на устройство (см. раздел "Прошивка устройства" ниже).

**Примечание:** Если `dfu-util` не распознает устройство, убедитесь, что:
- Драйвер установлен корректно
- Устройство находится в режиме DFU
- Используется правильный VID:PID (0483:df11 для STM32)

## Получение исходного кода

Получите исходный код прошивки и подмодули, выполните это один раз для инициализации локального клона из GitHub:

    git clone https://github.com/Rusik636/Ru-NanoVNA-D.git
    cd NanoVNA-D
    git submodule update --init --recursive

## Обновление исходного кода

Чтобы получить обновления из репозитория GitHub, перейдите в директорию `NanoVNA-D` и введите:

    git pull

## Сборка прошивки NanoVNA-H

Перейдите в директорию `NanoVNA-D` и введите:

    export TARGET=F072
    make clean
    make

## Сборка прошивки NanoVNA-H4

Перейдите в директорию `NanoVNA-D` и введите:

    export TARGET=F303
    make clean
    make

## Прошивка устройства

Когда сборка прошивки завершена, вы можете прошить её на ваше устройство NanoVNA.
Сначала переведите устройство в режим DFU одним из следующих способов.

* Откройте устройство и замкните перемычкой контакт `BOOT0` на контакт `Vdd` при включении устройства.
* Выберите в меню Config->DFU (требуется свежая прошивка).
* Нажмите джойстик на вашем -H4 при включении устройства.

Затем прошейте прошивку с помощью `dfu-util` через USB.

#### Для NanoVNA-H:

Перейдите в директорию `NanoVNA-D` и введите:

    dfu-util -d 0483:df11 -a 0 -s 0x08000000:leave -D build/H.bin

#### Для NanoVNA-H4:

Перейдите в директорию `NanoVNA-D` и введите:

    dfu-util -d 0483:df11 -a 0 -s 0x08000000:leave -D build/H4.bin

#### Или просто введите после сборки прошивки (для обеих вариантов).

Перейдите в директорию `NanoVNA-D` и введите:

    make flash

#### Игнорируйте кажущееся сообщение об ошибке во время прошивки

Низкоуровневая утилита `dfu-util` выводит много информации, которая очень полезна особенно для разработчиков, но может сбить с толку пользователя.
В частности, пожалуйста, игнорируйте сообщение о повреждённой прошивке, это нормальное поведение устройства до очистки статуса.
Важно отметить, что после очистки статуса больше нет условия ошибки.

```
...
Determining device status...
DFU state(10) = dfuERROR, status(10) = Device's firmware is corrupt. It cannot return to run-time (non-DFU) operations
Clearing status
Determining device status...
DFU state(2) = dfuIDLE, status(0) = No error condition is present
...
```

## Сопутствующие инструменты

Существует несколько отличных сопутствующих инструментов для ПК от сторонних разработчиков.

* [Программное обеспечение NanoVNA-App](https://github.com/OneOfEleven/NanoVNA-H/blob/master/Release/NanoVNA-App.rar) от OneOfEleven
* [Программное обеспечение NanoVNASharp для Windows](https://drive.google.com/drive/folders/1IZEtx2YdqchaTO8Aa9QbhQ8g_Pr5iNhr) от hugen79
* [NanoVNA WebSerial/WebUSB](https://github.com/cho45/NanoVNA-WebUSB-Client) от cho45
* [Приложение Android NanoVNA](https://play.google.com/store/apps/details?id=net.lowreal.nanovnawebapp) от cho45
* [NanoVNASaver](https://github.com/NanoVNA-Saver/nanovna-saver) от mihtjel и участников проекта NanoVNA-Saver
* [TAPR VNAR4](https://groups.io/g/nanovna-users/files/NanoVNA%20PC%20Software/TAPR%20VNA) поддерживает NanoVNA от erikkaashoek
* [Набор инструментов NanoVNA](https://github.com/Ho-Ro/nanovna-tools) от Ho-Ro
* см. директорию [python](/python/README.md) для использования NanoVNA с Python и Jupyter Notebook.

## Документация

* [Руководство пользователя NanoVNA (ja)](https://cho45.github.io/NanoVNA-manual/) от cho45. [(en:google translate)](https://translate.google.com/translate?sl=ja&tl=en&u=https%3A%2F%2Fcho45.github.io%2FNanoVNA-manual%2F)
* [Группа пользователей NanoVNA](https://groups.io/g/nanovna-users/topics) на groups.io.

## Справочные материалы

* [Схемы](/doc/nanovna-sch.pdf)
* [Фото печатной платы](/doc/nanovna-pcb-photo.jpg)
* [Блок-схема](/doc/nanovna-blockdiagram.png)
* Набор доступен на https://ttrf.tk/kit/nanovna

## Примечание

Материалы проектирования аппаратного обеспечения раскрыты для предотвращения некачественных клонов. Пожалуйста, дайте мне знать, если у вас будет собственное устройство.

## Авторство

**Автор прошивки:**
* [@DiSlord](https://github.com/DiSlord/) - [Оригинальный репозиторий](https://github.com/DiSlord/NanoVNA-D)

**Основано на коде от:**
* [@edy555](https://github.com/edy555)

**Автор перевода на русский:**
* [@Rusik636](https://github.com/Rusik636) - [Репозиторий русской версии](https://github.com/Rusik636/Ru-NanoVNA-D)

### Участники
* [@OneOfEleven](https://github.com/OneOfEleven)
* [@hugen79](https://github.com/hugen79)
* [@cho45](https://github.com/cho45)
