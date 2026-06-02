1. SAP Security Patch Day (Güvenlik Yamaları Yönetimi)
SAP, sistem güvenliğini en üst düzeyde tutmak amacıyla her ay düzenli olarak güvenlik açıklarını kapatan yamalar yayınlar.
Periyodik Yayın: SAP, siber güvenlik açıklarını ve bunlara ait çözüm notlarını (Security Notes / Patches) her ayın 2. Salı günü resmi olarak yayınlar.

Zafiyet Keşif Süreci: Bu açıklar, SAP'nin kendi özel siber güvenlik laboratuvarlarındaki ekiplerin sistemleri kırmaya çalışmasıyla (Offensive Security / Vulnerability Assessment) veya dünya genelindeki firmalardan/kullanıcılardan gelen bildirimlerle keşfedilir.

Kritiklik Derecesi (CVSS Skorları): Yayınlanan her güvenlik yamasının önem derecesini ve sisteme olan etki seviyesini (Impact Level) belirten uluslararası CVSS skorları ve her bir bulguya ait benzersiz CVE numaraları bulunur. Skor ne kadar yüksekse (Örn: 9.1), açığın o kadar kritik olduğu anlaşılır ve Basis ekibi tarafından hızla aksiyon alınması gerekir.

Sistem Uyumluluk Kontrolü: Basis danışmanı, SM51 gibi işlem kodlarından sistemin mevcut kernel (sap_exe, sap_db) ve destek paket seviyelerini (Support Package Level) inceler. Eğer yayınlanan yama sistemdeki mevcut sürümden daha eski bir açığı kapatıyorsa veya sistem o düzeltmeyi zaten barındırıyorsa yamanın yüklenmesine gerek kalmaz; sistem de zaten yüklemeye izin vermez

Uygulama Metodolojisi (Best Practice): Bir güvenlik yaması veya düzeltme notu asla doğrudan canlı (Production) sisteme yüklenmez. Önce Sandbox veya Test (QA) ortamlarına yüklenir, sistemde bozulan veya etkilenen bir fonksiyon olup olmadığı test edilir ve kullanıcı onayından sonra canlı ortama implante edilir. Yama; kod bloklarının güncellenmesi, bir taşıma isteğinin (Transport Request) geçilmesi veya kernel upgrade şeklinde sisteme uygulanır.

2. SAP Yazıcı ve Çıktı Mimarisi (Spool Architecture)
SAP içindeki bir belgenin (fatura, tır giriş kaydı, barkod vb.) basılması süreci, arka planda çalışan Spool mimarisi tarafından orkestre edilir

Spool Server ve Work Process: Büyük sistemlerde çıktı trafiğini yönetmek ve yük dengelemesi (Load Balancing) sağlamak amacıyla belirli bir sunucu merkezi spool sunucusu olarak atanabilir. SM50/SM66 ekranlarında gördüğümüz Spool Work Process (SPO), kullanıcıların çıktı taleplerini toplar, önce veri tabanındaki geçici bir tampon alana (Spool Buffer) yazar ve ardından ilgili yazıcıya sevk eder

Baskı Talebi Hiyerarşisi: SAP'de bir çıktı tetiklendiğinde iki aşamalı bir kimlik oluşur:

Spool Request: Çıktısı alınacak belgenin içeriğini, formatını ve ham verisini tutan istektir.
Output Request: Bu belgenin fiziksel olarak belirli bir yazıcıya (Printer) gönderilme emridi

SP01 (Spool Controller): Sistem genelinde gönderilen tüm çıktı taleplerinin anlık durumunun (Yeşil = Başarılı / Kırmızı = Hatalı / Waiting = Bekliyor) izlendiği, hataların incelendiği veya bir çıktının başka bir yazıcıya tekrar yönlendirilebildiği ana kontrol merkezidir.

