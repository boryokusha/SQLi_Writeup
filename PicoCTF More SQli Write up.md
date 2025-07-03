**Genel Bilgiler**:
200 Puan
Kategori : Web Exploitation

**Açıklama**:
Bu Websitedeki Bayrağı Bulabilecek Misin.
Gerekli detaylar makineyi başlattıktan sonra erişilebilinir olacaktır.

**İpuçları**:
-SQLiLite

**Çözüm**:

Flag : picoCTF{G3tting_5QL_1nJ3c7I0N_l1k3_y0u_sh0ulD_98236ce6}

Makineye Bağlandığınızda link üzerinden bir website ye bağlanıyoruz ve karşımıza bu sayfa çıkıyor. 

1.png

İlk olarak deneme için "Username:" ve "Password" kısımlarına "deneme" ve "deneme" olarak girişimizi sağlıyoruz. 

Ve karşımıza bu ekran çıkıyor :

![[2.png]]

Bu ekranda açık şekilde SQL query i görebiliyoruz bu bir güvenlik açığıdır ve işimizi bayağı bir şekilde kolaylaştırıyor. 
Eğer böyle bir ekran karşımıza çıkmasaydı Blind SQLi veya url de php erişimimiz olsaydı Out-Of-Band SQLi deneyebilirdik.

Elimizde Sql Query olduğu için bir önceki ekrana geri dönerek "Username:" ve "Password :" kısmına "' or 1=1;--" girişini yapabiliriz bu girişteki "or 1=1 " kısmı girişin tüm ihtimallerde doğru olmasını "--" kısmı ise query deki bundan sonraki kısımların yorum satırı olmasını sağlıyor.


Girişi yaptıktan sonra karşımıza böyle bir ekran geliyor :

![[3.png]]

Bu ekranda tüm tabloları listelemek için uygulamanın hangi database i ve hangi sürümü kullandığını öğrenmemiz lazım . Tabi ki bu bilgiyi ipucundan da alabilirdik fakat ben size ipucu elimizde olmasaydı nasıl bu bilgiye erişebileceğimizi göstericem .

Search çubuğuna :
`123' UNION SELECT 1, sqlite_version(), 3;--`
yazarak uygulamanın hangi SQL i ve hangi versiyonunu kullandığını öğrenebiliriz . Bu query i detaylı incelersek :

|Kısım|Açıklama|
|---|---|
|`123'`|Bu, muhtemelen bir WHERE koşulunda kullanılan bir kullanıcı girdi değeridir (örneğin: `WHERE id = '123'`). Burada `'` karakteri ile **açık SQL ifadesinden çıkış** yapılıyor.|
|`UNION SELECT`|SQL Injection tekniğinde kullanılır; mevcut sorguya **kendi SELECT ifademizi eklememizi** sağlar.|
|`1, sqlite_version(), 3`|Üç sütun döndüren bir sorgudur. İkinci sütun `sqlite_version()` fonksiyonunu çağırır ve **SQLite veritabanı sürümünü** döndürür.|
|`--`|SQL yorum satırı operatörüdür; sorgunun kalan kısmını iptal eder (örneğin kapanış `;` veya başka WHERE koşullarını).|

Karşımıza Çıkan Ekran :

![[4.png]]

Database hakkında bilgileri aldıktan sonra alttaki query i kullanarak tüm tabloları listeletebiliriz :
`123' UNION SELECT name, sql, null from sqlite_master;--`

Çıktımız :

![[5.png]]

Burada "more_table" flag column'unu görebiliriz.
Daha sonra ise "more_table" tablosunu listeletmemiz lazım. Bunun içinde 
`123' UNION SELECT flag, null, null from more_table;--`
bu query i kullanacağız.

![[6.png]]

Çıktımız bu şekilde flag açık bir şekilde gösteriliyor. 



