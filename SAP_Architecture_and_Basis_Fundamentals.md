SAP Architecture and Basis Fundamentals
SAP Multi-Tier Architecture (3-Tier Architecture)
The system shifts from traditional Mainframe structures to SAP R/3 and modern S/4HANA architecture.

The architecture relies on 3 core layers: Presentation (UI), Application, and Database.

Front-End and User Experience (UX)
Modern environments utilize web-based technologies such as HTML, JavaScript, and UI5 alongside the classic SAP GUI.

These components deliver responsive designs through the modern SAP Fiori ecosystem.

Execution Framework and Work Processes
The system executes background and foreground tasks through specific Work Processes.

Core operations depend on Dialogue (direct user interactions), Background (batch job processing), and Update (database write operations).

System Components and Network Management
The architecture integrates ICM (Internet Communication Manager) to manage internet traffic and Gateway connections for communication.

The file system layout utilizes the NFS (Network File System) structure.

Instance Topologies and Scaling
System scalability (Scale-out) relies on Central Instance, Primary Application Server (PAS), and Additional Application Server (AAS) topologies.

Each system configuration requires a SID (System ID – a unique 3-character identifier) and an Instance Number.

User Licensing and Analytics
The SAP landscape defines distinct licensing models, including Professional, Limited Professional, Developer, and Worker roles.

The system tracks user profiles and utilization statistics for performance analytics.

Operating System and Database Strategy
Modern S/4HANA strategies replace traditional databases like Oracle with SAP HANA (In-Memory database architecture).

These environments run strictly on Linux Enterprise Server (SUSE / Red Hat) distributions.

Change and Transport Management
The system moves developments and configurations between different environments using the Change and Transport System.

This process manages modifications through Customizing Requests and Workbench Requests.

-----------------------TR:

Harika bir Markdown (.md) dosyası içeriği oldu. Cümle sonlarındaki tüm geçmişe ve sürece yönelik ifadeleri (anlatılmıştır, bahsedilmiştir, yapılmıştır veya vtt kalıntıları gibi) temizleyerek, tamamen doğrudan, kesin ve profesyonel bir teknik doküman formatına getirdim.

Dosyayı doğrudan bu haliyle kopyalayıp repoya ekleyebilirsin:

SAP Mimarisi ve Basis Temelleri
SAP Çok Katmanlı Mimari (3-Tier Architecture)
Sistem, geleneksel Mainframe yapılardan SAP R/3 ve modern S/4HANA mimarisine geçişi kapsar.

Mimari 3 temel katmana dayanır: Presentation (Kullanıcı Arayüzü), Application (Uygulama) ve Database (Veri Tabanı).

Front-End ve Kullanıcı Deneyimi (UX)
Modern ortamlar, klasik SAP GUI'nin yanında HTML, JavaScript ve UI5 gibi web tabanlı teknolojileri kullanır.

Bu bileşenler, modern SAP Fiori ekosistemi aracılığıyla duyarlı (responsive) tasarımlar sunar.

İşlem Çerçevesi ve İş Süreçleri (Work Processes)
Sistem, arka plan ve ön plan görevlerini belirli Work Process'ler (İş Süreçleri) aracılığıyla yürütür.

Çekirdek işlemler Dialogue (doğrudan kullanıcı etkileşimleri), Background (toplu iş işleme) ve Update (veri tabanı yazma işlemleri) süreçlerine bağlıdır.

Sistem Bileşenleri ve Ağ Yönetimi
Mimari, internet trafiğini yönetmek için ICM (Internet Communication Manager) bileşenini ve iletişim için Gateway bağlantılarını entegre eder.

Dosya sistemi düzeni, NFS (Network File System) yapısını kullanır.

Instance Topolojileri ve Ölçeklendirme
Sistem ölçeklenebilirliği (Scale-out), Central Instance, Primary Application Server (PAS) ve Additional Application Server (AAS) topolojilerine dayanır.

Her sistem yapılandırması bir SID (System ID – 3 karakterli benzersiz bir tanımlayıcı) ve bir Instance Number (Instance Numarası) gerektirir.

Kullanıcı Lisanslama ve Analitik
SAP ekosistemi; Professional, Limited Professional, Developer ve Worker rolleri dahil olmak üzere farklı lisanslama modelleri tanımlar.

Sistem, performans analitiği için kullanıcı profillerini ve kullanım istatistiklerini takip eder.

İşletim Sistemi ve Veri Tabanı Stratejisi
Modern S/4HANA stratejileri, Oracle gibi geleneksel veri tabanlarının yerini SAP HANA (In-Memory/Bellek içi veri tabanı mimarisi) ile değiştirir.

Bu ortamlar kesin olarak Linux Enterprise Server (SUSE / Red Hat) dağıtımları üzerinde çalışır.

Değişiklik ve Transport (Taşıma) Yönetimi
Sistem; geliştirmeleri ve yapılandırmaları Değişiklik ve Taşıma Sistemi (Change and Transport System) kullanarak farklı ortamlar arasında taşır.

Bu süreç, değişiklikleri Customizing Request ve Workbench Request'ler aracılığıyla yönetir.
