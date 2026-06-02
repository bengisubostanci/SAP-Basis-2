1. SAP HANA Nedir ve Nasıl Ortaya Çıktı?
Tarihsel Dönüşüm: SAP sistemleri 2015 yılına kadar Oracle, DB2, MSSQL ve Sybase gibi geleneksel veri tabanları üzerinde çalışıyordu. Ancak 2016 yılında duyurulan S/4HANA ile birlikte SAP, sadece kendi geliştirdiği HANA veri tabanını destekleyeceğini açıkladı.

Teknolojik Milat: Gelişen 64-bit işlemci teknolojileri, çoklu iş parçacığı (Multi-threading) desteği ve RAM/donanım maliyetlerinin ucuzlaması, tebraytlar seviyesindeki verinin doğrudan RAM üzerinde tutulabilmesini sağladı.

In-Memory Teknolojisi: Geleneksel veri tabanları veriyi yavaş dönen mekanik disklerde (Hard Drive) ararken, HANA tüm veriyi doğrudan RAM (bellek) üzerinde tutar. Bu durum, disk okuma/yazma darboğazlarını ortadan kaldırarak veri işleme hızını ve sorgu performansını olağanüstü düzeyde artırır.

Tek Platform Yapısı: HANA; operasyonel işlemler (OLTP) ile analitik raporlama ve stratejik karar süreçlerini (OLAP) tek bir veri tabanı platformunda birleştirerek gerçek zamanlı analiz imkanı sunar.

2. Satır Tabanlı (Row-Store) ve Kolon Tabanlı (Column-Based) Depolama
Geleneksel veri tabanları veriyi satır satır saklarken, HANA verileri dikey olarak kolon (sütun) bazlı saklar.

Depolama Türü,Avantajı,Dezavantajı,En Uygun Olduğu Senaryo
Row-Store (Satır),"Veri yazma, ekleme ve güncelleme işlemleri çok hızlıdır.",Büyük veri kütlelerinde analitik raporlama ve filtreleme yaparken sistemi yorur.,Anlık veri girişi yapılan operasyonel süreçler.
Column-Store (Kolon),"Belirli sütunlarda toplulaştırma, filtreleme ve hesaplamaları inanılmaz hızlı yapar. Veriyi 7 kata kadar sıkıştırarak bellek tasarrufu sağlar.",Yapısı gereği veri yazma hızı satır tabanlıya göre bir tık daha yavaştır.,Milyonlarca kayıt içeren karmaşık analitik ve stratejik sorgular.

Sıkıştırma Mantığı (Dictionary Encoding): Kolon tabanlı yapıda, tekrarlayan uzun metinler (Örn: "United Kingdom", "Germany") yerine 0, 1, 2 gibi küçük sayısal kodlar atanarak bir konversiyon tablosunda (Conversion/Dictionary Table) tutulur. Sistem veriyi ekrana getirirken bu kodları tekrar metne dönüştürür; böylece RAM'de kaplanan alan ciddi oranda azalır.

3. Veri Tabanı Katmanları ve Mantıksal Objeler
SAP sistem mimarisi fiziksel, mantıksal ve uygulama kullanıcı katmanlarından oluşur.

Şema (Schema): Veri tabanı içindeki tabloların, prosedürlerin, indekslerin ve tüm objelerin mantıksal olarak bir çatı altında toplandığı yapıdır. Her şema aynı zamanda bir kullanıcı (User) gibi çalışır. S/4HANA ile birlikte bu ana şema ismi SAPANA$DB (veya eski sürümlerde SAPABAP1) olarak adlandırılır.

Görünüm (View): Kendisi fiziksel bir tablo olmayıp, veri çekmeyi hızlandırmak amacıyla birden fazla tablonun alanlarını birleştiren (Join) mantıksal bir yansımadır.

