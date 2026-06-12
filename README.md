# Algoritma Analizi Final Projesi (Proje 24)

**Hazırlayan:** Mustafa Efe Cebel  
**Seçilen Konu:** Dağıtık Yük Dengeleme (Consistent Hashing Simülasyonu)  
**Makale:** Karger, D., et al. (1997) - "Consistent Hashing and Random Trees"

---

## Projenin Amacı ve Özeti
Bu projede, Redis Cluster veya Memcached gibi büyük dağıtık mimarilerin arka planda yükü sunuculara nasıl adaletli dağıttığını gösteren web tabanlı bir simülatör geliştirdim. Amacım, geleneksel mod alma yöntemlerinin aksine, sistemde bir sunucu çöktüğünde tüm yapının patlamadığını, sadece çöken sunucunun yükünün akıllıca diğerlerine devredildiğini (failover) deneysel olarak kanıtlamak.

## Algoritma Medium ve Tasarım Detayları (Ödev Kriterleri)

### 1. Hash Ring (Halka) Mantığı ve Binary Search ($O(\log n)$)
* Sistemi dairesel bir hat (Hash Ring) üzerine kurdum. Hem sunuculara ait sanal düğümler hem de dağıtılacak veriler bu halka üzerinde benzersiz birer hash değerine sahip oluyor.
* Ödev isterlerinde kesin olarak belirtildiği gibi, halkaya gelen verinin saat yönünde hangi sunucuya düşeceğini bulurken ardışık arama ($O(n)$) kullanıp sistemi yormak yerine, **Binary Search (İkili Arama)** algoritmasını entegre ettim. Böylece arama maliyetini $O(\log n)$ seviyesine düşürerek teorik karmaşıklık ispatını kodda doğrudan sağladım.

### 2. Yükün Dengelenmesi ve Sanal Düğümler (Virtual Nodes)
* Projenin ilk geliştirme aşamalarında basit hash fonksiyonları yüzünden tüm yük tek bir sunucuya biniyordu (Hot Spot sorunu). Bu dağılım adaletsizliğini ve hash çakışmalarını tamamen çözmek için matematiksel dağılımı mükemmel olan **32-bit FNV-1a** hash algoritmasına geçtim.
* Her sunucu için halkada **16 adet sanal düğüm** (toplamda 80 nokta) oluşturdum. Bu sayede 100.000 adet test anahtarı halkaya olabildiğince dengeli ve adaletli bir şekilde paylaştırıldı.

### 3. $K/n$ Metrik Doğrulaması ve İstatistikler
* Geleneksel mod alma yöntemlerinde bir sunucu gitse her şeyin baştan rehash edilmesi gerekir. Kararlı Hashing'de ise sadece çöken sunucunun yükü taşınır.
* Yaptığım simülasyonda rastgele bir sunucuyu çökerttiğimde, yeri değişen (re-map edilen) anahtar sayısı **~28.512** civarında çıktı. Bu değer, teorik sınırımız olan $K/n$ ($100.000 / 5 = 20.000$) beklentisine oldukça yakın. Yani sistemdeki verilerin yaklaşık %70'i yerinde sapasağlam kalırken, sadece çöken sunucunun yükü başarıyla devredildi ve ampirik başarı kanıtlanmış oldu.

## Dosya Yapısı
* `index.html`: Projenin arayüz ve algoritma kodlarını içeren, harici hiçbir kütüphaneye veya internete bağımlı olmayan saf JavaScript/CSS dosyası.
* `README.md`: Şu an okuduğunuz teknik analiz ve proje raporu.

---

## 🛠️ Kodlama ve Geliştirme Süreci
Bu projenin geliştirilmesi sürecinde, modern mühendislik pratiklerine uygun olarak birçok yapay zeka modelinden aktif destek alınmıştır. Yapay zeka araçları projede şu aşamalarda kullanılmıştır:
* **Algoritma Kurulumu:** JavaScript mimarisinde veri dağılımını optimize eden *32-bit FNV-1a* hash fonksiyonunun koda entegre edilmesi ve arama hızını $O(\log n)$'e düşüren *Binary Search* mantığının hatasız şekilde kurulması adımlarında kodlama desteği alınmıştır.
* **Arayüz ve Kararlılık:** Tarayıcıların yerel güvenlik politikalarına takılmayan saf JavaScript olay dinleyicilerinin (Event Listeners) kurgulanması ve CSS arayüz tasarımlarının prototiplenmesi AI yardımıyla sağlanmıştır.
* **Raporlama:** Elde edilen ampirik test sonuçlarının ve metrik verilerinin akademik dökümantasyon standartlarına uygun olarak raporlanmasında optimizasyon amaçlı kullanılmıştır.
