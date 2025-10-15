

**GÖREV 1: ATM PARA ÇEKME SİSTEMİ PSEUDOCODE**

```pseudocode
BASLA

    // SABİT DEĞERLER
    SABİT DOGRU_PIN = "1234" // Gerçek sistemde veritabanından alınır
    SABİT GUNLUK_LIMIT = 5000 // Günlük çekim limiti
    SABİT PIN_HAKKI = 3

    // DEĞİŞKENLER
    DEGISKEN bakiye = 1500 // Örnek başlangıç bakiyesi
    DEGISKEN pin_giris_sayaci = 0
    DEGISKEN kart_bloke = YANLIS
    DEGISKEN cekilen_tutar = 0
    DEGISKEN gunluk_cekilen_toplam = 0
    DEGISKEN islem_devam = DOGRU
    DEGISKEN pin_dogru = YANLIS

    // 1. PIN DOĞRULAMA DÖNGÜSÜ
    DONGU pin_giris_sayaci < PIN_HAKKI VE pin_dogru == YANLIS
        
        YAZ "Lütfen kartınızı takınız."
        YAZ "Lütfen PIN kodunuzu giriniz:"
        OKU girilen_pin
        
        EGER girilen_pin == DOGRU_PIN ISE
            pin_dogru = DOGRU
            YAZ "PIN doğru. Hoş geldiniz."
        DEGILSE
            pin_giris_sayaci = pin_giris_sayaci + 1
            YAZ "Hatalı PIN. Kalan hakkınız: " + (PIN_HAKKI - pin_giris_sayaci)
            
            EGER pin_giris_sayaci == PIN_HAKKI ISE
                kart_bloke = DOGRU
                YAZ "3 hatalı PIN girişi. Kartınız bloke edilmiştir. Lütfen bankanızla iletişime geçin."
            BITEGER
            
        BITEGER
        
    BITDONGU // PIN DOĞRULAMA DÖNGÜSÜ SONU

    // 2. ANA İŞLEM DÖNGÜSÜ (PIN doğruysa ve kart bloke değilse)
    EGER pin_dogru == DOGRU VE kart_bloke == YANLIS ISE

        DONGU islem_devam == DOGRU
            
            YAZ "------------------------------------"
            YAZ "Lütfen yapmak istediğiniz işlemi seçiniz:"
            YAZ "1: Para Çekme"
            YAZ "2: Bakiye Sorgulama"
            YAZ "3: Çıkış"
            OKU secim

            EGER secim == 2 ISE
                // BAKİYE SORGULAMA
                YAZ "Hesap Bakiyeniz: " + bakiye + " TL"

            DEGILSE EGER secim == 1 ISE
                // PARA ÇEKME İŞLEMİ
                
                YAZ "Hesap Bakiyeniz: " + bakiye + " TL"
                YAZ "Günlük kalan çekim limitiniz: " + (GUNLUK_LIMIT - gunluk_cekilen_toplam) + " TL"
                YAZ "Çekmek istediğiniz tutarı (20 TL'nin katı) giriniz:"
                OKU cekilmek_istenen_tutar

                // TUTAR KONTROLÜ (20 TL KATLARI)
                EGER cekilmek_istenen_tutar MOD 20 != 0 ISE
                    YAZ "Hata: Lütfen 20 TL'nin katı bir tutar giriniz."
                
                // YETERSİZ BAKİYE KONTROLÜ
                DEGILSE EGER cekilmek_istenen_tutar > bakiye ISE
                    YAZ "Hata: Yetersiz bakiye. Bakiyeniz: " + bakiye + " TL"
                
                // GÜNLÜK LİMİT KONTROLÜ
                DEGILSE EGER (gunluk_cekilen_toplam + cekilmek_istenen_tutar) > GUNLUK_LIMIT ISE
                    YAZ "Hata: Günlük çekim limitinizi aşıyorsunuz. Kalan limit: " + (GUNLUK_LIMIT - gunluk_cekilen_toplam) + " TL"
                
                // BAŞARILI İŞLEM
                DEGILSE
                    bakiye = bakiye - cekilmek_istenen_tutar
                    gunluk_cekilen_toplam = gunluk_cekilen_toplam + cekilmek_istenen_tutar
                    YAZ "Lütfen paranızı alınız: " + cekilmek_istenen_tutar + " TL"
                    YAZ "Fişiniz hazırlanıyor..."
                    YAZ "Yeni bakiyeniz: " + bakiye + " TL"
                BITEGER
                
            DEGILSE EGER secim == 3 ISE
                // ÇIKIŞ
                islem_devam = YANLIS
            
            DEGILSE
                YAZ "Geçersiz seçim. Lütfen tekrar deneyiniz."
            BITEGER // SEÇİM KONTROL SONU

            // İŞLEM TEKRARI SEÇENEĞİ (Sadece Çıkış seçilmediyse veya kart bloke değilse sorulur)
            EGER islem_devam == DOGRU ISE
                YAZ "Başka bir işlem yapmak ister misiniz? (E/H)"
                OKU tekrar_secim
                
                EGER tekrar_secim == "H" VEYA tekrar_secim == "h" ISE
                    islem_devam = YANLIS
                BITEGER
            BITEGER
            
        BITDONGU // ANA İŞLEM DÖNGÜSÜ SONU

    BITEGER // PIN/KART BLOKE KONTROL SONU

    YAZ "İyi günler dileriz. Lütfen kartınızı almayı unutmayınız."

BITIR
```