Stored Procedure: Veri tabanı üzerinde çalışan ve parametre alarak karmaşık SQL komut zincirlerini/scriptlerini otomatik olarak tetikleyen saklı metotlardır (Örn: Belirli bir personel ID'sine göre maaş güncelleme işlemi).

İndeks (Index): Büyük tablolarda aranan veriye en hızlı şekilde ulaşmayı sağlayan mantıksal erişim metodudur.

Bakım Operasyonları: Veri tabanında alan kazanmak ve sorguları optimize etmek için Shrink (boşlukları daraltıp bir araya getirme), Reorganization (dağınık verileri ardışık alanlarda toplama) ve Partitioning (2 milyar kayıt limitini aşan devasa tabloları parçalara bölme) operasyonları yürütülür.

4. HANA Veri Tutarlılığı ve Kalıcı Bellek (Persistence)
"HANA madem RAM üzerinde çalışıyor, elektrik kesilirse veya sunucu çökerse veriler ne oluyor?" sorusunun cevabı HANA'nın Kalıcı Bellek (Persistence Layer) mimarisidir.

[Kullanıcı İşlemi (Commit)] 
         │
         ├──> 1. RAM (Bellek İçi Güncelleme - Anlık)
         └──> 2. Disk: Redo Log Files (Real-Time Senkron Yazma)
         
[Her 5 Dakikada Bir (Savepoint)] 
         │
         └──> RAM'deki Güncel Veri ──> Disk: Data Files (Kalıcı Depolama)

Real-Time Güncelleme: Sunucuda bir işlem onaylandığı (Commit) anda bu veri eş zamanlı olarak hem RAM'e işlenir hem de fiziksel diskteki Redo Log dosyalarına satır satır yazılır.

Savepoint Mekanizması: Sistem, varsayılan olarak her 5 dakikada bir bir Savepoint (Kayıt Noktası) tetikler. RAM'deki o anki güncel durum toplu olarak diskteki Data Files alanına kalıcı olarak yazılır.

Çökme Sonrası Geri Yükleme (Crash Recovery): Elektrik kesintisi sonrası sistem yeniden açılırken, en son başarılı Savepoint dosyasından veriler RAM'e yüklenir; ardından en son Savepoint ile çökme anı arasında oluşan Redo Log kayıtları işletilerek veri tabanı sıfır kayıpla en güncel haline geri döndürülür.


5. SAP HANA Çekirdek Servisleri (Engines)
HANA, arka planda her biri bağımsız birer sunucu gibi çalışan farklı motorlardan (Engines/Processes) oluşur:

Name Server: Dağıtık (Distributed) mimarilerde sistem topolojisini ve hangi verinin hangi sunucuda durduğu bilgisini yöneten merkezi servistir.

Index Server: En kritik motorudur. Gerçek verilerin ve tabloların saklandığı, SQL sorgularının işlendiği ve SAPANA$DB şemasının yaşadığı ana servistir.

Preprocessor Server: Metin içeren verilerin ön işlemlerini ve dilsel analizlerini üstlenir.

Compile Server: Stored procedure'lerin ve SQLScript kodlarının derlenmesini sağlar.

Statistics Server: Sistemin RAM, CPU gibi kaynak kullanımlarını ve performans geçmişini izleyen analitik motordur.

6. Suse Linux Üzerine SAP HANA Kurulum Adımları
Derste, donanım üreticileri tarafından sertifike edilmiş (Certified Appliances) bir mimari simüle edilerek, Suse Linux Enterprise Server (SLES) for SAP Applications işletim sistemi üzerine manuel kurulum adımları gösterilmiştir.

A) Disk Alanlarının Bölümlenmesi (Yast2 Mimarisi)
Linux kurulum ekranında SAP standartlarına uygun olarak 4 temel disk alanı XFS dosya formatıyla yapılandırılır:

/usr/sap (30-40 GB): Kurulum ve kernel dosyaları için.

/hana/shared: Kurulum dosyaları ve ortak paylaşılan alan için.

/hana/data (RAM boyutunun en az 3 katı): Kalıcı veri dosyaları için.

/hana/log (RAM boyutunun en az 1-1.5 katı): Redo Log dosyaları için.

B) İşletim Sistemi Hazırlığı ve Bağımlılıklar
Kurulum öncesinde sunucuya internet üzerinden zipper install komutuyla SAP'nin ihtiyaç duyduğu libgcc_s1, libstdc++6 ve insserv-compat gibi temel kütüphane paketleri yüklenir.

Ağ yapılandırmasında statik IP, alt ağ maskesi ve internet çıkış kapısı (Gateway) tanımlanır; ardından /etc/hosts dosyasına sunucu IP'si ve hostname eşleşmesi girilir.

C) Kurulumun Tetiklenmesi (hdblcm)
Kurulum medyası sunucuya aktarıldıktan sonra HDB_LCM_LINUX_X86_64 dizinine geçilir.

Görsel arayüz için hdblcmgui veya komut satırından doğrudan ./hdblcm (HANA Database Lifecycle Manager) scripti çalıştırılır.

Sihirbaz üzerinde SID (Örn: HDP), Instance Number (Örn: 00), şifreler ve disk yolları belirtilerek kurulum tamamlanır; sistem otomatik olarak sidadm kullanıcısı ile ayağa kalkar.

D) Yönetim ve Bağlantı (SAP HANA Studio)
Kurulum tamamlandıktan sonra, Windows istemciye kurulan SAP HANA Studio (veya modern web tabanlı SAP HANA Cockpit) aracı açılır.

Sunucu IP'si, Instance Numarası ve SYSTEM kullanıcısı bilgileri girilerek veri tabanına bağlanılır; sistemin RAM/CPU yükü, yedekleme (Backup) durumu ve servislerin sağlığı bu panel üzerinden izlenir.













