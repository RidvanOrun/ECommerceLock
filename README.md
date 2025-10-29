# LOCK YAPISI

Veritabanı sistemlerinde, birden fazla kullanıcının veya işlemin aynı anda aynı verilere erişmesi durumunda, veri tutarlılığı ve bütünlüğünü korumak için **lock** mekanizmaları kullanılır.
Bu mekanizmalar iki başlık altında incelenir: **Optimistic Lock** ve **Pessimistic Lock**.

---

## Optimistic Lock

**Tanım:**  
Optimistic Lock, aynı veriye birden fazla işlem eriştiğinde veri tutarlılığını sağlamak için kullanılır.  

**Kullanım Alanları:**  
E-ticarette en sık **stok güncellemeleri** veya **bakiye işlemlerinde** tercih edilir. Genelde **versionlama** mantığı ile uygulanır. 
Böylece aynı anda yapılan işlemlerden yalnızca biri başarılı olur, diğeri concurrency hatası alır.

**Entity Framework ile Yönetimi:**  
- `[ConcurrencyCheck]` ile belirli alanlarda eşzamanlı değişiklikleri kontrol eder,  
- `[Timestamp]` veya `RowVersion` alanı ile satır düzeyinde otomatik versiyon takibi yapar.

**Örnek Senaryo:**  
Stok sayısı 1 olan bir ürün var. Ali ürünü sepete ekleyip satın alıyor. Aynı anda Ayşe de sepete ekliyor. Ali işlemi bitirip stok 0’a düşürdüğünde, Ayşe hâlâ stok 1 sanıyor.
Eğer Optimistic Lock yoksa Ayşe’nin işlemi de geçer ve sistem negatif stok veya fazla satış hatası yapar.
Optimistic Lock sayesinde Ayşe’nin işlemi reddedilir ve veri tutarlılığı korunur. Ayşenin sorgusu veritabanına gittiğinde **version numarasının farklı olduğunu fark ederek hata verir**.

---

## Pessimistic Lock

**Tanım:**  
Pessimistic Lock, veri çakışmasını baştan engelleyen bir kilitleme yöntemidir. 
Bir kayıt üzerinde işlem başlatıldığında, o kayıt diğer işlemler tarafından değiştirilemez hale gelir.  

**Kullanım Alanları:**  
E-ticarette genelde **stok** veya **ödeme** gibi kritik senaryolarda, aynı anda iki işlemin aynı veriyi güncellememesi için kullanılır. 
Ancak performans maliyeti yüksek olduğu için genellikle sadece çok kritik işlemlerde tercih edilir.

**Örnek Senaryo:**  
Stok sayısı 1 olan bir ürünü Ali satın almak istediğinde sistem o ürünü kilitler. Bu sırada Ayşe de aynı ürünü almak isterse, Ali’nin işlemi bitene kadar bekler.
Ali işlemini tamamlayınca kilit kalkar, stok güncellenir ve Ayşe’nin işlemi devam eder; ancak stok tükendiği için işlem başarısız olur.

## 🧩 Race Condition, Lock ve Transaction İlişkisi

**Race Condition**, derin bir konudur fakar basit olarak; birden fazla işlemin aynı anda aynı veriye erişmesi sonucu ortaya çıkan ve veri tutarsızlığına yol açan bir durumdur.  
Örneğin, stokta yalnızca **bir ürün** kaldığında iki kullanıcının aynı anda satın alma işlemi başlatması, sistemin hatalı şekilde **iki satış** yapmasına neden olabilir.

Bu tür durumlarda **Optimistic Lock** ve **Pessimistic Lock** yapıları, Race Condition’a karşı etkili çözüm mekanizmaları olarak kullanılabilir.  
Lock yapıları, aynı veriye eşzamanlı erişimi kontrol ederek **veri bütünlüğünü (data integrity)** sağlar.

Ayrıca **Transaction** yapısı da Race Condition riskini azaltmak için tercih edilir.  
Transaction, bir işlem dizisinin **tamamının ya da hiçbirinin** uygulanmasını garanti eder.  
Örneğin, stokta yeterli ürün yoksa transaction işlemi **iptal eder** ve veri bütünlüğünü korur.
