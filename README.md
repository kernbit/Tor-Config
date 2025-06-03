# Torrc Dosyası Nasıl Yapılandırılır? (aga için Rehber)

Tor ağı üzerinden güvenli ve hızlı bir bağlantı kurmak için `torrc` dosyasını doğru bir şekilde yapılandırmak oldukça önemlidir. Bu rehberde, hız, gizlilik ve güvenlik odaklı örneklerle insan dostu bir anlatım sunuyorum.

## 1. torrc Dosyası Nedir?

`torrc`, Tor istemcisinin davranışlarını belirleyen ana yapılandırma dosyasıdır. Genellikle aşağıdaki yollarda bulunur:

- **Linux:** `/etc/tor/torrc` veya `~/.tor/torrc`
- **Windows:** `C:\Users\<KullanıcıAdı>\AppData\Roaming\tor\torrc`
- **macOS:** `/usr/local/etc/tor/torrc` veya `~/.tor/torrc`

## 2. Temel Ayarlar

Aşağıda, günlük kullanım için önerilen bazı temel ayarlar yer alıyor:

```bash
SocksPort 9050
Log notice file /var/log/tor/notices.log
DataDirectory /var/lib/tor
```

- `SocksPort`: Tor’un dinleyeceği port (varsayılan: 9050)
- `Log`: Log dosyasının yolu ve seviyesi
- `DataDirectory`: Tor’un veri depoladığı dizin

## 3. Hız için Yapılandırma

Tor ağı doğası gereği yavaştır fakat bazı ayarlamalarla hız optimize edilebilir. Çok yüksek hız beklemeyin, fakat aşağıdaki ayarları deneyebilirsiniz:

```bash
# En az 3, en fazla 5 devre oluşturur
NumEntryGuards 3

# Hızlı ve güvenilir relay'ler için minimum bant genişliği (ör: 3 MB/s)
EntryNodes {us},{de},{nl}
ExitNodes {us},{de},{nl}
StrictNodes 1

# Devre yenileme sıklığı (daha uzun devreler için daha az yenileme)
MaxCircuitDirtiness 600
```

**Not:** `EntryNodes` ve `ExitNodes` ülke kodları ile sadece belirli ülkelerin relay'lerini kullanabilirsiniz. Bu ayar hız için bazen avantaj sağlar fakat anonimlikten ödün verebilir.

## 4. Gizlilik ve Güvenlik için Ayarlar

Kendi güvenliğiniz ve gizliliğiniz için şu ayarları gözden geçirin:

```bash
# Her yeni bağlantıda yeni devre
IsolateSOCKSAuth 1

# DNS sızıntılarını engelle
DNSPort 5353
AutomapHostsOnResolve 1

# IPv6 yerine sadece IPv4
ClientUseIPv6 0

# Yerel saat dilimi bilgisini gizle
ClientTransportPlugin obfs4 exec /usr/bin/obfs4proxy
```

- `IsolateSOCKSAuth`: Her site için farklı devre kullanılır, parmak izi azaltılır.
- `ClientTransportPlugin`: obfs4 gibi pluggable transport’lar ile sansür aşılabilir.
- `AutomapHostsOnResolve`: .onion dışı adresler için local DNS kullanılmaz.

## 5. Sadece .onion Sitelerine Erişim (Ekstra Gizlilik)

Sadece .onion sitelerine erişmek istiyorsanız:

```bash
ExitNodes {}
StrictNodes 1
```

## 6. Kendi Bridge (Köprü) Eklemek

Ağınızda Tor engelliyse, bridge kullanabilirsiniz:

```bash
UseBridges 1
Bridge obfs4 <bridge_adresi>:<port> <fingerprint> cert=<cert> iat-mode=0
```

Bridge bilgilerini https://bridges.torproject.org/ adresinden alabilirsiniz.

## 7. Tam Örnek torrc

Aşağıda hız ve gizlilik dengesini gözeten örnek bir `torrc` dosyası bulabilirsiniz:

```bash
SocksPort 9050
Log notice file /var/log/tor/notices.log
DataDirectory /var/lib/tor
NumEntryGuards 3
EntryNodes {us},{de},{nl}
ExitNodes {us},{de},{nl}
StrictNodes 1
MaxCircuitDirtiness 600
IsolateSOCKSAuth 1
ClientUseIPv6 0
UseBridges 1
Bridge obfs4 1.2.3.4:443 1234567890ABCDEF1234567890ABCDEF12345678 cert=xyz iat-mode=0
```

## 8. Ekstra İpuçları

- **Güncel Kalın:** Tor’u ve relay listelerini güncel tutun.
- **Kendi VPN’inizle Kullanın:** Ekstra anonimlik için Tor’u bir VPN ile birlikte kullanabilirsiniz.
- **Kendi Bridge’inizi Kurun:** Ülkenizde Tor erişimi kısıtlıysa, kendi bridge’inizi oluşturun.
- **Logları Temizleyin:** Gizliliğiniz için log dosyalarını periyodik olarak temizleyin.

## 9. Dikkat Edilmesi Gerekenler

- Çok fazla özelleştirme, anonimlikten ödün verebilir.
- Sadece güvenilir kaynaklardan bridge ve relay bilgisi alın.
- Bazı ayarlar Tor’un çalışmasını engelleyebilir; değişikliklerden sonra Tor’u yeniden başlatmayı unutmayın.

---

**Daha Fazla Bilgi:**  
- https://2019.www.torproject.org/docs/tor-manual.html.en  
- https://tb-manual.torproject.org/tr/bridges/

---

Bu rehberde temel ve orta seviye `torrc` ayarlarına değindik. Sorunuz veya eklemek istediğiniz bir şey olursa belirtmekten çekinmeyin!
