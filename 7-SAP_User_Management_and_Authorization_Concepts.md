1. Yetkilendirme (Authorization) Neden Hayatidir?
Sistem Kasasını Korumak: Yetkilendirme, SAP sisteminin kapısına kilit vurmak gibidir. Kontrolsüz bir sistemde her kullanıcı her veriye erişirse veri güvenliği ve sistem kararlılığı ortadan kalkar.

Görevler Ayrılığı (Segregation of Duties - SoD): Sistemde suiistimalleri ve hataları önlemek amacıyla iş süreçlerindeki kritik adımların farklı kişilere bölünmesi ilkesidir. Örneğin; bir şirkette satın alma siparişini oluşturan kişi (Create) ile o siparişi onaylayan müdürün (Approve) yetkileri ve rolleri kesin olarak birbirinden ayrılmalıdır. Bir kişi hem sipariş geçip hem de kendi faturasını onaylayamamalıdır.

2. SAP Yetki Mimarisi Nasıl Çalışır? (Hiyerarşi)

[ 1. KULLANICI (User) ]        --> Sisteme giriş yapan hesap (Örn: SU01)
         │
[ 2. ROL (Role) ]              --> Görev tanımı (Örn: Muhasebe Sorumlusu - PFCG)
         │
[ 3. PROFİL (Profile) ]        --> Arka planda otomatik oluşan teknik paket
         │
[ 4. YETKİ OBJESİ (Object) ]   --> En küçük yetki nesnesi (Örn: S_TCODE, S_TABU_DIS)

Rol Yönetimi (PFCG T-Code): Kullanıcıların görevlerine göre rollerin yaratıldığı, içerisine hangi işlem kodlarının (T-Code) ekleneceğinin belirlendiği ana ekrandır.

Profil Mantığı: Bir rol oluşturulup kaydedildiğinde, sistem arka planda T_ ile başlayan benzersiz bir teknik profil dosyası üretir. Kullanıcıya rol atandığında aslında arka planda bu profil atanmış olur.

Teknik Alt Bilgi: SAP dünyasındaki en yetkili, her şeye erişebilen süper kullanıcı profili SAP_ALL'dur. SAP kurulumunu yapan Basis danışmanlarının kullandığı en yetkili kurulum profili ise SAP_NEW'dur.

Yetki Objeleri (Authorization Objects): Sistem rollerini değil, doğrudan en küçük teknik nesneleri kontrol eder.

S_TCODE: Kullanıcının hangi işlem kodlarını çalıştırabileceğini denetleyen en temel objedir; alt parametresi olan TCD alanına izin verilen kodlar yazılır.

S_TABU_DIS: Kullanıcının veri tabanındaki tabloları görüntüleyip görüntüleyemeyeceğini (Display/Modify) denetleyen kritik objedir.

3. SAP Kullanıcı Tipleri ve Özellikleri (SU01)
SAP'de kullanıcıların sisteme nasıl giriş yapacağına ve ne amaçla kullanılacağına göre 5 farklı kullanıcı tipi tanımlanır:

Kullanıcı Tipi,Özellikleri,Şifre Süresi Değişimi,Kullanım Amacı
Dialogue (Diyalog),Klasik insan kullanıcısıdır. SAP GUI veya Fiori üzerinden sisteme etkileşimli olarak giriş yapar.,Parametrede belirtilen sürede bir şifre değiştirmeye zorlanır.,Şirket çalışanları.
System (Sistem),İnsan etkileşimi olmadan çalışan arka plan süreçleri ve robotlar içindir. SAP GUI ile giriş yapamaz (Non-Dialogue).,Şifre değişimine ve süre kısıtlamasına uğramaz.,"Arka plan işleri (Batch), RFC bağlantıları ve RPA robotları."
Communication,Dış sistemlerle entegrasyon amacıyla kullanılır. Ekran arayüzü kullanımına kapalıdır.,Şifre değişim zorunluluğu yoktur.,Sistemler arası veri transferi ve API entegrasyonları.
Service (Servis),Genelde anonim veya geniş kitlelerin ortak erişimi için tanımlanır.,Kullanıcıyı şifre değiştirmeye zorlamaz.,"Misafir kullanıcılar, dışarıdan gelen teknik destek ekipleri."
Reference,"Doğrudan sisteme giriş yetkisi olmayan, sadece yetki kopyalamaya yarayan şablon kullanıcıdır.",Giriş yetkisi olmadığı için şifre süreçlerinden muaftır.,Aynı kadrodaki çok sayıda çalışana ortak yetki dağıtmak.


