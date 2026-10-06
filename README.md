## 1. IDLE'ı Neden Kısayol İle Çalıştırabiliyoruz?

* **İşletim Sistemi Başlatıcı Mimarisi:** Grafiksel arayüzdeki kısayol simgesi, işletim sisteminin pencere yöneticisi tarafından tanınan bir uygulama başlatıcısıdır (macOS'ta `.app` demeti/paketi, Windows'ta `.lnk` dosya kısayolu). Bu başlatıcı bağımsız bir derlenmiş yazılım yerine, arka planda doğrudan sistemdeki Python yorumlayıcısını çağırır.
* **`idlelib` Modül Entegrasyonu:** IDLE, Python'dan ayrı bağımsız bir program değildir; Python'un standart kütüphanesinde yer alan yerleşik bir modüldür (`idlelib`). Kısayol tetiklendiğinde sistem aslında `python -m idlelib` komutunu arka planda icra eder.
* **Tkinter Arayüz Motoru:** Komut tetiklendiğinde `idlelib` paketi Python'un yerleşik grafik arayüz kütüphanesi olan `tkinter` (Tcl/Tk motoru) üzerinden pencereyi çizdirir ve kullanıcıya etkileşimli kabuğu (Shell) sunar.
* **Ortam Değişkeni (PATH) ve Sembolik Bağlantılar:** Komut satırından ya da Spotlight/Arama üzerinden doğrudan `idle3` yazılarak çalıştırılabilmesinin sebebi ise Python kurulumu sırasında çalıştırılabilir betiklerin sistemin `PATH` ortam değişkenine (veya `/usr/local/bin` gibi global dizinlere sembolik link/symlink olarak) eklenmesidir. İşletim sistemi bu sayede tüm diski aramak yerine doğrudan tanımlı yollara bakarak kısayolu çözer.

* ---

## 2. Python'un Dosya Sistemi Nasıl Biçimlenmiş?

* **Kaynak Kod Katmanı (.py Dosyaları):** Python kaynak kodları, işletim sistemi düzeyinde UTF-8 karakter kodlamasına sahip salt metin (plain text) dosyalarıdır. Derleme öncesi insan tarafından doğrudan okunabilen ham betiklerdir.
* **Ara Derleme Katmanı (Bytecode, .pyc ve `__pycache__`):** Python bir betiği çalıştırdığında ya da bir dosyayı `import` ettiğinde, CPython yorumlayıcısı kaynak kodu doğrudan çalıştırmak yerine önce platformdan bağımsız ara bir makine diline, yani **Bayt Koda (Bytecode)** derler. Bu bayt kodlar, kaynak dosyanın bulunduğu dizinde otomatik açılan `__pycache__/` klasörü altında `.pyc` uzantısıyla (yorumlayıcı sürüm etiketiyle birlikte) önbelleğe alınır. Amaç; kod değişmediği sürece bir sonraki çalıştırmada derleme aşamasını atlayarak başlangıç süresini optimize etmektir.
* **Modül ve Paket (Package) Mimarisi:** Python dosya sisteminde her tekil `.py` dosyası birer **modüldür**. Mantıksal olarak bir arada bulunması gereken modülleri barındıran klasörler ise **paket (package)** yapısını meydana getirir. Bir klasörün paket olarak tanınabilmesi için geleneksel mimaride içerisinde `__init__.py` dosyasının yer alması gerekir.
* **Fiziksel Kurulum ve Dizin Hiyerarşisi:** Python çalışma ortamı işletim sistemi üzerinde katmanlı bir dizin mimarisine sahiptir:
  * **İkili/Çalıştırılabilir Dosyalar (`bin/`):** Çekirdek Python yorumlayıcısı (`python3`), paket yöneticisi (`pip`) ve IDLE başlatıcısı gibi çalıştırılabilir dosyaların barındığı alan.
  * **Standart Kütüphane (`lib/python3.x/`):** Python ile yerleşik gelen temel modül ve paketlerin (örneğin `os`, `sys`, `math`, `idlelib`, `tkinter`) bulunduğu sistem dizini.
  * **Üçüncü Parti Kütüphaneler (`site-packages/`):** Dışarıdan `pip` komutu ile sisteme ya da izole çalışma alanlarına (virtual environment / `.venv/`) yüklenen harici kütüphanelerin tutulduğu dinamik modül havuzu.
