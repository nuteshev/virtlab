# 1. Определить алгоритм с наилучшим сжатием
```
[root@localhost ~]# /sbin/modprobe zfs
[root@localhost ~]# zpool create tank  sdd sde sdf sdg
[root@localhost ~]# for fs in gzip-9 lz4 lzjb zle; do zfs create tank/$fs; zfs set compression=$fs tank/$fs; done
[root@localhost ~]# wget https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.9.8.tar.xz
--2020-11-17 23:34:27--  https://cdn.kernel.org/pub/linux/kernel/v5.x/linux-5.9.8.tar.xz
Resolving cdn.kernel.org (cdn.kernel.org)... 151.101.113.176
Connecting to cdn.kernel.org (cdn.kernel.org)|151.101.113.176|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 115540828 (110M) [application/x-xz]
Saving to: ‘linux-5.9.8.tar.xz’

linux-5.9.8.tar.xz                      100%[============================================================================>] 110.19M  6.30MB/s    in 16s

2020-11-17 23:34:44 (6.74 MB/s) - ‘linux-5.9.8.tar.xz’ saved [115540828/115540828]
[root@localhost ~]# tar -xf linux-5.9.8.tar.xz
[root@localhost ~]# for fs in gzip-9 lz4 lzjb zle; do cp -r linux-5.9.8 /tank/$fs; done
[root@localhost ~]# zfs list
NAME          USED  AVAIL     REFER  MOUNTPOINT
tank         1.99G  1.63G       28K  /tank
tank/gzip-9   253M  1.63G      253M  /tank/gzip-9
tank/lz4      383M  1.63G      383M  /tank/lz4
tank/lzjb     439M  1.63G      439M  /tank/lzjb
tank/zle      964M  1.63G      964M  /tank/zle
```
Отсюда следует, что самым лучшим алгоритмом является gzip-9, дальше идут lz4, lzjb, zle. 

# 2. Определить настройки pool’a
```
[root@localhost ~]# ./gdown.pl  https://drive.google.com/file/d/1KRBNW33QWqbvbVHa3hLJivOAt60yukkg/view
Cannot open cookies file ‘gdown.cookie.temp’: No such file or directory
--2020-11-18 01:16:37--  https://docs.google.com/uc?id=1KRBNW33QWqbvbVHa3hLJivOAt60yukkg&export=download
Resolving docs.google.com (docs.google.com)... 64.233.164.194
Connecting to docs.google.com (docs.google.com)|64.233.164.194|:443... connected.
HTTP request sent, awaiting response... 302 Moved Temporarily
Location: https://doc-0c-bo-docs.googleusercontent.com/docs/securesc/ha0ro937gcuc7l7deffksulhg5h7mbp1/ijas688pbvk34cpmqg2s8ara41kur3vs/1605662175000/16189157874053420687/*/1KRBNW33QWqbvbVHa3hLJivOAt60yukkg?e=download [following]
Warning: wildcards not supported in HTTP.
--2020-11-18 01:16:46--  https://doc-0c-bo-docs.googleusercontent.com/docs/securesc/ha0ro937gcuc7l7deffksulhg5h7mbp1/ijas688pbvk34cpmqg2s8ara41kur3vs/1605662175000/16189157874053420687/*/1KRBNW33QWqbvbVHa3hLJivOAt60yukkg?e=download
Resolving doc-0c-bo-docs.googleusercontent.com (doc-0c-bo-docs.googleusercontent.com)... 64.233.162.132
Connecting to doc-0c-bo-docs.googleusercontent.com (doc-0c-bo-docs.googleusercontent.com)|64.233.162.132|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [application/x-gzip]
Saving to: ‘gdown.20201118011637.603571386615844’

     0K ......                              7.35M=0.9s

2020-11-18 01:16:47 (7.35 MB/s) - ‘gdown.20201118011637.603571386615844’ saved [7275140]

[root@localhost ~]# mv gdown.20201118011637.603571386615844 zfs_task1.tar.gz
[root@localhost ~]# tar -xzf zfs_task1.tar.gz
[root@localhost ~]# zpool import -d zpoolexport/
   pool: otus
     id: 6554193320433390805
  state: ONLINE
 action: The pool can be imported using its name or numeric identifier.
 config:

        otus                         ONLINE
          mirror-0                   ONLINE
            /root/zpoolexport/filea  ONLINE
            /root/zpoolexport/fileb  ONLINE
[root@localhost ~]# zpool import -d zpoolexport/ otus
[root@localhost ~]# zfs get compression,recordsize,checksum otus
NAME  PROPERTY     VALUE      SOURCE
otus  compression  zle        local
otus  recordsize   128K       local
otus  checksum     sha256     local
[root@localhost ~]# zpool get size otus
NAME  PROPERTY  VALUE  SOURCE
otus  size      480M   -
[root@localhost ~]# zpool status otus
  pool: otus
 state: ONLINE
  scan: none requested
config:

        NAME                         STATE     READ WRITE CKSUM
        otus                         ONLINE       0     0     0
          mirror-0                   ONLINE       0     0     0
            /root/zpoolexport/filea  ONLINE       0     0     0
            /root/zpoolexport/fileb  ONLINE       0     0     0

errors: No known data errors
```
Отсюда мы заключаем, что типом пула является mirror-0, размер хранилища - 480 МБайт, значение recorsize - 128 КБайт, контрольная сумма - SHA-256, используется сжатие zle, значение
recordsize - 128 КБайт. 

