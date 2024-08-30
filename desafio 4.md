#!/bin/bash

# Detectar la distribución de Linux
detect_distro() {
    if [ -f /etc/os-release ]; then
        . /etc/os-release
        DISTRO=$ID
    elif [ -f /etc/lsb-release ]; then
        . /etc/lsb-release
        DISTRO=$DISTRIB_ID
    elif [ -f /etc/debian_version ]; then
        DISTRO="debian"
    elif [ -f /etc/redhat-release ]; then
        DISTRO=$(cat /etc/redhat-release | awk '{print tolower($1)}')
    else
        echo "Distribucion no soportada."
        exit 1
    fi
}

# Identifica el gestor de paquetes, realiza update, upgrade e instala apache
manage_packages() {
    case $DISTRO in
        ubuntu|debian)
            PKG_MANAGER="apt-get"
            sudo $PKG_MANAGER update -y
            sudo $PKG_MANAGER upgrade -y
            sudo $PKG_MANAGER install apache2 -y
            ;;
        fedora)
            PKG_MANAGER="dnf"
            sudo $PKG_MANAGER upgrade --refresh -y
            sudo $PKG_MANAGER install httpd -y
            ;;
        centos|rhel)
            PKG_MANAGER="yum"
            sudo $PKG_MANAGER update -y
            sudo $PKG_MANAGER upgrade -y
            sudo $PKG_MANAGER install httpd -y
            ;;
        arch)
            PKG_MANAGER="pacman"
            sudo $PKG_MANAGER -Syu --noconfirm
            sudo $PKG_MANAGER -S apache --noconfirm
            ;;
        *)
            echo "Distribución no soportada."
            exit 1
            ;;
    esac
}

# Iniciar el servicio de Apache
start_apache() {
    case $DISTRO in
        ubuntu|debian|arch)
            sudo systemctl start apache2
            sudo systemctl enable apache2
            ;;
        fedora|centos|rhel)
            sudo systemctl start httpd
            sudo systemctl enable httpd
            ;;
    esac
}

# Modificar el archivo index.html con un mensaje personalizado
modify_index_html() {
    case $DISTRO in
        ubuntu|debian|arch)
            echo    "<!DOCTYPE html>
                    <html lang="en">
                    <head>
                        <meta charset="UTF-8">
                         <meta name="viewport" content="width=device-width, initial-scale=1.0">
                            <title>Botcamp DevOps</title>
                    </head>
                    <body>
                        <h1>Bootcamp DevOps Engineer</h1>
                    </body>
                    </html>" | sudo tee /var/www/html/index.html
            ;;
        fedora|centos|rhel)
            echo "<!DOCTYPE html>
                    <html lang="en">
                    <head>
                        <meta charset="UTF-8">
                         <meta name="viewport" content="width=device-width, initial-scale=1.0">
                            <title>Botcamp DevOps</title>
                    </head>
                    <body>
                        <h1>Bootcamp DevOps Engineer</h1>
                    </body>
                    </html>" | sudo tee /var/www/html/index.html
            ;;
    esac
}

# Ejecuta las funciones
detect_distro
manage_packages
start_apache
modify_index_html

echo "Instalación y configuración completa. Apache está ejecutándose."
