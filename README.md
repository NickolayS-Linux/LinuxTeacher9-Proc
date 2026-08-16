# LinuxTeacher9-Proc
Размещаем свой RPM в своем репозитории, ДЗ №8

Создал аккаунт на GitHub - https://github.com/

Предварительно установленное и настроенное следующее ПО:

ПК на Linux c 16 ГБ ОЗУ или виртуальная машина с системой Ubuntu.

Oracle VirtualBox (https://www.virtualbox.org/wiki/Linux_Downloads).

Все дальнейшие действия были проверены при использовании VirtualBox 7.2.6 r172322, хостовая ОС: Ubuntu 24.04 Desktop.

Гостевая система — Ubuntu 24.04.4 LTS. (Сервер и клиент

Оформить отчет в README-файле в GitHub-репозитории.

Цель домашнего задания:

1. Реализация аналога ps ax
 
echo "Вывод процессов Pid, Ppid, Name..."
while read -r line; do
    if [ -d "$line" ]; then
        cat $line/status 2>/dev/null | grep -E "Pid|Ppid|Name" | awk '$2 > 0'
    fi
done < <(find /proc -maxdepth 1 -type d -regextype posix-extended -regex ".*/[0-9]+")

Результат работы первой части скрипта:

Name:   docker-proxy
Pid:    2690
PPid:   2386
Name:   docker-proxy
Pid:    2696
PPid:   2386
Name:   postgres
Pid:    2754
PPid:   2647
Name:   postgres
Pid:    2755
PPid:   2647
Name:   postgres
Pid:    2756
PPid:   2647


----------------------------
2. Реализация аналога lsof

echo " Вывод открытых файлов..."

# Перебор всех процессов в /proc

for pid in /proc/[0-9]*; do
    # Получаем имя процесса
    realName=$(cat $pid/status 2>/dev/null | grep -E "Name" | cut -d ':' -f2-2 | head -1)
    # Проверяем, что это действительно каталог процесса (не мета-объект)
    if [ -d "$pid" ]; then
        # Читаем команду процесса из /proc/pid/cmdline
        cmd=$(cat "$pid/cmdline" | tr '\0' ' ')
        # Проходим по всем символичным ссылкам в /proc/pid/fd
        for entry in "$(ls "$pid/fd")"; do
            #if [ -L "$pid/fd/$entry" ]; then
                # Получаем реальный путь, на который указывает ссылка
                real_path=$(readlink "$pid/fd/$entry")
                echo "PID: ${pid//[^0-9]}, Имя процесса: $realName, Файл: $cmd"
            #fi
        done
    fi
done



