ALGORİTMA: Hastane_Randevu_ve_Tahlil_Sistemi

BAŞLA

TEKRAR
    // 1. Hasta Kimlik Doğrulama
    YAZ "Lütfen TC Kimlik Numaranızı giriniz: "
    OKU tc_no

    EĞER tc_no geçerli değilse
        YAZ "Geçersiz TC Kimlik Numarası! Lütfen tekrar deneyin."
        DEVAM ET
    SON

    // 2. İşlem Seçimi
    YAZ "İşlem Seçiniz:"
    YAZ "1 - Randevu Al"
    YAZ "2 - Tahlil Sonucu Gör"
    YAZ "3 - Çıkış"
    OKU islem

    EĞER islem = 1 ise
        // ======================
        // MODÜL 1: RANDEVU ALMA
        // ======================

        YAZ "Mevcut Poliklinikler:"
        YAZ "1 - Dahiliye"
        YAZ "2 - Kardiyoloji"
        YAZ "3 - Göz"
        YAZ "4 - Kulak Burun Boğaz"
        YAZ "Poliklinik seçiniz (1-4): "
        OKU poliklinik_secim

        // Doktor Listesi
        EĞER poliklinik_secim = 1 ise
            doktor_listesi ← ["Dr. Ayşe Yılmaz", "Dr. Mehmet Demir"]
        DEĞİLSE EĞER poliklinik_secim = 2 ise
            doktor_listesi ← ["Dr. Selin Kaya", "Dr. Ali Koç"]
        DEĞİLSE EĞER poliklinik_secim = 3 ise
            doktor_listesi ← ["Dr. Elif Şen", "Dr. Murat Arslan"]
        DEĞİLSE
            doktor_listesi ← ["Dr. Zeynep Acar", "Dr. Cem Yıldız"]
        SON

        YAZ "Uygun doktorlar:"
        HER doktor İÇİN doktor_listesi YAP
            YAZ doktor
        SON

        YAZ "Randevu almak istediğiniz doktorun adını giriniz: "
        OKU doktor_secim

        uygun_saatler ← ["09:00", "09:30", "10:00", "10:30", "11:00"]
        YAZ "Uygun randevu saatleri:"
        HER saat İÇİN uygun_saatler YAP
            YAZ saat
        SON

        YAZ "Randevu saati seçiniz: "
        OKU saat_secim

        YAZ "Randevuyu onaylıyor musunuz? (E/H): "
        OKU onay

        EĞER onay = "E" ise
            YAZ "Randevunuz başarıyla oluşturuldu."
            YAZ "SMS gönderiliyor..."
            YAZ "Onay SMS'i başarıyla gönderildi."
        DEĞİLSE
            YAZ "Randevu iptal edildi."
        SON

    DEĞİLSE EĞER islem = 2 ise
        // ============================
        // MODÜL 2: TAHLİL SONUCU GÖR
        // ============================

        YAZ "Tahlil kayıtları kontrol ediliyor..."
        tahlil_var_mi ← RASTGELE(TRUE/FALSE)

        EĞER tahlil_var_mi = FALSE ise
            YAZ "Sistemde kayıtlı tahlil bulunamadı."
        DEĞİLSE
            sonuc_hazir_mi ← RASTGELE(TRUE/FALSE)
            EĞER sonuc_hazir_mi = TRUE ise
                YAZ "Tahlil Sonuçlarınız Hazır!"
                YAZ "- Kan Değeri: Normal"
                YAZ "- Şeker Değeri: Hafif yüksek"
                YAZ "- Kolesterol: Normal"
                
                YAZ "Sonuçları PDF olarak indirmek ister misiniz? (E/H): "
                OKU pdf_onay

                EĞER pdf_onay = "E" ise
                    YAZ "PDF hazırlanıyor..."
                    YAZ "Tahlil Sonucu PDF başarıyla indirildi."
                DEĞİLSE
                    YAZ "PDF indirilmeyecek."
                SON
            DEĞİLSE
                YAZ "Tahlil sonuçlarınız henüz hazır değil. Lütfen daha sonra tekrar deneyiniz."
            SON
        SON

    DEĞİLSE EĞER islem = 3 ise
        YAZ "Sistemden çıkış yapılıyor..."
        ÇIK
    DEĞİLSE
        YAZ "Geçersiz işlem seçimi!"
    SON

    // 3. Yeni İşlem Döngüsü
    YAZ "Başka bir işlem yapmak ister misiniz? (E/H): "
    OKU devam_onay

UNTIL devam_onay = "H"

YAZ "Teşekkür ederiz, iyi günler dileriz!"

BİTİR
