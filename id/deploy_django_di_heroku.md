# Heroku & Django

> **English Version:** [Heroku & Django](../all/heroku_django.md)

Cara Deploy Project Django di Heroku.com

### 1. Buat project Django anda, misalkan seperti contoh dibawah.

```
src/
src/manage.py
src/myproject/
src/myproject/settings.py
```

### 2. Install [Heroku toolbelt](https://toolbelt.heroku.com/)

### 3. Membackup _(Membuat file cadangan untuk `settings.py`)_

> Hal ini sangat penting dilakukan untuk meminimalisir apabila terjadi kesalahan konfigurasi, maupun kebutuhan lainnya.

- Silahkan rename/ganti nama file `settings.py` _(yang default)_ menjadi `old_settings.py`.
- Buat folder baru bernama `settings/` yang sejajar dengan file `old_settings.py` yang telah anda buat.

  - Didalam folder `settings/` terdapat 3 file berupa:
    - `__init__.py`
    - `local.py`
    - `production.py`

  - Dan kurang lebih project anda nantinya akan seperti ini:

		```
		src/
		src/manage.py
		src/myproject/
		src/myproject/old_settings.py
		src/myproject/settings/__init__.py
		src/myproject/settings/local.py
		src/myproject/settings/production.py
		```

### 4. Menambahkan script didalam file `settings/__init__.py`:

Didalam file `__init__.py` tambahkan beberapa kode untuk mengimport'nya
dari file `local.py` atau `production.py`

```python
try:
  from .local import *
except:
  pass

from .production import *
```

- Copy/salin seluruh kode yang ada didalam file `old_settings.py` ke `local.py`.
- Copy/salin seluruh kode yang ada didalam file `old_settings.py` ke `production.py`.

### 5. Mengawali _(initialize)_ repository Git anda dan menambahkan file `.gitignore`:

Pada project root anda, silahkan ikuti beberapa perintah berikut:
- mengawalinya dengan perintah `$ git init` _(untuk menggenerate file-file git kedalam folder project anda)_.
- buat file bernama `.gitignore` _(file ini digunakan untuk mengecualikan file/folder yang tidak di ikutsertakan ketika anda menguploadnya ke heroku)_

  > File `.gitingore` ini sejajar dengan file `manage.py`

  Didalam file `.gitingore`, tambahkan beberapa baris nama file/folder didalamnya:

  ```
  #.gitignore
	yourproject/settings/local.py
  ```

  > Dalam kasus diatas berarti yang tidak ter-unggah nantinya adalah file `yourproject/settings/local.py`.
  > Untuk lebih lengkapnya bisa melihat dokumentasi dari `.gitignore`: https://git-scm.com/docs/gitignore

### 6. Menambahkan file `requirements.txt`:

Didalam file `requirements.txt` ini merupakan beberapa packages yang anda butuhkan:

```
dj-database-url==0.4.0
Django==1.9.7
gunicorn==19.4.5
psycopg2==2.6.1
whitenoise==2.0.6
```

> Anda mungkin perlu memeriksa versi masing-masing dari tool/package, karena hal tersebut adalah penting.

### 7. Menambahkan file `Procfile`:

File `Procfile` ini tanpa diikuti extensi apapun _(seperti halnya menggunakan `.` titik)_ maupun yang lainnya. Taruh file ini sejajar dengan file `manage.py`, dan masukkan perintah berikut didalam filenya:

```
web: gunicorn yourproject.wsgi
```

### 8. Mengganti kode didalam file `production.py`

*Adapun file `local.py` tidak dirubah, file tersebut digunakan untuk keperluan development saja.*

```
DEBUG = FALSE
import dj_database_url
DATABASES = {
  'default': dj_database_url.config()
}

SECURE_PROXY_SSL_HEADER = ('HTTP_X_FORWARDED_PROTO', 'https')
```

### 9. Mengecek dan menambahkan project anda ke `.git`

```
$ git status -u                               # file `local.py` tidak muncul disini.
$ git add --all                               # atau juga bisa dengan menggunakan `$ git add .`
$ git commit -m "Prepare for production"      # memberikan komentar pada commit.
```

10. Membuat project di Heroku beserta Databasenya.

```
$ heroku create <yourprojectname>
$ heroku addons:create heroku-postgresql:hobby-dev     # menambahkan database
$ heroku config:set DISABLE_COLLECTSTATIC=1            # mematikan perintah untuk mengoleksi file-file static.
```

> **Catatan:** `<yourprojectname>` harus diubah dengan nama project anda.

### 11. Deploy ke Heroku

```
$ git push heroku master
```

### 12. Apakah ada perubahan lain? _(ya, tentu ada)_. Pastikan Anda melakukan hal-hal berikut:

  1. Rubah didalam file `requirements.txt` sesuai dengan kebutuhan.
  2. Rubah didalam file `production.py` sesuai dengan kebutuhan _(Dan bandingkan kembali dengan file `local.py`)_
  3. Commit seluruh perubahan:

    ```
    git add --all
    git commit -m "Update changes"
    ```

  4. Push ke Heroku:

    ```
    git push heroku master
    ```

## Perintah yang Umum digunakan

* `$ heroku restart`: untuk beberapa alasan yang mungkin anda butuh untuk merestartnya.

* `$ heroku run bash`: untuk menjalankan bash Linux didalam app anda.

* `$ heroku run python manage.py shell`: menjalankan Python shell di Heroku

* `$ heroku run python manage.py`: Anda juga diizinkan untuk menggunakan perintah tambahan seperti halnya `heroku run python manage.py migate`

* `$ heroku logs`: untuk mengecek logs dari app anda, hal ini biasanya untuk mengecek error pada aplikasi atau `Application Errors` pada saat men-deploy situs/app anda.

* `$ heroku open`: membukan situs project anda.

* `$ heroku ps:scale web=X worker=Y`:
  - ganti `X` dengan nomor dari `web dynos` dari app anda; semakin tinggi jumlahnya semakin besar kinerja dan biayanya.
  - untuk lebih lanjut, silahkan cek pada [dokumentasinya](https://devcenter.heroku.com/articles/scaling).

----------------------

Subscribe on our [YouTube Channel](http://joincfe.com/youtube) and join us for more in-depth tutorials on [Django development](http://joincfe.com/enroll).

Cheers!

## Organized by CodingForEntrepreneurs

> Last edited by: Agus Makmun - _[github.com/agusmakmun](github.com/agusmakmun)_
