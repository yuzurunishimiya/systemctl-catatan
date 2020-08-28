# systemctl-catatan

## how to run python program in systemd

1. Misal kita punya file python + env
    ```
    ├── app.py
    ├── env

    ```
2. directory diatas dilihat dulu pake command `$ pwd`, oke misal di /home/project/
3. kita pindah directory dulu ke sini
    ```
    $ cd /etc/systemd/system
    $ sudo nano python-app.service (terserah namanya mau apa)
    ```
4. buat service di file tadi dengan script berikut:
    ```
    [Unit]
    Description=test-running-gunicorn
    After=network.target

    [Service]
    User=sam
    WorkingDirectory=/home/project
    Environment= /home/project/env/bin
    ExecStart=/home/project/env/bin/gunicorn --workers=4 --bind 0.0.0.0:5000 app:app
    Restart=on-failure

    [Install]
    WantedBy=multi-user.target

    ```
    note: silahkan disesuaikan workingDirectory, Environment (virtualenv kamu dimana), dan pake gunicorn
    oh ya, jangan lupa install gunicorn, pip3 install gunicorn. --workers=4 aja, --bind 0.0.0.0:5000 port nya disesuaikan aja
    note lagi:
    kalo kamu pake wsgi.py, berarti nanti gini, --bind 0.0.0.0:5000 wsgi:app
    biasanya deploy itu di wsgi.py, isi wsgi.py:
    
        ```
        from app import app
        app.run()
        ```
    
    tapi gak pun gamasalah

5. Ohya diatas itu khusus flask, pake gunicorn, kalo cmn file biasa dan bkn flask gini
    ```
    ...
    execStart=/home/project/env/bin/python3 namafile.py
    ...
    ```

6. Jangan lupa start service
    ```
    $ sudo systemctl start namafileservice.service // start service
    $ sudo systemctl status namafileservice.service // cek status servicenya, kalo misal failed berarti ada yang salah
    $ sudo journalctl -u namafileservice.service // cek log nya kalo ada error cek disini
    $ sudo systemctl daemon-reload // kalo ada perubahan file, direload dlu 
    $ sudo systemcl stop namafileservice.service
    ```
7. Jangan lupa di test, dicek jalan atau engga programnya
