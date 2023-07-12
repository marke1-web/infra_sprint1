# Kittygram

Kittygram � ���������� ���� ��� ������ ������������ ������� ��������. ��� ��������� ������� ������, ������� ������� �� ������-���������� �� Django � ��������-���������� �� React.

## ����������
- Python
- frontend - React
- backend - Django
- nginx
- gunicorn

## ���������
<i>��� ������� ������� ��� Linux</i><br>
<br>1. ������������ �����������
```
git@github.com:marke1-web/infra_sprint1.git
```

<br>2. ������� � ������������ ����������� ���������
```
python3 -m venv venv
```
```
source venv/bin/activate
```

<br>3. ���������� ����������� ��� Python
```
pip install -r requirements.txt
```

<br>4. ������� � ����� backend � ��������� ��������
```
cd infra_sprint1/backend/
```
```
python manage.py migrate
```

<br>5. ������� ����������
```
python manage.py createsuperuser
```

<br>6. � ����� infra_sprint1/backend/kittygram_backend/settings.py � ���������� ALLOWED_HOSTS �������� ��������� ������, � ����� �������� ��� ��� ������� IP (���� ����)
```
ALLOWED_HOSTS = ['xxx.xxx.xxx.xxx', '127.0.0.1', 'localhost', 'xxx.xxx.xxx.xxx']
```

<br>7. � ���� �� ����� �������� �������� ���������� DEBUG � True �� False
```
DEBUG = False
```


<br>8. ������� � infra_sprint1/frontend � ���������� ����������� ��� frontend-����������
```
npm i
```

<br>9. ��� backend-���������� ���������� WSGI-������ gunicorn
```
pip install gunicorn
```

<br>10. ������� ���� ������������ ��� ����������� WSGi-�������
```
sudo nano /etc/systemd/system/gunicorn_��������_�������.service
```

<br>11. ������ � ���� ��������� ���������
```
[Unit]
Description=gunicorn daemon
After=network.target

[Service]
User=<���-������������-�-�������>
WorkingDirectory=/home/<���-������������>/infra_sprint1/backend/
ExecStart=/home/<���-������������>/infra_sprint1/venv/bin/gunicorn --bind 0.0.0.0:8080 kittygram_backend.wsgi

[Install]
WantedBy=multi-user.target
```

<br>12. ��������� ��������� ������ � ������ � � ����������
```
sudo systemctl start gunicorn
```
```
sudo systemctl enable gunicorn
```

<br>13. ���������� ���-������ � ��������� ���
```
sudo apt install nginx -y
```
```
sudo systemctl start nginx
```

<br>14. ������� � infra_sprint1/frontend/, �������������� frontend-���������� � ���������� ��� � ��������� ���������� ���-�������
```
npm run build
```
```
sudo cp -r <���_������������>/infra_sprint1/frontend/build/. /var/www/kittygram/
```

<br>15. ������� � infra_sprint1/backend/, ������� ������� ��� ������� Django � ���������� ��� � ��������� ���������� ���-�������
```
infra_sprint1/backend/kittygram_backend/settings.py �������� �������� ���������� STATIC_URL � �������� ����� STATIC_ROOT

STATIC_URL = '/static_backend/'
STATIC_ROOT = BASE_DIR / 'static_backend'
```
```
python manage.py collectstatic
```
```
sudo cp -r infra_sprint1/backend/static_backend/ /var/www/kittygram/
```

<br>16. ������� ����� ��� ����������� � ���������� ���-�������, �������� ����� �������
```
cd /var/www/kittygram/
```
```
sudo mkdir media
```
```
sudo chown -R <���_������������> /var/www/kittygram/media/
```

<br>17. ������� � ���� ������������ ���-������� � �������� ��� ��������� �� ���������
```
sudo nano /etc/nginx/sites-enabled/default
```
```
server {

    server_name server_name <���������-IP-�����> <��������-���>;

    location /api/ {
        proxy_pass http://127.0.0.1:8080;
    }

    location /admin/ {
        proxy_pass http://127.0.0.1:8080;
    }

    location /media/ {
        alias /var/www/kittygram/media/;
    }

    location / {
    root    /var/www/kittygram;
    index   index.html index.htm;
    try_files  $uri /index.html;
    }

}
```

<br>18. ��������� ���� ������������ ���-�������, ������������� ��� ������ � ������ ������
```
sudo nginx -t
```
```
sudo systemctl reload nginx
```

<br>19. �������� SSL-���������� ��� ������ ��������� ����� ��� ������ certbot
```
sudo apt install snapd
```
```
sudo snap install core; sudo snap refresh core
```
```
sudo snap install --classic certbot
```
```
sudo ln -s /snap/bin/certbot /usr/bin/certbot
```
```
sudo certbot --nginx
```
