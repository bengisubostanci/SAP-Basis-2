1. Veri Tabanı Yedekleme ve Felaket Kurtarma (Backup & Recovery)
SAP HANA veri tabanının sürekliliğini ve veri güvenliğini sağlamak amacıyla farklı yedekleme stratejileri yürütülür.
--Yedekleme Türleri:

-Full Backup (Tam Yedek): Veri tabanındaki tüm veri kütlesinin yedeğidir. Sistemin büyüklüğüne göre 6-8 saat sürebilir ve süreç esnasında sistemi kapatmaya gerek yoktur (Online Backup yapılabilir).
-Incremental Backup (Artımlı Yedek): En son alınan tam veya artımlı yedekten sonra sadece değişen verileri kapsar. Az yer kaplar ancak geri yükleme (Restore) esnasında tüm zincirin sağlam olmasına bağımlıdır.
-Differential Backup (Fark Yedeği): En son alınan tam yedekten sonra değişen tüm verileri kopyalar. Artımlı yedeğe göre diskte fazla yer kaplar ama geri dönme hızı çok daha yüksektir.
--Kritik Metrikler (RPO ve RTO)

-RPO (Recovery Point Objective): Bir felaket anında şirketin ne kadarlık veri kaybına tahammülü olduğunu belirtir (Örn: "En fazla 10 dakikalık veri kaybolabilir").
-RTO (Recovery Time Objective): Sistem çöktükten sonra Basis ekibinin sistemi ne kadar sürede ayağa kaldırması gerektiğini gösteren zamandır (Örn: "En fazla 2 saat içinde sistem çalışmalıdır")
--Felaket Kurtarma Merkezi (DRC) ve Log Shipping: Sunucu merkezinin (Örn: Ankara) fiziksel bir zarar görmesi riskine karşı, başka bir şehirde (Örn: İzmir) sistemin birebir kopyası (Disaster Recovery Site) hazır tutulur. Canlı sistemdeki Redo Log dosyaları ağ üzerinden sürekli uzak sunucuya taşınır (Log Shipping); bu sayede ana merkez çöktüğü an trafik saniyeler içinde uzak lokasyona yönlendirilir (Switchover/Failover).

