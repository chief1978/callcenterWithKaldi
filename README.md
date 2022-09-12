Here sample config file for callcenter
1. install docker git
sudo yum install -y yum-utils
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
sudo systemctl start docker
sudo yum install git

2. скопировать daemon.json в /etc/docker перезапустить сервис docker
3. скопировать содержимое в /opt/evocc
   так же можно забрать с git-hub (!нету ru eng файлов астера)
   ## Get config from GIT
   cd /opt/ && \
	git clone https://github.com/chief1978/callcenter.git && \
	git checkout master && \
	cp -r /opt/callcenter/* /opt
4. внести изменени€ в
   docker-compose - пароль дл€ mariadb
   odbc.ini - пароль дл€ mariadb
   fastagi/fastagi.properties - помен€ть ip сервера (подскажут админы)
     statusUrl=http://192.168.9.9:8080/api/v1/smev-client/getAppealState
     dealsUrl=http://192.168.9.9:8080/api/v1/smev-client/getAppealsList
5. docker-compose up -d
6. docker exec -i evocc_mysql_1 sh -c 'exec mysql -uroot -p"тут_пароль"' < dump.sql - выгружаем структуру Ѕƒ asterisk
   или запускаем importData.sh (предварительно помен€в пароль)
7. дальше мен€ем sip.conf и extension.conf
8. логин, пароль и ip от mariaDB нужны дл€ импорта данных в пентаху (Ёльнар занимаетс€), ip нужен админам дл€ запуска сервиса интеграции ari (админы занимаютс€)
9. предоставить права на запись дл€ папки chmod a+rw -R ./asterisk/
10. добавление в крон скрипта проверяющего запущен ли модуль cdr_adaptive_odbc.so и запускающий его если он не запущен
*/10 * * * * /usr/bin/python3 /opt/evocc/jobs/status_checks.py

Мониторинг grafana
http://host_ip/stat
user admin
pass eevei7Ae

итого у нас 4 докера
- mariadb бд
- nginx дл€ доступа к файлам записей астериска
- fastagi дл€ java 8 запускаетс€ agi приложение дл€ обработки статусов запросов
- asterisk

«вуки пишем через сервис yandex 
https://cloud.yandex.ru/services/speechkit
конвертируем в wav через 
https://online-audio-converter.com/ru/
на хосте уже можно установить sox и


for song in *.wav; do sox "$song" -e a-law -t RAW -r 8000 -c 1 "${song%.wav}.alaw";done



for song in *.ogg; do ffmpeg -i "$song" -ar 8000 -c 1 -f alaw -acodec pcm_alaw "${song%.ogg}.alaw";done

ffmpeg -i in.ogg -ar 8000 -c 1 -f alaw -acodec pcm_alaw out.alaw