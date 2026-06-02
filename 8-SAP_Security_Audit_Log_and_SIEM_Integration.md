1. SAP Güvenlik ve Yetkilendirme Hiyerarşisi (SU01 ve PFCG)

Teknik Yetki Akışı: SAP programları ve işlem kodları (T-Code) doğrudan rolleri tanımaz; arka plandaki en küçük teknik nesne olan Yetki Objelerini (Authorization Objects) kontrol eder.
Hiyerarşik Yapı: Yetki objeleri bir araya gelerek Profilleri, profiller ise son kullanıcıya atanan Rolleri oluşturur.
SU24 (T-Code ve Obje İlişkisi): Bir işlem kodunun (Örn: Cihaz dizinlerini listeleyen AL11) düzgün çalışabilmesi için arka planda hangi yetki objelerine (S_ADMI_FCD, S_DATASET vb.) ihtiyaç duyduğu SU24 ekranından analiz edilir.
SU21 (Yetki Obje Yönetimi): Sistemdeki tüm yetki objelerinin sınıflar halinde (Örn: BC_A) ve içerdikleri teknik parametrelerle (Örn: ACTVT - Aktivite kodu: 03=Görüntüleme, 02=Değiştirme) yönetildiği ekrandır.
Geliştirici Tüyosu (Z'leme): SAP ile hazır gelen standart şablon roller (Örn: SAP_AUDIT_ADMIN) asla doğrudan bir kullanıcıya atanmamalıdır. Bu roller PFCG ekranında "Z"'lenerek (Z_ ile başlayan yeni bir kurumsal rol olarak) kopyalanmalı, düzenlenmeli ve kullanıcıya o şekilde bağlanmalıdır.

2. Kullanıcı İzleme, Raporlama ve Güvenlik Parametreleri
SUIM (Kullanıcı Bilgi Sistemi): "Sistemde kimlerin SAP_ALL (tam yetki) hakkı var?", "Kritik yetki objeleri kimlere atanmış?" veya "Hatalı şifre giren kullanıcılar kimler?" gibi tüm denetim ve siber güvenlik raporlarının çekildiği merkezi ekrandır.

SU53 (Anlık Yetki Kontrolü): Bir kullanıcı yetki hatası aldığında, SU53 ekranı çalıştırılarak o an takıldığı yetki nesnesi, alan ve eksik parametre değeri nokta atışı tespit edilir.

Kritik Oturum Parametreleri (RZ11):

Kullanıcının üst üste kaç kez hatalı şifre girdiğinde kilitleneceği (Örn: Güvenlik politikası gereği 5 deneme),

Bir kullanıcının aynı anda en fazla kaç adet bağımsız oturum (ekran/pencere) açabileceği (SAP GUI mimarisinde teknik sınır 6'dır),

Sunucu RAM'ini boşa harcamamak adına, belirli bir süre işlem yapmayan (Idle) kullanıcının sistemden otomatik olarak atılma süresi (Timeout) gibi kurallar RZ11 üzerindeki dinamik/statik parametrelerle yönetilir.

3. Ölçeklenebilirlik ve Yük Dengeleme: Logon Groups (SMLG)
Büyük kurumlarda sistemin çökmesini engellemek ve yüzlerce/binlerce kullanıcının yükünü dengeli dağıtmak amacıyla Logon Group (Oturum Açma Grupları) mimarisi kullanılır.
[Kullanıcı Giriş Talebi (SAP GUI)] 
                │
                ▼
      [ 1. MESSAGE SERVER ] (Yükü Kontrol Eder)
                │
         ┌──────┴──────┐
         ▼             ▼
  [ 2. DIALOG INSTANCE 1 ]   [ 3. DIALOG INSTANCE 2 ]


SMLG T-Code: Birden fazla uygulama sunucusunun (Dialog Instance) bir araya getirilerek gruplandığı ekrandır. Örneğin; Muhasebe ekibi için FI_GROUP adında bir logon grubu oluşturulup sadece o işe ayrılmış sunucular bu gruba bağlanabilir.

Message Server'ın Görevi: Kullanıcı SAP GUI üzerinden logon grubunu seçerek giriş yaptığında, istek doğrudan Message Server'a gider. Message Server, gruptaki sunucuların anlık yük durumunu kontrol eder ve kullanıcıyı en boş olan Dialog Instance'a yönlendirerek otomatik yük dengelemesi (Load Balancing) sağlar.

Uç Kullanıcı Yapılandırması (services): Bu yapının çalışabilmesi için uç kullanıcının bilgisayarındaki C:\Windows\System32\drivers\etc\services dosyasına SAP'nin Message Server portunu belirten teknik satırın (Örn: sapms[SID] 36[Instance_No]/tcp) eklenmesi gerekir.

4. SAP Siber Güvenlik ve Loglama Çeşitleri
SAP sistemi üzerinde dönen her operasyon, sistem seviyesinde farklı log mekanizmalarıyla kayıt altına alınır:
Sistem Logları (SM21): SAP uygulama sunucusu seviyesinde oluşan hataları, sistem kilitlenmelerini, parametre değişikliklerini ve sunucu hareketlerini izlemeye yarar.

Uygulama Logları (SLG1 / BALHDR): İş süreçleri (Workflow), toplu işler (Batch Jobs) ve programların kendi içinde ürettiği uygulama seviyesindeki çalışma ve hata kayıtlarını tutar.

Kotsal İptal Logları (ST22 - Dump Analysis): Programlama hatalarından (Sıfıra bölme, veri tipi uyuşmazlığı vb.) dolayı çöken ve yarıda kesilen ABAP kodlarının detaylı analizi ve tespiti için kullanılır.

Tablo Değişiklik Logları (DBTABLOG / SE16): Kritik tablolarda (Örn: USR02 kullanıcı tablosu) veri güvenliğini sağlamak için, tablonun teknik ayarlarından "Log data changes" özelliği aktif edilir. Böylece o tabloda yapılan her türlü ekleme, silme ve güncelleme işlemi DBTABLOG tablosuna (veya SCU3 işlem koduna) kaydedilir.

Giriş Çıkış Geçmişi (USR41 / RSDG_USER_CHANGE_LOG): Kullanıcıların sisteme hangi tarihte, hangi bilgisayar adından ve IP adresinden giriş/çıkış yaptığını ve şifre değiştirme geçmişini saklayan log yapısıdır.


5. Security Audit Log (SAL) Yönetimi (SM19 / SM20)
Siber güvenlik denetimlerinde sızma girişimlerini, yetkisiz erişimleri ve kritik sistem hareketlerini izlemek için kullanılan en temel Siber Güvenlik Günlüğü mekanizmasıdır.

SM19 (Log Yapılandırması): Security Audit Log sisteminin parametrik olarak aktif edildiği ve filtrelerin tanımlandığı ekrandır. Filtreler üzerinde hangi istemcilerde (Client), hangi kullanıcılarda ve hangi olaylarda (Örn: Hatalı şifre denemesi AO2, kritik T-Code çalıştırma) log alınacağı belirlenir.

Depolama Stratejisi (File vs. Database): SAL kayıtları veri tabanına yazılırsa hızlı sorgulanır ancak çok fazla kullanıcı olan sistemlerde veri tabanına ciddi yük getirir. Bu yüzden logların dosya sistemi (File System) seviyesinde sunucunun yerel diskinde (/usr/sap/[SID]/.../log) saklanması ve SM18 ile 30 günden eski logların otomatik temizlenmesi performans açısından tavsiye edilir.

SM20 (Log Analizi): SM19 ile toplanan siber güvenlik günlüklerinin sorgulandığı ve incelendiği ekrandır. Örneğin; bir kullanıcıya bilerek hatalı şifre girildiğinde sunucu, terminal adı ve IP adresiyle birlikte siber saldırı veya yetkisiz giriş denemesi anında SM20 (veya SM21) ekranına düşer.

SIEM Entegrasyonu: Büyük şirketlerde bu ham SAP logları (SAL, SM21 vb.) parse edilerek (anlamlandırılarak) merkezi siber güvenlik yönetim yazılımlarına (SIEM - Splunk, IBM QRadar, Microsoft Sentinel vb.) aktarılır ve tüm şirket genelinde anlık tehdit takibi yapılır.