2. Arka Plan İş Yönetimi (Background / Batch Jobs)
   Sistemi yoran uzun ve hacimli programların kullanıcı performansını etkilememesi için süreçler arka planda (Background) çalışacak şekilde zamanlanır.
   --İşlem Kodları (T-Codes):
   -SM36: Arka plan işlerinin (Job) tanımlandığı, hangi ABAP programının hangi varyant ve yazıcı parametreleriyle ne zaman koşturulacağının (Örn: Her gece 03:00'te) planlandığı ekrandır.
   -SM37: Zamanlanmış veya geçmişte koşturulmuş tüm işlerin durumlarının (Scheduled, Released, Ready, Active, Finished, Canceled) izlendiği ve loglarının incelendiği ana takip merkezidir.

   --İş Süreci (Work Process) Ataması: Arka plan işleri tetiklendiğinde SM50 ekranındaki BGD (Background) iş süreçleri tarafından devralınarak yürütülür.
   --Best Practice: Bir arka plan işi oluşturulurken asla kişisel kullanıcı hesabı bağlanmamalıdır; personelin işten ayrılması durumunda işlerin durmaması için her zaman SAPCHG veya sistem servis hesapları gibi genel (Generic) kullanıcılar tanımlanmalıdır.

3. Sistem Kopyalama ve Yenileme (System Copy & Refresh)
      Canlı sistemdeki (PRD) taze ve gerçek verilerin, testlerin doğru yürütülebilmesi amacıyla kalite/test sistemine (QA) birebir aktarılması işlemidir.
      -Homojen Sistem Kopyası: Kaynak sistem (PRD) ile hedef sistemin (QA) işletim sistemi ve veri tabanı mimarisinin birebir aynı olduğu durumlarda (Örn: Her ikisi de Linux + HANA) uygulanan kopyalama yöntemidir. Süreç veri tabanı yedeğinin (Full Backup) hedef sunucuya taşınarak üzerine restore edilmesiyle yürütülür.
      -Heterojen Sistem Kopyası: Kaynak ve hedef sistemlerin işletim sistemi veya veri tabanının farklı olduğu (Örn: Windows + Oracle'dan Linux + HANA'ya geçiş) durumlarda yapılan göç (Migration) işlemidir. Bu işlemde yedek dosyası doğrudan kullanılamaz; veriler SWPM aracıyla export ve import edilerek aktarılır.

4. Kritik Sistem Yenileme Sonrası Adımları (System Refresh Post-Steps)
   Veri tabanı yedeği hedef sisteme geri yüklendiği an, hedef sistem kendisini hâlâ "Canlı (PRD) Sistem" zanneder. Canlı sisteme ait tüm bağlantılar, şifreler ve entegrasyonlar hedef ortama aynen kopyalandığı için, bu adımların gecikmeden manuel olarak temizlenmesi Basis danışmanının en büyük sorumluluğudur.
Kopyalama biter bitmez sırasıyla şu Post-Step (Kurulum Sonrası) adımları uygulanır:
-Arka Plan İşlerinin Temizlenmesi (RSBTC_CLEAR_BDC_ TABLES): Canlı sistemden kopyalanan binlerce zamanlanmış işin test ortamında yanlışlıkla çalışıp dış dünyaya hatalı mailler veya faturalar fırlatmasını önlemek için, SM37 kuyruğundaki tüm işler RSBTC_CLEAR_BDC_TABLES programı ile hızla silinir veya askıya alınır.
-RFC Bağlantılarının ve İletişim Kanallarının Temizlenmesi (SM59 & SCOT): Canlı banka entegrasyonları, müşteri API'leri ve dış sistem bağlantıları test ortamında felaketlere yol açmaması için SM59 ekranındaki kritik RFC bağlantıları silinir veya güvenli test adreslerine yönlendirilir. Aynı şekilde SCOT e-posta sunucu ayarları da dışarıya mail çıkışını engellemek için bozulur veya kapatılır.
-Veri Tabanı Bağlantılarının Kesilmesi (DBCO): Canlı veri tabanı bağlantı stringleri (DBCO) test ortamına göre yeniden konfigüre edilerek test sisteminin yanlışlıkla kurumsal canlı veri tabanlarına yazma yapması engellenir.
-Spool Kuyruğunun Temizlenmesi (SP01): Canlıdan kalan ve yazıcılarda bekleyen eski çıktı kuyrukları SP01 üzerinden temizlenerek test ortamındaki yazıcıların kilitlenmesi önlenir.
-Mantıksal Sistem İsimlerinin Dönüştürülmesi (BDLS): Veri tabanındaki tüm tablolarda gömülü kalan eski canlı sistem isimleri (Örn: PRDCLNT400), BDLS işlem kodu koşturularak yeni test sisteminin mantıksal ismiyle (Örn: QACLNT400) baştan aşağı güncellenir.
-Kullanıcı Kilitlerinin Yönetimi (SU10): Şirket genelindeki tüm canlı kullanıcıların test sistemine girip verileri kurcalamasını önlemek için, Basis ekibi dışındaki tüm kullanıcılar SU10 ile toplu olarak kilitlenir.
-Giriş Bilgi Ekranının Özelleştirilmesi (SE61): Kullanıcıların hangi sunucuya girdiğini net fark etmesi için SAP giriş ekranına SE61 işlem kodu üzerinden büyük harflerle "DİKKAT: BURASI TEST / KALİTE ORTAMIDIR" bilgi notu eklenir.
-Taşıma Sistemi Konfigürasyonu (STMS): Canlı sistemden kopyalanan eski taşıma rotaları silinir; test sunucusu STMS ekranından geliştirme (DEV) sistemine bağlı bir hedef olarak yeniden yapılandırılır.

   