# 3. Найти сообщение от преподавателей
```
[root@localhost ~]# ./gdown.pl https://drive.google.com/file/d/1gH8gCL9y7Nd5Ti3IRmplZPF1XjzxeRAG/view?usp=sharing
Cannot open cookies file ‘gdown.cookie.temp’: No such file or directory
--2020-11-18 01:37:00--  https://docs.google.com/uc?id=1gH8gCL9y7Nd5Ti3IRmplZPF1XjzxeRAG&export=download
Resolving docs.google.com (docs.google.com)... 64.233.164.194
Connecting to docs.google.com (docs.google.com)|64.233.164.194|:443... connected.
HTTP request sent, awaiting response... 302 Moved Temporarily
Location: https://doc-00-bo-docs.googleusercontent.com/docs/securesc/ha0ro937gcuc7l7deffksulhg5h7mbp1/3q8v4rnqkrker2t8f3lbl7k22s3mp7va/1605663375000/16189157874053420687/*/1gH8gCL9y7Nd5Ti3IRmplZPF1XjzxeRAG?e=download [following]
Warning: wildcards not supported in HTTP.
--2020-11-18 01:37:01--  https://doc-00-bo-docs.googleusercontent.com/docs/securesc/ha0ro937gcuc7l7deffksulhg5h7mbp1/3q8v4rnqkrker2t8f3lbl7k22s3mp7va/1605663375000/16189157874053420687/*/1gH8gCL9y7Nd5Ti3IRmplZPF1XjzxeRAG?e=download
Resolving doc-00-bo-docs.googleusercontent.com (doc-00-bo-docs.googleusercontent.com)... 64.233.162.132
Connecting to doc-00-bo-docs.googleusercontent.com (doc-00-bo-docs.googleusercontent.com)|64.233.162.132|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: unspecified [application/octet-stream]
Saving to: ‘gdown.20201118013700.523031302568601’

     0K .....                               8.13M=0.6s

2020-11-18 01:37:02 (8.13 MB/s) - ‘gdown.20201118013700.523031302568601’ saved [5432736]

[root@localhost ~]# mv gdown.20201118013700.523031302568601 otus_task2.file
[root@localhost ~]# zfs receive otus/storage@task2  < otus_task2.file
[root@localhost ~]# cd /otus/storage/
[root@localhost storage]# find . | grep secret_message
./task1/file_mess/secret_message
[root@localhost storage]# cat task1/file_mess/secret_message
https://github.com/sindresorhus/awesome
```
Зашифрованным сообщением является адрес https://github.com/sindresorhus/awesome. 