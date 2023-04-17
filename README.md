# Hyper-V + Ubuntu 22.04 で拡張セッションを使えるようにするまで

## まずは Ubuntu 側で色々やる

1. パッケージの追加
    ```
    apt install -y linux-tools-virtual
    apt install -y linux-cloud-tools-virtual
    apt install -y xrdp xorgxrdp
    ```

1. 一度 xrdp 関連のデーモンを停める
    ```
    systemctl stop xrdp
    systemctl stop xrdp-sesman
    ```

1. /etc/xrdp/xrdp.ini を書き換える  
    [xrdp.ini.patch を参照](./etc/xrdp/xrdp.ini.patch)

1. /etc/xrdp/startubuntu.sh を用意  
    [startubuntu.sh を参照](./etc/xrdp/startubuntu.sh)
    ```
    chmod +x /etc/xrdp/startubuntu.sh
    ```

1. /etc/xrdp/sesman.ini を書き換え  
    [sesman.ini.patch を参照](./etc/xrdp/sesman.ini.patch)

1. /etc/X11/Xwrapper.config を書き換え  
    [Xwrapper.config.patch を参照](./etc/X11/Xwrapper.config.patch)

1. /etc/modprobe.d に blacklist-vmw-vsock-vmci-transport.conf を作る  
    [blacklist-vmw-vsock.vmci-transport.conf を参照](./etc/modprobe.d/blacklist-vmw-vsock-vmci-transport.conf)

1. /etc/modules-load.d に hyperv.conf を作る  
    [hyerpv.conf を参照](./etc/modules-load.d/hyperv.conf)

1. /etc/polkit-1/localauthority/50-local.d に 45-allow-colord.pkla を作る  
    [45-allow-colord.pkla を参照](./etc/polkit-1/localauthority/50-local.d/45-allow-colord.pkla)

1. /etc/polkit-1/localauthority/50-local.d に46-allow-update-repo.pkla を作る  
    [46-allow-update-repo.pkla を参照](./etc/polkit-1/localauthority/50-local.d/46-allow-update-repo.pkla)

1. デーモン情報の再読み込みと xrdp の起動
    ```
    systemctl daemon-reload
    systemctl start xrdp
    ```

## Windows 側で管理者権限の PowerShell を使用して設定
1. 対象の仮想マシンに対して、拡張セッションを有効化
    ```
    Set-VM -VMName "Your VM Name" -EnhancedSessionTransportType HvSocket
    ```