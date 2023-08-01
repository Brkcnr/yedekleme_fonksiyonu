# yedekleme_fonksiyonu
Linux işletim sisteminde yazılmış bir yedekleme fonksiyonu 

ADIM 1
````
// Öncelikle 'sudo nano' komutu ile oluşturmak istediğimiz dosyanın ismini girip belirlediğimiz konumda shell script oluşturun
# sudo nano /home/kali/siber-sh/script.sh 
// Ardından script.sh dosyasının içine yedekleme fonksiyonunu yapacak kodu yazın:

#!/bin/bash
 
# Yedek almak için fonksiyon
backup_files() {
    local source_dir="$1"
    local backup_dir="$2"
    local current_time=$(date +"%Y%m%d%H")
    local backup_filename="${current_time}_backup.tar.gz"

    tar -czf "${backup_dir}/${backup_filename}" -C "${source_dir}" .
    echo "Yedekleme tamamlandı: ${backup_dir}/${backup_filename}"
}

# Saat başı yedeklemeyi kontrol eder
while true; do
    # Şuanki dakika bilgisini al
    current_minute=$(date +"%M")

    # Eğer saat başı ise yedekleme işlemini gerçekleştir
    if [[ "$current_minute" == "00" ]]; then
        # Yedekleme işlemini gerçekleştir
        source_directory="/path/to/your/scripts"  # Scriptlerin olduğu dizin
        backup_directory="/path/to/your/backup"  # Yedeklerin kaydedileceği dizin
        backup_files "$source_directory" "$backup_directory"
else
    echo "Henüz saat başı değil."
fi
done

// Kodu yazdıktan sonra script'i çalıştırılabilir yapmak için 'sudo chmod +x /home/kali/siber-sh/script.sh' komutunu yazın
````
ADIM 2
````
// Ardından, sisteminizdeki systemd için bir hizmet birimi dosyası oluşturun. Bu dosya .service uzantısına sahip olmalı ve /lib/systemd/system/ dizini altına kaydedilmelidir.
# sudo nano /lib/systemd/system/shellscript.service 
// Şimdi aşağıdaki içeriği ekleyin ve shell script adını ve konumunu güncelleyin.

[Unit]
Description=My Shell Script

[Service]
ExecStart=/usr/bin/script.sh

[Install]
WantedBy=multi-user.target

// Dosyayı kaydedip kapatın.
````
ADIM 3
````
// Yeni servisi etkinleştirin
// Yeni dosyayı okumak için systemctl arka plan programını yeniden yükleyin. .service dosyasında herhangi bir değişiklik yaptıktan sonra bu arka plan programını her seferinde yeniden yüklemeniz gerekir.
# sudo systemctl daemon-reload 
// Şimdi hizmetin sistem önyüklemesinde başlamasını sağlayın, ayrıca aşağıdaki komutları kullanarak hizmeti başlatın.
# sudo systemctl enable shellscript.service 
# sudo systemctl start shellscript.service 
// Son olarak komut dosyasının çalışır durumda olduğunu ve bir systemd hizmeti olarak çalıştığını doğrulayın.
# sudo systemctl status shellscript.service 

// Çıktı aşağıdaki gibi görünmeli
shellscript.service - My Shell Script
     Loaded: loaded (/lib/systemd/system/shellscript.service; enabled; preset: disabled)
     Active: active (running) since Tue 2023-08-01 10:41:18 +03; 9min ago
   Main PID: 23849 (script.sh)
      Tasks: 2 (limit: 2255)
     Memory: 588.0K
        CPU: 138ms
     CGroup: /system.slice/shellscript.service
             ├─23849 /bin/bash /home/kali/siber-sh/script.sh
             └─28664 sleep 10

Aug 01 10:49:28 kali script.sh[27908]: /home/kali/siber-sh/script.sh: line 5: //: Is a directory
Aug 01 10:49:38 kali script.sh[27990]: /home/kali/siber-sh/script.sh: line 5: //: Is a directory
Aug 01 10:49:48 kali script.sh[28076]: /home/kali/siber-sh/script.sh: line 5: //: Is a directory
Aug 01 10:49:58 kali script.sh[28172]: /home/kali/siber-sh/script.sh: line 5: //: Is a directory
Aug 01 10:50:08 kali script.sh[28256]: /home/kali/siber-sh/script.sh: line 5: //: Is a directory
Aug 01 10:50:18 kali script.sh[28339]: /home/kali/siber-sh/script.sh: line 5: //: Is a directory
Aug 01 10:50:28 kali script.sh[28424]: /home/kali/siber-sh/script.sh: line 5: //: Is a directory
Aug 01 10:50:38 kali script.sh[28498]: /home/kali/siber-sh/script.sh: line 5: //: Is a directory
Aug 01 10:50:48 kali script.sh[28580]: /home/kali/siber-sh/script.sh: line 5: //: Is a directory
Aug 01 10:50:58 kali script.sh[28663]: /home/kali/siber-sh/script.sh: line 5: //: Is a directory

// Shell script'i sistem başlangıcında çalışacak şekilde ayarlamış oldun.
````
