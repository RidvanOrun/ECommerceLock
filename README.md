# ECommerceLock

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
