BASLA

    // DEĞİŞKEN TANIMLAMALARI
    DEGISKEN kullanici_giris_yapildi = YANLIS
    DEGISKEN sepet = BOS_LISTE
    DEGISKEN toplam_tutar = 0
    DEGISKEN indirim_kodu_uygulandi = YANLIS
    DEGISKEN kargo_ucreti = 0
    DEGISKEN indirim_orani = 0.10 // Örnek %10 indirim
    DEGISKEN min_siparis_tutar = 50
    DEGISKEN ucretsiz_kargo_limit = 200

    // 1. KULLANICI GİRİŞ KONTROLÜ (KOŞUL)
    YAZ "E-ticaret sistemine hoş geldiniz."
    YAZ "Lütfen giriş yapınız (G) veya devam ediniz (D):"
    OKU secim_giris

    EGER secim_giris == "G" ISE
        YAZ "Kullanıcı adı ve şifre giriniz:"
        OKU kullanici_adi
        OKU sifre
        // Basit bir doğrulama varsayımı
        EGER kullanici_adi == "demo" VE sifre == "123" ISE
            kullanici_giris_yapildi = DOGRU
            YAZ "Giriş başarılı. Hoş geldiniz, " + kullanici_adi
        DEGILSE
            YAZ "Hatalı giriş. Misafir olarak devam ediyorsunuz."
        BITEGER
    DEGILSE
        YAZ "Misafir olarak devam ediliyor."
    BITEGER


    // 2. ÜRÜN EKLEME VE SEPET YÖNETİMİ (DÖNGÜ)
    DEGISKEN alisveris_devam = DOGRU
    DONGU alisveris_devam == DOGRU

        YAZ "--- ÜRÜN GEZİNME VE EKLEME ---"
        YAZ "A: Ürün Ekle, B: Sepeti Görüntüle/Düzenle, C: Ödeme Aşamasına Geç"
        OKU ana_secim

        EGER ana_secim == "A" ISE
            // ÜRÜN EKLEME ADIMI
            YAZ "Hangi kategoriye göz atmak istersiniz? (Elektronik, Giyim, Kitap, vb.)"
            OKU kategori_secim // (Kategoriler arası gezinme simülasyonu)

            YAZ "Eklemek istediğiniz ürün ID'sini ve adetini giriniz:"
            OKU urun_id
            OKU urun_adet

            // STOK KONTROLÜ (KOŞUL)
            DEGISKEN mevcut_stok = 10 // Örnek stok
            EGER urun_adet > mevcut_stok ISE
                YAZ "Hata: Yeterli stok yok. Mevcut: " + mevcut_stok
            DEGILSE
                // Ürünü sepete ekle
                DEGISKEN urun_fiyat = 75.0 // Örnek fiyat
                DEGISKEN eklenen_urun_toplam = urun_fiyat * urun_adet
                SEPETE_EKLE(sepet, urun_id, urun_adet, eklenen_urun_toplam)
                toplam_tutar = toplam_tutar + eklenen_urun_toplam
                YAZ "Ürün sepete eklendi. Sepet Toplamı: " + toplam_tutar + " TL"
            BITEGER

        DEGILSE EGER ana_secim == "B" ISE
            // SEPET GÖRÜNTÜLEME VE DÜZENLEME (DÖNGÜ)
            DEGISKEN sepet_duzenleme_devam = DOGRU
            DONGU sepet_duzenleme_devam == DOGRU
                YAZ "--- SEPETİNİZ ---"
                YAZ sepet // Sepet içeriğini yaz
                YAZ "D: Düzenle (Ürün Sil/Adet Değiştir), H: Ana Menüye Dön"
                OKU duzenleme_secim
                
                EGER duzenleme_secim == "D" ISE
                    YAZ "Düzenleme işlemi yapıldı. Sepet toplamı yeniden hesaplanıyor."
                    // Gerçek sistemde sepet yeniden hesaplanır. Basitçe güncelleyelim:
                    toplam_tutar = YENIDEN_HESAPLA(sepet) 
                DEGILSE EGER duzenleme_secim == "H" ISE
                    sepet_duzenleme_devam = YANLIS
                BITEGER
            BITDONGU
        
        DEGILSE EGER ana_secim == "C" ISE
            alisveris_devam = YANLIS
        
        DEGILSE
            YAZ "Geçersiz seçim. Lütfen tekrar deneyin."
        BITEGER

    BITDONGU // ÜRÜN EKLEME/SEPET DÖNGÜSÜ SONU


    // 3. ÖDEME SÜRECİ KONTROLLERİ
    YAZ "--- ÖDEME AŞAMASI ---"

    // MINIMUM SİPARİŞ KONTROLÜ (KOŞUL)
    EGER toplam_tutar < min_siparis_tutar ISE
        YAZ "Hata: Sepet tutarı minimum sipariş limiti (" + min_siparis_tutar + " TL) altındadır."
        GIT_SEPET_DUZENLE // Geri adım atmak yerine sonlandırıyoruz
        BITIR
    BITEGER


    // İNDİRİM KODU UYGULAMA (KOŞUL)
    YAZ "İndirim kodunuz var mı? (E/H)"
    OKU indirim_soru
    EGER indirim_soru == "E" ISE
        YAZ "İndirim kodunu giriniz:"
        OKU indirim_kodu
        EGER indirim_kodu == "INDIRIM10" ISE // Örnek kod
            indirim_tutari = toplam_tutar * indirim_orani
            toplam_tutar = toplam_tutar - indirim_tutari
            indirim_kodu_uygulandi = DOGRU
            YAZ "İndirim uygulandı. İndirim Tutarı: " + indirim_tutari + " TL"
            YAZ "Güncel Toplam: " + toplam_tutar + " TL"
        DEGILSE
            YAZ "Geçersiz indirim kodu."
        BITEGER
    BITEGER


    // KARGO ÜCRETİ HESAPLAMA (KOŞUL)
    EGER toplam_tutar >= ucretsiz_kargo_limit ISE
        kargo_ucreti = 0
        YAZ "Ücretsiz Kargo kazandınız!"
    DEGILSE
        kargo_ucreti = 15.0 // Örnek kargo ücreti
        YAZ "Kargo Ücreti: " + kargo_ucreti + " TL"
    BITEGER

    DEGISKEN odenecek_toplam = toplam_tutar + kargo_ucreti
    YAZ "ÖDENECEK SON TUTAR: " + odenecek_toplam + " TL"


    // ÖDEME YÖNTEMİ SEÇİMİ (KOŞUL)
    YAZ "--- ÖDEME YÖNTEMİ SEÇİMİ ---"
    YAZ "Lütfen ödeme yöntemi seçiniz (1: Kredi Kartı, 2: Kapıda Ödeme):"
    OKU odeme_secim

    EGER odeme_secim == 1 ISE
        YAZ "Kredi kartı bilgileri giriliyor ve ödeme bankaya yönlendiriliyor..."
        DEGISKEN odeme_basarili = DOGRU // Basarılı varsayalım
    DEGILSE EGER odeme_secim == 2 ISE
        YAZ "Kapıda ödeme seçildi. Ek ücret (varsa) uygulanabilir."
        odeme_basarili = DOGRU
    DEGILSE
        YAZ "Hata: Geçersiz ödeme yöntemi seçimi."
        GIT_ODEME_SECIMI_TEKRARLA // Geri adım atmak yerine sonlandırıyoruz
        odeme_basarili = YANLIS
    BITEGER

    
    // SİPARİŞ ONAYI
    EGER odeme_basarili == DOGRU ISE
        YAZ "Ödeme başarılı! Siparişiniz onaylanmıştır."
        YAZ "Sipariş Detayları: Toplam Ürün: " + toplam_tutar + " TL, Kargo: " + kargo_ucreti + " TL, Genel Toplam: " + odenecek_toplam + " TL"
        SIPARIS_KAYDET(sepet, odenecek_toplam)
    DEGILSE
        YAZ "Sipariş işlemi başarısız. Lütfen tekrar deneyiniz."
    BITEGER

BITIR
