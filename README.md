Here sample config file for callcenter
1. install docker git
sudo yum install -y yum-utils
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
sudo yum install docker-ce docker-ce-cli containerd.io docker-compose-plugin
sudo systemctl start docker
sudo yum install git

2. ����������� daemon.json � /etc/docker ������������� ������ docker
3. ����������� ���������� � /opt/evocc
   ��� �� ����� ������� � git-hub (!���� ru eng ������ ������)
   ## Get config from GIT
   cd /opt/ && \
	git clone https://github.com/chief1978/callcenter.git && \
	git checkout master && \
	cp -r /opt/callcenter/* /opt
4. ������ ��������� �
   docker-compose - ������ ��� mariadb
   odbc.ini - ������ ��� mariadb
   fastagi/fastagi.properties - �������� ip ������� (��������� ������)
     statusUrl=http://192.168.9.9:8080/api/v1/smev-client/getAppealState
     dealsUrl=http://192.168.9.9:8080/api/v1/smev-client/getAppealsList
5. docker-compose up -d
6. docker exec -i evocc_mysql_1 sh -c 'exec mysql -uroot -p"���_������"' < dump.sql - ��������� ��������� �� asterisk
   ��� ��������� importData.sh (�������������� ������� ������)
7. ������ ������ sip.conf � extension.conf
8. �����, ������ � ip �� mariaDB ����� ��� ������� ������ � ������� (������ ����������), ip ����� ������� ��� ������� ������� ���������� ari (������ ����������)
9. ������������ ����� �� ������ ��� ����� chmod a+rw -R ./asterisk/
10. ���������� � ���� ������� ������������ ������� �� ������ cdr_adaptive_odbc.so � ����������� ��� ���� �� �� �������
*/10 * * * * /usr/bin/python3 /opt/evocc/jobs/status_checks.py

���������� grafana
http://host_ip/stat
user admin
pass eevei7Ae

����� � ��� 4 ������
- mariadb ��
- nginx ��� ������� � ������ ������� ���������
- fastagi ��� java 8 ����������� agi ���������� ��� ��������� �������� ��������
- asterisk

����� ����� ����� ������ yandex 
https://cloud.yandex.ru/services/speechkit
������������ � wav ����� 
https://online-audio-converter.com/ru/
�� ����� ��� ����� ���������� sox �


for song in *.wav; do sox "$song" -e a-law -t RAW -r 8000 -c 1 "${song%.wav}.alaw";done



for song in *.ogg; do ffmpeg -i "$song" -ar 8000 -c 1 -f alaw -acodec pcm_alaw "${song%.ogg}.alaw";done

ffmpeg -i in.ogg -ar 8000 -c 1 -f alaw -acodec pcm_alaw out.alaw