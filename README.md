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
    
        PS=$(cat $line/status 2>/dev/null | awk '$2 >0' | grep -E "Pid|Ppid|Name")
        
        echo $PS
        
    fi
    
done < <(find /proc -maxdepth 1 -type d -regextype posix-extended -regex ".*/[0-9]+")

Результат работы первой части скрипта:

Name: xrdp PPid: 1 Pid: 704942

Name: postgres PPid: 704873 Pid: 704946

Name: postgres PPid: 704873 Pid: 704953

Name: postgres PPid: 704873 Pid: 704955

Name: postgres PPid: 704873 Pid: 704961

Name: postgres PPid: 704873 Pid: 704962

Name: postgres PPid: 704873 Pid: 704963

Name: postgres PPid: 704865 Pid: 704967

Name: postgres PPid: 704865 Pid: 704968

Name: postgres PPid: 704865 Pid: 704974

Name: postgres PPid: 704865 Pid: 704975

Name: postgres PPid: 704865 Pid: 704976

----------------------------

2. Реализация аналога lsof

echo " ------------------------- "

echo " Вывод открытых файлов..."

# Перебор всех процессов в /proc/pid/fd

for pid in /proc/[0-9]*; do

    # Получаем имя процесса
    
    realName=$(cat $pid/status 2>/dev/null | grep -E "Name" | cut -d ':' -f2-2 | head -1)    
    
    # Проверяем, что это действительно каталог процесса (не мета-объект)
    
    if [ -d "$pid" ]; then
    
        # Читаем команду процесса из /proc/pid/cmdline
        
        cmd=$(cat "$pid/cmdline" | tr '\0' ' ')
        
        # Проходим по всем символичным ссылкам в /proc/pid/fd
        
        for entry in $(ls "$pid/fd"); do
        
            if [ -L "$pid/fd/$entry" ]; then
            
                # Получаем реальный путь, на который указывает ссылка
                
                real_path=$(readlink "$pid/fd/$entry")
                
                echo "PID: ${pid//[^0-9]}, процесса: $realName, команда: $cmd, открытые файлы: $real_path"
                
            fi
            
        done
        
    fi
    
done

PID: 2762, процесса:    postgres, команда: postgres: logical replication launcher, открытые файлы: /dev/pts/0

PID: 2762, процесса:    postgres, команда: postgres: logical replication launcher, открытые файлы: anon_inode:[signalfd]

PID: 2762, процесса:    postgres, команда: postgres: logical replication launcher, открытые файлы: pipe:[17637]

PID: 2762, процесса:    postgres, команда: postgres: logical replication launcher, открытые файлы: anon_inode:[eventpoll]

**PID: 2762, процесса:    postgres, команда: postgres: logical replication launcher, открытые файлы: /var/lib/posgresql/data/pgdata/global/6100**

PID: 704865, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/test17 , открытые файлы: /dev/null

PID: 704865, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/test17 , открытые файлы: pipe:[2585392]

PID: 704865, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/test17 , открытые файлы: anon_inode:[eventpoll]

PID: 704865, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/test17 , открытые файлы: pipe:[2585392]

PID: 704865, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/test17 , открытые файлы: anon_inode:[signalfd]

PID: 704865, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/test17 , открытые файлы: pipe:[2585391]

PID: 704865, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/test17 , открытые файлы: pipe:[2585391]

PID: 704865, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/test17 , открытые файлы: pipe:[2585392]

PID: 704865, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/test17 , открытые файлы: socket:[2585396]

PID: 704865, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/test17 , открытые файлы: socket:[2585397]

PID: 704865, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/test17 , открытые файлы: socket:[2585401]

PID: 704873, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/data , открытые файлы: /dev/null

PID: 704873, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/data , открытые файлы: pipe:[2588003]

PID: 704873, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/data , открытые файлы: anon_inode:[eventpoll]

PID: 704873, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/data , открытые файлы: pipe:[2588003]

PID: 704873, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/data , открытые файлы: anon_inode:[signalfd]

PID: 704873, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/data , открытые файлы: pipe:[2588002]

PID: 704873, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/data , открытые файлы: pipe:[2588002]

PID: 704873, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/data , открытые файлы: pipe:[2588003]

PID: 704873, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/data , открытые файлы: socket:[2588007]

PID: 704873, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/data , открытые файлы: socket:[2588008]

PID: 704873, процесса:  postgres, команда: /opt/pgpro/1c-17/bin/postgres -D /var/lib/pgpro/1c-17/data , открытые файлы: socket:[2589390]

PID: 704938, процесса:  postgres, команда: postgres: logger                                             , открытые файлы: /dev/null

PID: 704938, процесса:  postgres, команда: postgres: logger                                             , открытые файлы: socket:[2587561]

PID: 704938, процесса:  postgres, команда: postgres: logger                                             , открытые файлы: socket:[2587561]

PID: 704938, процесса:  postgres, команда: postgres: logger                                             , открытые файлы: anon_inode:[signalfd]

PID: 704938, процесса:  postgres, команда: postgres: logger                                             , открытые файлы: pipe:[2585391]

PID: 704938, процесса:  postgres, команда: postgres: logger                                             , открытые файлы: anon_inode:[eventpoll]

PID: 704938, процесса:  postgres, команда: postgres: logger                                             , открытые файлы: pipe:[2585392]

PID: 704938, процесса:  postgres, команда: postgres: logger                                             , открытые файлы: anon_inode:[eventpoll]

**PID: 704938, процесса:  postgres, команда: postgres: logger                                             , открытые файлы: /var/lib/pgpro/1c-17/test17/log/postgresql-2026-08-14_000000.log**


Запуск скрипта

sudo nano /usr/local/bin/proc.sh 

sudo chmod +x /usr/local/bin/proc.sh 



