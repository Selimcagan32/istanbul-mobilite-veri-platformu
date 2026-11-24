# 0001 Mimari: batch, streaming ve tablo formatı

Durum: Önerildi
Tarih: 23 Kasım 2026

## Bağlam
Platform iki tür veri alır: ayda bir gelen toplu ulaşım ve trafik CSV dosyaları ve anlık kart geçiş olayları. Aylık hat raporu ayın kapanışından sonra hazırlanır ve bir gün gecikmeyi kabul eder. Durak yoğunluğu ise en geç 5 dakika içinde güncellenmelidir.

## Karar
Aylık dosyalar batch olarak bronze, silver ve gold katmanlarından geçer; geçiş olayları Kafka ve Structured Streaming ile aynı kataloğa yazılır; tablo formatı Iceberg, katalog Polaris olur.

## Gerekçe
Aylık rapor bir gün gecikmeyi kabul eder, bu yüzden batch yeterlidir.
Durak yoğunluğu en geç 5 dakikada güncellenmelidir, bu yüzden streaming gerekir.
DuckDB ile yazılan bronze tablosu aynı katalog üzerinden Spark ile de okunabilir.
Elenen seçenek 1: Her şeyi streaming ile işlemek. Akış işi gün boyu çalışır, aylık rapor için gereksiz maliyet getirir.
Elenen seçenek 2: Her şeyi batch ile işlemek. Durak yoğunluğunun 5 dakika hedefini karşılayamaz.

## Sonuçlar
İş: Batch ve streaming için iki ayrı akış kurulup yönetilecek.
Maliyet: Streaming işi gün boyu çalıştığı için sürekli kaynak kullanır.
Risk: Kafka veya streaming işi durursa durak yoğunluğu verisi gecikir.