3. SPAD ile Cihaz Tanımlama ve Erişim Metotları (Access Methods)
Sistemdeki tüm fiziksel yazıcılar, barkod basıcılar ve PDF dönüştürücüler SPAD (Spool Administration) işlem kodu üzerinden sisteme eklenir ve konfigüre edilir. Bir yazıcının SAP'ye eklenmesindeki en kritik adım Akses Metodunun (Access Method) doğru seçilmesidir

Frontex Printing (Akses Metodu: G): Kullanıcının doğrudan kendi bilgisayarında (lokalinde) tanımlı olan varsayılan yazıcıya çıktı göndermesini sağlar. Kullanıcı belgeyi yazdır dediğinde SAP GUI arayüzü tetiklenir ve Windows'un kendi yerel yazdırma servisi devreye girerek çıktıyı basar. Lokal yazıcılar için sunucu seviyesinde ek bir ayara gerek yoktur

Network / Server Printing (Akses Metodu: S / U): Ağ üzerindeki harici bir pirnt sunucusuna (Print Server) veya doğrudan IP adresi olan kurumsal bir ağ yazıcısına çıktı göndermek için kullanılır. Güvenli protokoller (LPD/LPR) üzerinden 515 veya 516 portları kullanılarak veri uzak lokasyondaki (Örn: Rize depodaki) yazıcı servisine (sapessprint) devredilir ve basılır

4. Driver, Format ve Yazı Tipi (Font) Yönetimi
Device Type (Yazıcı Sürücüsü): SAP'nin ham çıktı verisini, fiziksel yazıcının anlayacağı komut diline çeviren sürücülerdir. Örneğin; standart Windows yazıcıları için yaygın olarak I9SWIN (Türkçe karakter seti destekli) sürücüsü kullanılırken, etiket basan barkod yazıcıları için Zebra (LBLZ2) gibi özel üretici sürücüleri SPAD üzerinden sisteme import edilir.

Format ve Page Type: Basılacak kağıdın fiziksel boyutlarını (Örn: A4 portre 297x210 mm veya yatay llandscape) ve sürekli form kağıtlarının satır/sütun sınırlarını belirleyen şablonlardır. Gerektiğinde kuruma özel faturaya uygun z-formatları üretilebilir.

Font Yükleme (SE73): SAP içinde standart olarak bulunmayan kurumsal bir yazı tipinin (Örn: Kalın veya özel bir font) sisteme yüklenmesi işlemidir. SE73 işlem kodu kullanılarak TrueType (.ttf) formatındaki font dosyası SAP sistemine bir yama/istek halinde import edilir; böylece tasarlanan formlarda (Smartforms/Adobe Forms) bu yeni yazı tipi kullanılabilir hale gelir.

5. Form Teknolojileri ve Housekeeping (Sistem Temizliği)
Form Çeşitleri (Smartforms & Adobe Forms): SAP'de kurumsal döküman tasarımları geçmişte Smartforms ile yapılırken, modern mimaride yerini Java tabanlı çalışan Adobe Document Services (ADS) mimarisine bırakmıştır. ADS formlarının basılabilmesi için SAP NetWeaver Java sunucusu ile ABAP sunucusu arasında özel bir entegrasyon ve hedef bağlantısı (FP_PDF_TEST_00 gibi test kodları ile doğrulanan RFC/Destination) kurulmalıdır.

Housekeeping ve Taşma Önleme (RSPO0041): Gönderilen her çıktı veri tabanında (TSP01 ve TSP03 tablolarında) yer kaplar. Eğer sistem düzenli temizlenmezse, Spool numara aralığı dolar ve sistem "Spool Overflow" (Spool Taşması) hatası vererek şirketteki tüm yazdırma operasyonunu durdurur.

Otomatik Temizlik: Bunun önüne geçmek için RSPO0041 (veya modern sürümlerde RSPO1041) standart ABAP programı çalıştırılarak bir varyanta bağlanır (Örn: "10 günden eski başarılı çıktıları sil") ve bu program arka planda düzenli koşan bir arka plan işi (Batch Job) haline getirilerek sistem otomatik olarak temiz tutulur.

