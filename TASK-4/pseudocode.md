ALGORİTMA: Universite_Ders_Kayit_Sistemi

BAŞLA

/********************
  Başlangıç / Login
********************/
TEKRAR
    YAZ "Öğrenci No: "
    OKU ogr_no
    YAZ "Şifre: "
    OKU sifre

    EĞER dogrula_kullanici(ogr_no, sifre) = FALSE İSE
        YAZ "Giriş başarısız. Tekrar deneyin."
        DEVAM ET  // login döngüsü
    DEĞİLSE
        ogrenci ← yukle_ogrenci_verisi(ogr_no)
        // ogrenci yapı: {isim, ogr_no, GPA, tamamlanan_dersler[], 
        // mevcut_kayitlar[{ders,kredi,gun,saat_araligi}], toplam_kredi}
        ÇIK
SON UNTIL dogrula_kullanici(ogr_no,sifre) = TRUE

/********************
  Ana Menü & Döngü
********************/
TEKRAR
    YAZ ""
    YAZ "Ana Menü:"
    YAZ "1 - Ders Listesini Görüntüle"
    YAZ "2 - Ders Ekle/Çıkar"
    YAZ "3 - Kayıt Özetini Gör ve Onayla"
    YAZ "4 - Çıkış"
    YAZ "Seçiminiz: "
    OKU secim

    EĞER secim = 1 İSE
        // Ders listesi göster (döngü halinde gezinme/filtre olabilir)
        ders_listesi ← getir_tum_dersler()
        HER ders İÇİN ders_listesi YAP
            YAZ ders.kod, " - ", ders.ad, " (Kredi:", ders.kredi, ") Kontenjan:", ders.kontenjan, "/", ders.max_kontenjan
        SON

    DEĞİLSE EĞER secim = 2 İSE
        // Ders Ekle/Çıkar Döngüsü
        TEKRAR
            YAZ ""
            YAZ "Ders Ekle/Çıkar Menüsü:"
            YAZ "1 - Ders Ekle"
            YAZ "2 - Ders Çıkar"
            YAZ "3 - Geri Ana Menüye"
            YAZ "Seçiminiz: "
            OKU ekcekim_secim

            EĞER ekcekim_secim = 1 İSE
                YAZ "Eklemek istediğiniz dersin kodunu girin: "
                OKU ders_kod
                ders ← getir_ders_by_kod(ders_kod)

                EĞER ders = YOK İSE
                    YAZ "Geçersiz ders kodu."
                    DEVAM ET
                SON

                // --- 1) Kontenjan kontrolü ---
                KALAN_KONTENJAN ← ders.max_kontenjan - ders.kontenjan
                EĞER KALAN_KONTENJAN ≤ 0 İSE
                    YAZ "Dersin kontenjanı dolu."
                    DEVAM ET
                SON

                // --- 2) Ön-koşul kontrolü ---
                eksik_onkosullar ← []
                HER onkod İÇİN ders.onkosul_listesi YAP
                    EĞER onkod NOT IN ogrenci.tamamlanan_dersler İSE
                        Ekle(eksik_onkosullar, onkod)
                    SON
                SON

                EĞER uzunluk(eksik_onkosullar) > 0 İSE
                    YAZ "Bu dersi almak için eksik ön-koşul ders(ler)i var: ", eksik_onkosullar
                    DEVAM ET
                SON

                // --- 3) Zaman çakışması kontrolü ---
                çakışma_var ← FALSE
                HER mevcut_ders İÇİN ogrenci.mevcut_kayitlar + geçici_eklenen_dersler YAP
                    EĞER zaman_cakisiyor(ders.zaman, mevcut_ders.zaman) = TRUE İSE
                        çakışma_var ← TRUE
                        çakışan_ders ← mevcut_ders
                        KIR
                    SON
                SON

                EĞER çakışma_var = TRUE İSE
                    YAZ "Zaman çakışması tespit edildi: Seçilen ders ", ders.kod, " ile ", çakışan_ders.kod, " çakışıyor."
                    DEVAM ET
                SON

                // --- 4) Kredi limiti kontrolü ---
                yeni_toplam_kredi ← ogrenci.toplam_kredi + ders.kredi + toplam_kredi_gecici_eklenenler()
                EĞER yeni_toplam_kredi > 35 İSE
                    YAZ "Kredi limiti aşılıyor. (Yeni toplam kredi: ", yeni_toplam_kredi, ") Maksimum: 35"
                    DEVAM ET
                SON

                // --- 5) Danışman onayı gerek kontrolü (GPA < 2.5 ise) ---
                EĞER ogrenci.GPA < 2.5 İSE
                    YAZ "GPA'nız ", ogrenci.GPA, ". Bu ders için danışman onayı gereklidir."
                    onay ← talep_danisman_onayi(ogrenci.ogr_no, ders.kod)
                    EĞER onay = "ONAYLANDI" İSE
                        // devam et
                        YAZ "Danışman onayı alındı."
                    DEĞİLSE
                        YAZ "Danışman onayı alınamadı. Ders eklenemiyor."
                        DEVAM ET
                    SON
                SON

                // --- Tüm kontroller geçti: Ders eklenir (geçici olarak) ---
                ekle_gecici_kayit(ogrenci, ders)
                YAZ "Ders eklendi (geçici). Kod:", ders.kod, " Ad:", ders.ad
                DEVAM ET

            DEĞİLSE EĞER ekcekim_secim = 2 İSE
                YAZ "Çıkarmak istediğiniz dersin kodunu girin: "
                OKU ders_kod_cikar
                EĞER ders_kod_cikar NOT IN ogrenci.mevcut_kayitlar_kodları VE NOT IN geçici_eklenenler_kodları İSE
                    YAZ "Belirtilen ders kayıtlarınızda bulunmuyor."
                    DEVAM ET
                SON

                // Ders çıkarma işlemi
                cikar_ders(ogrenci, ders_kod_cikar) // geçici veya mevcut kayıttan çıkar
                YAZ "Ders çıkarıldı:", ders_kod_cikar
                DEVAM ET

            DEĞİLSE EĞER ekcekim_secim = 3 İSE
                KIR  // Ders Ekle/Çıkar döngüsünden çık -> ana menü
            DEĞİLSE
                YAZ "Geçersiz seçim."
            SON

        SON UNTIL ekcekim_secim = 3

    DEĞİLSE EĞER secim = 3 İSE
        // Kayıt Özeti Göster ve Onayla
        geçici_eklenenler ← getir_gecici_eklenenler(ogrenci)
        çıkartılanlar ← getir_gecici_cikartilanlar(ogrenci)
        final_toplam_kredi ← ogrenci.toplam_kredi + toplam_kredi(gecici_eklenenler) - toplam_kredi(çıkartılanlar)

        YAZ "----- KAYIT ÖZETİ -----"
        YAZ "Öğrenci: ", ogrenci.isim, " (No: ", ogrenci.ogr_no, ")"
        YAZ "Mevcut Kayıtlar:"
        HER d İÇİN ogrenci.mevcut_kayitlar YAP
            YAZ d.kod, " - ", d.ad, " (", d.kredi, " kredi)"
        SON
        YAZ "Eklenecek Dersler:"
        HER d İÇİN geçici_eklenenler YAP
            YAZ d.kod, " - ", d.ad, " (", d.kredi, " kredi)"
        SON
        YAZ "Çıkarılacak Dersler:"
        HER d İÇİN çıkartılanlar YAP
            YAZ d.kod, " - ", d.ad, " (", d.kredi, " kredi)"
        SON
        YAZ "Yeni Toplam Kredi: ", final_toplam_kredi

        YAZ "Kayıtları onaylıyor musunuz? (E/H): "
        OKU onay_genel

        EĞER onay_genel = "E" İSE
            // Kalıcı kayıt işlemleri
            UYGULA_gecici_degısklıklar(ogrenci)
            YAZ "Kayıtlar başarıyla güncellendi."
            // opsiyonel: bildirim gönder
            YAZ "Bilgilendirmeniz için e-posta/SMS gönderiliyor..."
            YAZ "Bildirim gönderildi."
        DEĞİLSE
            YAZ "Kayıt işlemi onaylanmadı. Değişiklikler beklemede."
        SON

    DEĞİLSE EĞER secim = 4 İSE
        YAZ "Sistemden çıkılıyor..."
        // isterse otomatik bekleyen onayları iptal et
        ÇIK
    DEĞİLSE
        YAZ "Geçersiz seçim."
    SON

    // Ana menüde döngü devam eder

