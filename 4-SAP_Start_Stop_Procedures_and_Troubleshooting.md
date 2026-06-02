1. SAP Başlatma ve Durdurma Prosedürü (Start / Stop)
SAP sistemlerinin sorunsuz çalışması, veri kaybı yaşanmaması ve performans problemi oluşmaması için sunucuların kesin bir sıra ve prosedüre göre yönetilmesi gerekir.

A) Başlatma (Start) Sırası
Sistem sıfırdan ayağa kaldırılırken aşağıdan yukarıya doğru şu sıra takip edilir:

İşletim Sistemi ve Altyapı Kontrolü: Sunucunun CPU, RAM (top, free -m) ve Disk alanı (df -h) kaynaklarının yeterli ve erişilebilir olduğu doğrulanır.

Veri Tabanı (Database - DB): SAP uygulama sunucularından önce mutlaka veri tabanı (HANA, Sybase veya Oracle) ayağa kaldırılmalıdır. DB açılmadan uygulama sunucusu açılamaz.

Merkezi Sunucu (Central Instance / ASCS): Message Server ve Enqueue Server bileşenlerini barındıran merkezi yapı başlatılır.

Uygulama Sunucuları (Dialogue Instance / PAS - AAS): Kullanıcıların doğrudan bağlanacağı diyalog süreçlerini yöneten sunucular en son ayağa kaldırılır.

B) Durdurma (Stop) Sırası
Sistem kapatılırken başlatma sırasının tam tersi (yukarıdan aşağıya) uygulanır:

Dialogue Instance (Uygulama Sunucuları) kapatılır.

Central Instance / ASCS (Merkezi Sunucu) kapatılır.

Database (Veri Tabanı) en son kapatılır.

C) Operasyonel Yönetim Alternatifleri
Derste bu işlemlerin 3 farklı yöntemle yapılabileceği gösterilmiştir:

SAP Management Console (SAP MC): Windows tabanlı görsel arayüz üzerinden sağ tıklayıp Start/Stop diyerek kolayca yönetilir.

İşletim Sistemi Komutları (Linux - sidadm): Komut satırında startsap all / stopsap all veya daha modern bir araç olan sapcontrol -parameter [Command] komutları kullanılır.

Windows Servisleri (services.msc): Windows tabanlı SAP sistemlerinde ise arka plandaki Windows servisleri tetiklenerek süreç yürütülür.

2. Güvenli Sistem Kapatma (Basis Disiplini)
Canlı veya test ortamındaki bir SAP sistemi doğrudan "küt diye" kapatılmaz. Kapatma komutu verilmeden önce şu kontrollerin yapılması şarttır:

SM37 (Arka Plan İşleri): O esnada çalışan veya çalışmak üzere olan kritik toplu işlerin (Batch Jobs) olup olmadığı denetlenir.

SM13 (Güncelleme Süreçleri): Veri tabanına veri yazan anlık güncelleme (Update) süreçleri kontrol edilir; sistem yarım kalmış bir maaş veya fatura işlemi varken kapatılmamalıdır.

SM50 (Aktif Süreçler): Sistemde o an aktif iş yükü üreten kullanıcı süreçleri incelenir.

SM02 (Sistem Mesajı): Her şey uygunsa, kullanıcılara ekranlarında anlık görünecek şekilde "Sistem 10 dakika içinde kapatılacaktır, lütfen verilerinizi kaydedip çıkış yapın" tarzında bir bilgi mesajı yayınlanır.

3. Otomasyon ve Kabuk Programlama (Shell Scripting & Cron)
Sistemlerin her gün manuel açılıp kapatılması yerine bu süreçlerin otomatize edilmesi işlenmiştir.

Linux Cron Yapısı (crontap -e): Belirli zamanlanmış görevleri (Örn: her gece 23:00'te geçici logları sil, sunucuyu kapat vb.) çalıştırmak için Linux altyapısındaki cron mekanizması kullanılır.

Kabuk Programlama (Shell Script - .sh): Basis danışmanları, arka arkaya çalışması gereken SAP komutlarını (Örn: Önce DB başlat, 5 dk bekle, sonra uygulamayı başlat) bir metin dosyasına yazıp .sh uzantısıyla çalıştırılabilir scriptler haline getirirler. Windows tarafında bunun karşılığı PowerShell (.ps1) veya Batch (.bat) dosyalarıdır.

4. Sistem Hatalarını Çözme (Troubleshooting & Logs)
Sistem başlatılırken trafik lambası simgesinin sarıdan yeşile dönmediği veya doğrudan kırmızıya düştüğü panik anlarında Basis danışmanının bakması gereken yerler ve hata tipleri şunlardır:

A) En Sık Karşılaşılan Hata Tipleri
Veri Tabanı Erişim Problemi (R3trans -d): Uygulama sunucusunun veri tabanına bağlanamaması durumudur. R3trans -d komutu çalıştırıldığında 0000 yerine 0012 gibi olumsuz bir kod (Return Code) dönüyorsa DB kapalıdır veya ağ (network) erişim sorunu vardır.

Dosya Sistemi ve Mant Alanı Doluluğu (df -h): Disk alanının tamamen dolması nedeniyle yeni log dosyası yazılamıyorsa sistem açılışı bloke olur.

Paylaşılan Bellek (Shared Memory) Hataları: İşletim sistemindeki RAM parametrelerinin SAP'nin ihtiyaç duyduğu ortak bellek alanını oluşturmaya yetmemesidir.

Kernel Uyuşmazlıkları: sap_exe, sap_db gibi çekirdek paketlerin versiyonlarının birbiriyle eşleşmemesinden kaynaklanan açılış hatalarıdır.

B) Log Dosyalarının Okunması (AL11)
Sistem içinden veya işletim sistemi seviyesindeki /usr/sap/[SID]/[Instance]/work dizininden hatalar analiz edilir.

AL11 T-Code: SAP içerisindeki log dizinlerine erişim sağlayan işlem kodudur.

dev_disp: Dispatcher (Yük dağıtıcı) logları.

dev_w0, dev_w1: İlgili iş süreçlerinin (Work Processes) detaylı hata logları.

stderr / boot.log: İşletim sistemi ve açılış (boot) esnasında yaşanan donanımsal/sistemsel hataları gösterir.

SAP Note Çözümü: Loglarda yakalanan spesifik bir hata kodu veya not numarası, resmi support.sap.com portalında aratılarak SAP tarafından önerilen çözüm adımları (SAP Notes) sisteme uygulanır.
