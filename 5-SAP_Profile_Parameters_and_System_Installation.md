1. SAP Profil Parametreleri (RZ10, RZ11 ve SM50)
SAP sisteminin çalışma performansını, hafıza (RAM) limitlerini ve güvenlik politikalarını (örneğin şifre kuralları) belirleyen arka plan ayarlarına Profil Parametreleri denir.

İşlem Kodları (T-Codes):
RZ11: Parametreleri görüntülemeye, dökümantasyonunu okumaya ve alabileceği değerleri (True/False, sayısal değerler vb.) incelemeye yarar.
RZ10: Profil dosyalarını düzenlemek, yeni parametre eklemek veya mevcut parametre değerlerini değiştirmek için kullanılır.

Statik ve Dinamik Parametre Farkı:


Statik Parametreler: Değiştirildikten sonra aktif olabilmesi için sistemin mutlaka yeniden başlatılması (Restart) gerekir. Derste diyalog iş süreci (Work Process) sayısının SM50 ekranında 10'dan 11'e çıkarılması için sistemin kapatılıp açılması bu duruma örnektir.
Dinamik Parametreler: Sistemi kapatıp açmaya gerek kalmadan, RZ11 üzerinden değiştirildiği an sistemde canlı olarak aktifleşen parametrelerdir.
Profillerin Okunma Öncelik Sırası (Hiyerarşi):
Sistem açılırken parametre değerlerini şu sıraya göre okur ve baskılar:

Instance Profile (Instance Profili): O uygulama sunucusuna özel ayarlardır.
Default Profile (Default Profil): Tüm uygulama sunucularının ortak baktığı genel dosyadır.
Kernel Default: Eğer hiçbir profilde değer set edilmediyse, SAP çekirdeğinin (Kernel) fabrikasyon varsayılan değerleri devreye girer.
Dolayısıyla, hem Default hem Instance profilinde aynı parametre varsa, Instance Profili geçerli olur ve diğerini ezer.


2. SAP ABAP Developer Edition ve Linux (Ubuntu) Kurulumu
SAP Basis danışmanlarının kendi bilgisayarlarında deneme yapabilmesi için ücretsiz sunulan laboratuvar sürümünün sanal makineye kurulma adımları uygulamalı olarak işleniyor.


Sanallaştırma (VirtualBox): Bilgisayarın donanım kaynaklarını (CPU, RAM, Disk) bölerek içinde bağımsız sanal işletim sistemleri çalıştırmayı sağlar
Ubuntu Sunucu Hazırlığı:
Ağ ayarlarında dış dünya ve SAP GUI ile kolay konuşabilmesi için Köprü Bağdaştırıcı (Bridged Adapter) seçilir.

SAP'nin isimden IP çözümlemesi yapabilmesi için Linux'taki /etc/hosts dosyasına sunucunun statik IP'si ve vhcalnplci.dummy.nodomain şeklindeki Hostname bilgisi mutlaka kaydedilir.
SAP kernelının çalışabilmesi için gerekli olan uuidd servisi, tcsh ve ksh (Korn Shell) gibi Linux paketleri kurulur.
SAP Kurulum Aşaması:

Windows'tan Linux sunucusuna kurulum dosyalarını aktarmak için 22 nolu port üzerinden SFTP (WinSCP / FileZilla) protokolü kullanılır.
Kurulum paketindeki ./install.sh scripti çalıştırılarak süreç başlatılır. Kurulum esnasında geçici lisans dosyalarının doğru dizine kopyalanması (cp) ve yetkilerinin (chmod 777) ayarlanması gerekir.
Kurulum bittiğinde sistemde otomatik olarak sidadm (Örn: npladm) kullanıcısı oluşur ve terminalde startsap komutuyla sistem ayağa kaldırılır.

3. Maintenance Planner (Sistem Bakım ve Güncelleme Planlayıcısı)
Gerçek şirket ortamlarında sistemlerin paket seviyelerini yükseltmek (Upgrade / Patch) veya yeni bir SAP bileşeni kurmak için kullanılan bulut tabanlı resmi SAP aracı tanıtılıyor.

Hayat Kurtaran Özelliği: SAP sistemleri birbirine bağımlı yüzlerce paketten oluşur. Bir paketi güncellerken başka hangi paketlerin de güncellenmesi gerektiğini insan eliyle hesaplamak imkansıza yakındır. Maintenance Planner, sistem bileşenlerini analiz ederek indirmeniz gereken dosyaların bütünsel bir listesini (Stack File - .xml) otomatik oluşturur.
Sistem Sürdürülebilirliği (Identical Landscapes):Maintenance Planner üzerindeki "Copy" özelliği sayesinde, DEV sunucusu için planlanan bakım aktivitesinin ve indirme listesinin bir kopyası doğrudan QA sunucusuna aktarılarak sistemler arasında %100 uyumluluk (Identity) garanti edilir.
Geliştirme (DEV), Test (QA) ve Canlı (PRD) sistem diziliminde, DEV sistemine kurulan bir güncelleme paket setinin birebir aynısının QA ve PRD sistemlerine de kurulması zorunludur
Maintenance Planner üzerindeki "Copy" özelliği sayesinde, DEV sunucusu için planlanan bakım aktivitesinin ve indirme listesinin bir kopyası doğrudan QA sunucusuna aktarılarak sistemler arasında %100 uyumluluk (Identity) garanti edilir.
Downtime (Sistem Kesinti Süresi) Öngörüsü: Büyük güncellemeler (Upgrade) haftalarca süren hazırlıklar gerektirebilir. Ancak sistemin tamamen kapatılması gereken asıl kesinti süresi (Downtime), aracın sunduğu analizler sayesinde önceden 3-5 saat gibi kısa sürelere indirgenecek şekilde planlanabilir.