SON UNTIL secim = 4

YAZ "ÇIKIŞ - İyi günler!"
BİTİR

/********************
  Yardımcı Fonksiyonlar (açıklama)
********************/

FONKSİYON dogrula_kullanici(ogr_no, sifre)
    // veritabanı kontrolü, hash karşılaştırma vb.
    DÖNÜŞ TRUE veya FALSE
SON

FONKSİYON yukle_ogrenci_verisi(ogr_no)
    // öğrenci bilgilerini DB'den çeker: GPA, tamamlanan dersler, mevcut kayıtlar, toplam_kredi
    DÖN ogrenci_yapisi
SON

FONKSİYON zaman_cakisiyor(zaman1, zaman2)
    // zaman1 ve zaman2: {gunler[], baslangic_saat, bitis_saat} biçiminde
    // eğer aynı gün ve saat aralıkları çakışıyorsa TRUE döndür
    DÖNÜŞ TRUE/FALSE
SON

FONKSİYON talep_danisman_onayi(ogr_no, ders_kod)
    // Danışmana bildirim gönder, onay bekle. Simülasyon: ONAYLANDI / REDDEDILDI / BEKLEME
    DÖN "ONAYLANDI" veya "RED" veya "BEKLEME"
SON

FONKSİYON ekle_gecici_kayit(ogrenci, ders)
    // geçici eklenen ders listesine ekle, kontenjan geçici olarak azaltılabilir
SON

FONKSİYON cikar_ders(ogrenci, ders_kod)
    // mevcut veya geçici kayıtlardan sil
SON

FONKSİYON UYGULA_gecici_degısklıklar(ogrenci)
    // veritabanında değişiklikleri kalıcı hale getir: kontenjan güncelle, öğrenci kayıtlarını güncelle, kredi güncelle
SON

