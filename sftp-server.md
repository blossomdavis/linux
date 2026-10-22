# Konfigurera en dedikerad och isolerad SFTP-server (Ubuntu VM)

Mål: Förstå hur man skapar en säker, isolerad fildelningsmiljö där användare kan ladda upp/ned filer via en krypterad förbindelse utan att få tillgång till systemets SSH-skal (shell) eller övriga delar av filsystemet.
Huvudmoment och instruktioner

    Säkerställ installation av SSH-tjänsten

        Verifiera att OpenSSH-servern är installerad och körs på din Ubuntu VM.

    Användar- & Grupphantering

        Skapa en dedikerad användargrupp (t.ex. sftpgroup).

        Skapa en ny SFTP-användare, koppla denne till sftpgroup och spärra shell-åtkomst genom att sätta skalet till /usr/sbin/nologin.

    Konfigurera Chroot-behörigheter (Kritiskt steg)

        Konfigurera filsystemet så att root äger den översta mappen för Chroot (t.ex. /var/sftp) med rättigheterna 755.

        Skapa en underkatalog (t.ex. /var/sftp/uploads) som ägs av din SFTP-användare/grupp där faktiska filer får laddas upp.

    SSHD-konfiguration (/etc/ssh/sshd_config)

        Lägg till ett regelblock längst ned i konfigurationsfilen:
        Plaintext

        Match Group sftpgroup
            ChrootDirectory /var/sftp
            ForceCommand internal-sftp
            AllowTcpForwarding no
            X11Forwarding no

        Starta om SSH-tjänsten (sudo systemctl restart ssh).

    Testning och verifiering

        Testa att ansluta med SSH från en annan klient eller terminal: ssh användare@server_ip (ska nekas / stängas av).

        Anslut via SFTP i terminalen (sftp användare@server_ip) eller med FileZilla.

        Verifiera att du kan ladda upp filer i mappen uploads/, men att du inte kan navigera ovanför din tilldelade root-katalog (..).
