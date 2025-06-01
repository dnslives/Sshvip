#!/bin/bash
claro
#--------------------------
# SCRIPt GUSTAVO VPN
# CANAL TELEGRAM: @scottssh
#--------------------------

# - Núcleos
VERMELHO='\033[1;31m'
AMARELO='\033[1;33m'
SCOLOR='\033[0m'

# - Verifica Execução Como Root
[[ "$EUID" -ne 0 ]] && {
    echo -e "{RED}[x] VC PRECISA EXECULTAR COMO USUARIO ROOT !{SCOLOR}"
    saída 1
}

# - Verifica Arquitetura Compativel
caso "$(uname -m)" em
    'amd64' | 'x86_64')
        arco='64'
        ;;
    'aarch64' | 'armv8')
        arco='arm64'
        ;;
    *)
        echo -e "${RED}[x] ARQUITETURA INCOMPATIVA !${SCOLOR}"
        saída 1
        ;;
esac

# - Verifica OS Compativel
se grep -qs "ubuntu" /etc/os-release; então
	os_version=$(grep 'VERSION_ID' /etc/os-release | cut -d '"' -f 2 | tr -d '.')
    [[ "$os_version" -lt 1804 ]] && {
        echo -e "${RED}[x] VERSÃO DO UBUNTU INCOMPATIVEL !\n${YELLOW}[!] REQUER UBUNTU 18.04 OU SUPERIOR !${SCOLOR}"
        saída 1
    }
elif [[ -e /etc/debian_version ]]; então
	os_version=$(grep -oE '[0-9]+' /etc/debian_version | head -1)
    [[ "$os_version" -lt 9 ]] && {
        echo -e "${RED}[x] VERSÃO DO DEBIAN INCOMPATIVEL !\n${YELLOW}[!] REQUER DEBIAN 9 OU SUPERIOR !${SCOLOR}"
        saída 1
    }
outro
    echo -e "${RED}[x] OS INCOMPATIVEL !\n${YELLOW}[!] REQUER DISTROS BASE DEBIAN/UBUNTU !${SCOLOR}"
    saída 1
fi

# - Atualiza Lista/Pacotes/Sistema
dpkg --configure -a
apt update -y && apt upgrade -y
apt install unzip python3 -y

# - Desabilita ipv6
sysctl -w net.ipv6.conf.all.disable_ipv6=1 && sysctl -p
eco 'net.ipv6.conf.all.disable_ipv6 = 1' > /etc/sysctl.d/70-disable-ipv6.conf
sysctl -p -f /etc/sysctl.d/70-disable-ipv6.conf

# - Executar instalador
[[ -e Mais ]] && rm Mais
wget raw.githubusercontent.com/gustavo3419/SSHPLUS/main/script/${arch}/Plus && chmod 777 Plus && ./Plus