4. Basis Danışmanının Yetki ve Analiz Araçları
Sistem içinde kimin hangi yetkiye sahip olduğunu bulmak veya yetki hatalarını çözmek için şu 3 temel işlem kodu (T-Code) kullanılır:

SUIM (User Information System): Yetki dünyasının en güçlü raporlama aracıdır. "Sistemde SAP_ALL yetkisine sahip kritik kullanıcılar kimler?", "Hangi kullanıcı hangi rollere sahip?" veya "X işlem koduna kimlerin giriş izni var?" gibi hayati güvenlik raporları bu ekrandan çekilir.

SU53 (Authorization Check): Bir kullanıcı sistemde bir işlemi yaparken "Kullanıcı yetkiniz bulunmamaktadır" hatası aldığında başvurulacak ilk ekrandır. Kullanıcıya bu kodu çalıştırmasını söyleyerek, o an takıldığı en alt katmandaki eksik yetki objesinin ve parametresinin ne olduğu nokta atışı tespit edilir ve PFCG üzerinden role ilave edilir.

ST01 / STAUTHTRACE (Sistem İzi / Trace): Kullanıcıların sistem üzerinde yaptığı tüm hareketleri ve yetki kontrollerini arka planda canlı olarak izlemeye (Trace etmeye) yarar. Kullanıcının hangi ekranlardan geçerek hangi nesneleri tetiklediği detaylıca listelenir.

5. Gerçek Projelerden Sektörel Basis Tiyoları
Kullanıcı Kayıtlarında İzlenebilirlik: Yeni bir kullanıcı açarken veya bir rolü güncellerken, SU01 ekranındaki bilgi alanına mutlaka talebi yapan kişinin adı, tarihi ve talep edilen destek biletinin (Ticket ID) numarası yazılmalıdır. İleride yaşanacak bir denetimde veya kriz anında kendinizi korumanın tek yolu bu geriye dönük dökümantasyondur.

Şablon Rolleri Doğrudan Atamama: SAP sistemiyle birlikte varsayılan gelen hazır şablon roller (Örn: SAP_ ile başlayanlar) asla doğrudan kullanıcılara atanmamalıdır. Bu roller her zaman PFCG üzerinden "Z"'lenerek (Kopyalanarak Z_ ile başlayan yeni bir kurumsal rol oluşturularak) özelleştirilmeli ve kullanıcıya o şekilde tanımlanmalıdır.

Lisans Maliyeti Optimizasyonu: Kullanıcı kartlarındaki lisans veri alanının boş bırakılması, SAP'nin o kullanıcıyı en pahalı lisans türünden ücretlendirmesine yol açar. Şirket bütçesini korumak için kullanıcıların gerçek görev tanımlarına göre lisans tiplerinin (Professional, Limited vb.) girilmesi Basis ekibinin sorumluluğundadır.

Merkezi Kullanıcı Yönetimi (CUA - Central User Administration): Şirkette onlarca farklı SAP sunucusu (DEV, QA, PRD, HR, CRM) olduğunda, her sistemde tek tek kullanıcı açmak yerine, tek bir sistemi ana merkez (Master) seçerek tüm sunuculardaki kullanıcı ve şifre yönetimini tek bir ekrandan dağıtmayı sağlayan mimaridir. (Günümüz dünyasında bu yapı buluttaki Identity Access Governance - IAG sistemine taşınmaktadır).
