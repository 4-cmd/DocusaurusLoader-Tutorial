### `restrict_to_same_domain`

Tarama işleminin başlangıç URL'siyle aynı alan adı içerisinde kalmasını sağlar.

`True` değeri tercih edilmiştir çünkü farklı alan adlarına yönelerek kapsam dışı sayfaların taranmasını önler.

Bunun dezavantajı, gerekli dokümanların farklı bir alan adında bulunması durumunda bu sayfalara erişilememesidir.

------------------------------------------------------------------------

### `continue_on_failure`

Bir sayfanın yüklenmesi sırasında hata oluştuğunda tarama işleminin devam etmesini sağlar.

`True` değeri tercih edilmiştir çünkü tek bir sayfadaki erişim hatasının diğer sayfaların yüklenmesini engellemesini önler.

Bunun dezavantajı, bazı sayfalar yüklenemese bile işlemin devam etmesi nedeniyle eksik dokümanların fark edilmesinin zorlaşabilmesidir.

------------------------------------------------------------------------

### `show_progress`

Doküman yükleme işlemi sırasında ilerleme durumunun görüntülenmesini sağlar.

`True` değeri tercih edilmiştir çünkü çok sayıda sayfanın tarandığı işlemlerde sürecin takip edilmesini kolaylaştırır.

Bunun dezavantajı, terminal çıktısında ek ilerleme bilgileri oluşturmasıdır.

------------------------------------------------------------------------

### `encoding`

Web sayfalarından alınan metinlerin hangi karakter kodlaması kullanılarak çözümleneceğini belirtir.

`utf-8` değeri tercih edilmiştir çünkü Türkçe karakterler ve diğer Unicode karakterleri için yaygın olarak kullanılan bir kodlama standardıdır.

Sunucunun farklı bir karakter kodlaması kullanması durumunda bazı karakterlerin hatalı görüntülenmesine neden olabilir.

------------------------------------------------------------------------

### `custom_html_tags`

Web sayfasından içerik çıkarılırken dikkate alınacak HTML etiketlerini belirtir.

`["main", "article"]` değerleri tercih edilmiştir çünkü bu etiketler genellikle sayfanın ana içerik bölümlerini barındırır.

Ancak bazı web sitelerinde asıl içerik farklı HTML etiketleri içerisinde bulunabileceğinden içeriklerin eksik alınmasına neden olabilir.

------------------------------------------------------------------------

### `filter_urls`

Tarama işlemi sırasında tüm siteyi değil sadece ilgili bölüme filtreleme uygulanacak URL adreslerini belirtir.

Belirtilen URL, LangChain dokümantasyonunun Fleet Templates bölümündeki sayfaları hedeflemek amacıyla kullanılmıştır.

Filtreleme kapsamının yanlış belirlenmesi, istenen sayfaların tarama dışında kalmasına neden olabilir.

------------------------------------------------------------------------

### `parsing_function`

`parse_page` fonksiyonu, yüklenen HTML içeriğindeki gereksiz elementleri temizleyerek LLM kullanımına uygun düz metin oluşturmak amacıyla yükleyici nesnesine atanır.

Bu atama, HTML içeriğinin özel bir ayrıştırma işleminden geçirilmesini sağlar.

Temizleme işleminin kapsamı gereğinden geniş tutulursa LLM için gerekli içerikler de silinebilir.

------------------------------------------------------------------------

### ❓ `parsing_function` Parametresi Nasıl Aktarılmalı

Geliştirme aşamasında gerçekleştirilen testler sonucunda, `parsing_function` parametresinin doğrudan `DocusaurusLoader` constructor metoduna aktarılması sırasında aşağıdaki hatayla karşılaşılmıştır:

```bash
TypeError: SitemapLoader.__init__() got multiple values for keyword argument 'parsing_function'
```

#### 🔐 Nasıl Çözüldü?

Yapılan incelemeler sonucunda, `parsing_function` parametresinin constructor üzerinden aktarılması yerine, yükleyici nesnesi oluşturulduktan sonra ilgili özelliğe atanmasıyla sorun giderilmiştir.

Bunun için aşağıdaki yöntem kullanılmıştır

```python
loader = DocusaurusLoader(
    url=url,
    restrict_to_same_domain=restrict_to_same_domain,
    continue_on_failure=continue_on_failure,
    show_progress=show_progress,
    encoding=encoding,
    custom_html_tags=custom_html_tags,
    filter_urls=filter_urls
)

# HTML içeriğini temizlemek için özel ayrıştırma fonksiyonunu atar.
loader.parsing_function = parse_page

return loader
```
