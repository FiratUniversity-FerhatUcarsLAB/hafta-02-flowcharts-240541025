ALGORITMA: Akilli_Ev_Guvenlik_Sistemi

BAŞLA

// Sistem sürekli çalışır
DO
    EĞER sistem_aktif() = FALSE İSE
        YAZ "Sistem pasif. Bekleniyor..."
        BEKLE(5 saniye)
        DEVAM ET
    SON

    // Sensör verilerini oku
    hareket ← oku_hareket_sensoru()
    kapi ← oku_kapi_sensoru()
    pencere ← oku_pencere_sensoru()
    kamera ← oku_kamera_sensoru()

    // Durum analizi
    tehdit_seviyesi ← 0

    EĞER hareket = ALGILANDI İSE
        tehdit_seviyesi ← tehdit_seviyesi + 1
        kamera_aktif_et()
    SON

    EĞER kapi = AÇIK İSE
        tehdit_seviyesi ← tehdit_seviyesi + 1
    SON

    EĞER pencere = AÇIK İSE
        tehdit_seviyesi ← tehdit_seviyesi + 1
    SON

    // Yanlış alarm kontrolü
    EĞER ev_sahibi_evde_mi() = TRUE İSE
        YAZ "Ev sahibi evde, yanlış alarm olasılığı yüksek."
        tehdit_seviyesi ← 0
    SON

    // Alarm seviyesi belirleme
    EĞER tehdit_seviyesi = 1 İSE
        alarm_seviyesi ← "DÜŞÜK"
    DEĞİLSE EĞER tehdit_seviyesi = 2 İSE
        alarm_seviyesi ← "ORTA"
    DEĞİLSE EĞER tehdit_seviyesi ≥ 3 İSE
        alarm_seviyesi ← "YÜKSEK"
    DEĞİLSE
        alarm_seviyesi ← "YOK"
    SON

    // Alarm ve bildirim işlemleri
    EĞER alarm_seviyesi ≠ "YOK" İSE
        YAZ "Alarm Seviyesi: ", alarm_seviyesi
        siren_calistir(alarm_seviyesi)
        gonder_bildirimler(alarm_seviyesi)
    SON

    // Alarm sıfırlama kontrolü
    EĞER reset_komutu_geldi_mi() = TRUE İSE
        durdur_siren()
        YAZ "Alarm sıfırlandı. Sistem izleme moduna döndü."
    SON

    BEKLE(3 saniye)  // sensör döngü aralığı

WHILE DOĞRU  // Sonsuz döngü

BİTİR


// Yardımcı Fonksiyonlar

FONKSIYON sistem_aktif()
    DÖN TRUE veya FALSE
SON

FONKSIYON oku_hareket_sensoru()
    DÖN "ALGILANDI" veya "YOK"
SON

FONKSIYON oku_kapi_sensoru()
    DÖN "AÇIK" veya "KAPALI"
SON

FONKSIYON oku_pencere_sensoru()
    DÖN "AÇIK" veya "KAPALI"
SON

FONKSIYON oku_kamera_sensoru()
    DÖN "AKTİF" veya "PASİF"
SON

FONKSIYON ev_sahibi_evde_mi()
    DÖN TRUE veya FALSE
SON

FONKSIYON gonder_bildirimler(seviye)
    GÖNDER SMS, UYGULAMA, E-POSTA
SON
